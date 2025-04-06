---
title: "패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (2)"
date: "2025-03-31"
thumbnail: "/assets/img/thumbnail/netty.png"
---

# 패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (2)
---

## 2. 패킷 유실 상황 대비 Drop 처리

```java
public class AccessAuthorityTimeoutHandler extends ChannelDuplexHandler {
   @Override
   public void userEventTriggered(ChannelHandlerContext ctx, Object evt) {
       if ( evt instanceof IdleStateEvent) {
           IdleStateEvent event = (IdleStateEvent) evt;


           if ( IdleState.READER_IDLE == event.state() ) {
               log.info("Idle session closed. [ID]" + ctx.channel().id().asShortText());
               ctx.close();
           }
       }
   }
}

```