---
title: "패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)"
date: "2025-03-24"
thumbnail: "/assets/img/thumbnail/netty.png"
---

# 패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)
---

Netty 는 비동기 이벤트 기반 프레임워크다. 이런 Netty 를 활용한 TCP 서버 개발 당시, 클라이언트의 다중 send() 요청을 하나의 Transaction 으로 간주하여 처리해야 했다.   
또한, 다중 send()가 아니더라도 TCP 통신 특성 상, 여러 개로 단편화된 패킷이 수신될 경우를 대비하여 Netty Decoder 를 적용한 사례를 정리한 글이다.

1. 클라이언트는 서버에서 메시지의 총 길이와 헤더 정보가 담긴 구조체를 send 하고, 바로 이어서 메시지의 Body 내용이 담긴 구조체를 send 한다. 
2. 클라이언트가 보내는 두 번의 send 과정 중, 데이터의 크기나 프로토콜 특성으로 인해 패킷이 분할되어 수신될 수 있다.

위의 2가지 상황을 고려하여 아래와 같이 보완 처리했다.

1. 메시지의 총 길이와 헤더 정보 구조체에서 메시지의 총 길이를 추출하여 그 길이만큼 패킷을 계속 누적하여 수집
2. 패킷이 유실될 가능성을 염두하여 특정 Timeout 만큼 대기하고 이후에는 패킷을 Drop 처리

## 1. 메시지의 총 길이만큼 패킷 누적

```java
public class MultiSendDecoder extends ByteToMessageDecoder {
	@Override
	protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) {
		// 최소 4바이트 수신 여부 확인
		if (4 > in.readableBytes()) {
			return;
		}

		// 프로토콜 코드 peek
		int code = in.getIntLE(in.readerIndex());

		switch (code) {
			case MultiSendConstants.OLD_PROTOCOL -> { // 구 프로토콜은 패킷 사이즈가 고정 (100 바이트 고정)
				// 전체 데이터 수신시까지 누적
				if ( 100 == in.readableBytes() ) {
					out.add(in.readBytes(100));
				} else {
					log.debug("[RECEIVED SIZE]{}", in.readableBytes());
				}
			}
			case MultiSendConstants.NEW_PROTOCOL_CODE -> out.add(in.readBytes(4)); // Multi Send 첫 번째 (프로토콜 코드값만 read)
			case MultiSendConstants.NEW_PROTOCOL_DATA -> { // Multi Send 두 번째
				// 패킷 구조 (message code:4 + message length:4 + message:N)
				// message length 까지 수신된 경우
				if ( 8 <= in.readableBytes() ) {
					// message length peek (message code 4바이트만큼 건너뛴 offset 부터 읽기)
					int messageSize = in.getIntLE(4 + in.readerIndex());
					// message length + message 까지 전부 수신된 경우
					if ((4 + messageSize) == in.readableBytes()) { 
						out.add(in.readBytes(4 + messageSize)); // message length:4 + message:N 만큼 데이터를 Netty Handler 에 전달
					} else {
						log.debug("[RECEIVED SIZE]{}, [MESSAGE SIZE]{}", in.readableBytes(), messageSize);
					}
				}
			}
			default -> {
				log.warn("Unsupported protocol version. [CODE]{}", code);
				// 미지원 프로토콜 요청 패킷 SKIP 처리
				in.skipBytes(in.readableBytes());
				ctx.close().addListener(ChannelFutureListener.CLOSE);
			}
		}
	}
}
```
### 코드 설명
1. MultiSendConstants.OLD_PROTOCOL : 옛날 프로토콜로 객체의 데이터 길이가 제한되어 있고, 패킷에 데이터를 4바이트 단위로 남는 바이트는 0으로 채운(padding) 형태로 marshalling 하여 패킷 전송
2. MultiSendConstants.NEW_PROTOCOL_CODE : 신규 프로토콜로 통신해야 할 프로토콜 코드를 먼저 전달
3. MultiSendConstants.NEW_PROTOCOL_DATA : (2)의 프로토콜 코드 전달 후, 바로 이어서 DATA 패킷 send() 하며, DATA 패킷은 메시지 코드와 이어질 데이터 부분의 전체 길이를 포함한 형태로 패킷 전송