---
title: "패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (2)"
date: "2025-03-31"
thumbnail: "/assets/img/thumbnail/netty.png"
---

# 패킷 단편화와 Multi Send 상황을 고려한 Netty Decoder 적용 (2)
---

## 2. 패킷 유실 상황 대비 Drop 처리

```java
public class NettyTimeoutHandler extends ChannelDuplexHandler {
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
### 코드 설명

```java
@Bean
public ServerBootstrap serverBootstrap() {
   ServerBootstrap serverBootstrap = new ServerBootstrap();


   serverBootstrap.group(new NioEventLoopGroup(), new NioEventLoopGroup())
           .channel(NioServerSocketChannel.class)
           .handler(new LoggingHandler(LogLevel.INFO))
           .childHandler(new ChannelInitializer<SocketChannel>() {
               @Override
               protected void initChannel(SocketChannel ch) throws Exception {
                   ch.pipeline()
                           // Read idle timeout 60초 설정
                           .addLast(new IdleStateHandler(60, 0, 0))
                           // Read idle timeout 핸들러
                           .addLast(NettyTimeoutHandler)
                           // 메시지 디코더
                           .addLast(new MultiSendDecoder())
                           // 메시지 핸들러
                           .addLast(multiSendHandler);
               }
           });


   return serverBootstrap;
}
```