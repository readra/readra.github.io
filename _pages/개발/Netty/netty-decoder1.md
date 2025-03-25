---
title: "패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)"
date: "2025-03-24"
thumbnail: "/assets/img/thumbnail/netty.png"
---

# 패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (1)
---

Netty 는 비동기 이벤트 기반 프레임워크다. 이런 Netty 를 활용한 TCP 서버 개발 당시, 클라이언트의 다중 send() 요청을 하나의 Transaction 으로 간주하여 처리해야 했다.   
또한, 다중 send()가 아니더라도 TCP 통신 특성 상, 여러 개로 단편화된 패킷이 수신될 경우를 대비하여 Netty Decoder 를 적용한 사례를 정리한 글이다.