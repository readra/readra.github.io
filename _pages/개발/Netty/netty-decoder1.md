---
title: "패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)"
date: "2025-03-24"
thumbnail: "/assets/img/thumbnail/netty.png"
---

# 패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)
---

Netty 는 비동기 이벤트 기반 프레임워크다. 이런 Netty 를 활용한 TCP 서버 개발 당시, 클라이언트의 다중 send() 요청을 하나의 Transaction 으로 간주하여 처리해야 했다.   
또한, 다중 send()가 아니더라도 TCP 통신 특성 상, 여러 개로 단편화된 패킷이 수신될 경우를 대비하여 Netty Decoder 를 적용한 사례를 정리한 글이다.

1. 클라이언트는 서버에서 메시지의 총 길이와 헤더 정보가 담긴 구조체를 send 하고, 바로 이어서 메시지의 Body 내용이 담긴 구조체를 send한다. 
2. 클라이언트가 보내는 두 번의 send 과정 중, 데이터의 크기나 프로토콜 특성으로 인해 패킷이 분할되어 수신될 수 있다.