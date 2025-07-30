---
title: Netty
tags:
  - Java
  - Spring
  - Netty
categories:
  - Java
  - Netty
cover: https://image.runtimes.cc/202404051531898.png
abbrlink: 15056
date: 2023-12-10 19:03:58
updated: 2023-12-10 19:04:00
---



Server

```java
// 把 NettyServer 的创建交给 Spring 管理
@Component
public class NettyServer {

    private Logger logger = LoggerFactory.getLogger(getClass());

    @Value("${netty.port}")
    private Integer port;

    @Resource
    private NettyServerHandlerInitializer nettyServerHandlerInitializer;

    // boss 线程组，用于服务端接受客户端的连接
    private EventLoopGroup bossGroup = new NioEventLoopGroup();
  
    // worker 线程组，用于服务端接受客户端的数据读写
    private EventLoopGroup workerGroup = new NioEventLoopGroup();
  
    // Netty Server Channel
    private Channel channel;

    // 启动 Netty Server
    @PostConstruct
    public void start() throws InterruptedException {
        // 创建 ServerBootstrap 对象，用于 Netty Server 启动
        ServerBootstrap bootstrap = new ServerBootstrap();
        // 设置 ServerBootstrap 的各种属性
      	// 设置两个 EventLoopGroup 对象
        bootstrap.group(bossGroup, workerGroup)
          			// 指定 Channel 为服务端 NioServerSocketChannel
                .channel(NioServerSocketChannel.class)
          			// 设置 Netty Server 的端口
                .localAddress(new InetSocketAddress(port)) 
          			// 服务端 accept 队列的大小
                .option(ChannelOption.SO_BACKLOG, 1024) 
          			// TCP Keepalive 机制，实现 TCP 层级的心跳保活功能
                .childOption(ChannelOption.SO_KEEPALIVE, true) 
          			// 允许较小的数据包的发送，降低延迟
                .childOption(ChannelOption.TCP_NODELAY, true)
          			// 处理器
                .childHandler(nettyServerHandlerInitializer);
        // 绑定端口，并同步等待成功，即启动服务端
        ChannelFuture future = bootstrap.bind().sync();
        if (future.isSuccess()) {
            channel = future.channel();
            logger.info("[start][Netty Server 启动在 {} 端口]", port);
        }
    }

    // 关闭 Netty Server
    @PreDestroy
    public void shutdown() {
        // 关闭 Netty Server
        if (channel != null) {
            channel.close();
        }
        // <3.2> 优雅关闭两个 EventLoopGroup 对象
        bossGroup.shutdownGracefully();
        workerGroup.shutdownGracefully();
    }

}
```

NettyServerHandlerInitializer

```java
@Component
public class NettyServerHandlerInitializer extends ChannelInitializer<Channel> {

    /**
     * 心跳超时时间
     */
    private static final Integer READ_TIMEOUT_SECONDS = 3 * 60;
  
    @Resource
    private NettyServerHandler nettyServerHandler;

    @Override
    protected void initChannel(Channel ch) {
        // 获得 Channel 对应的 ChannelPipeline
        ChannelPipeline channelPipeline = ch.pipeline();
        // 添加一堆 NettyServerHandler 到 ChannelPipeline 中
        channelPipeline
                // 空闲检测
                .addLast(new ReadTimeoutHandler(READ_TIMEOUT_SECONDS, TimeUnit.SECONDS))
                // 编码器
                .addLast(new InvocationEncoder())
                // 解码器
                .addLast(new InvocationDecoder())
                // 消息分发器
                .addLast(messageDispatcher)
                // 服务端处理器
                .addLast(nettyServerHandler)
        ;
    }

}
```

## 模拟启动Netty

```java
ChannelFuture future = bootstrap.bind(port).sync();
System.out.println("Netty 代理服务器启动，监听端口：" + port);
future.channel().closeFuture().sync(); // 阻塞直到关闭
```

```java
public class NettyTest {
    public static void main(String[] args) throws InterruptedException {
        RiceCooker cooker = new RiceCooker();

        // 模拟 bootstrap.bind().sync()
        cooker.start();  // 启动（阻塞直到准备好）

        // 模拟 future.channel().closeFuture().sync()
        new Thread(() -> {
            try {
                Thread.sleep(5000); // 模拟5秒后煮好饭
                cooker.close();     // 自动关闭
            } catch (InterruptedException ignored) {}
        }).start();

        cooker.waitUntilClosed(); // 阻塞，直到关闭
    }
}


class RiceCooker {
    boolean isStarted = false;
    boolean isClosed = false;

    // 启动电饭煲
    public void start() throws InterruptedException {
        System.out.println("启动电饭煲...");
        Thread.sleep(1000); // 模拟准备时间
        isStarted = true;
        System.out.println("电饭煲开始煮饭！");
    }

    // 等待“煮饭完成”
    public void waitUntilClosed() throws InterruptedException {
        while (!isClosed) {
            Thread.sleep(500); // 等待状态变化
        }
        System.out.println("煮饭完成，电饭煲关闭。");
    }

    public void close() {
        isClosed = true;
    }
}
```

## 粘包

### 模拟粘包问题

```java
public class NettyServer {
    public static void main(String[] args) throws Exception {
        EventLoopGroup boss = new NioEventLoopGroup();
        EventLoopGroup worker = new NioEventLoopGroup();

        ServerBootstrap b = new ServerBootstrap();
        b.group(boss, worker)
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<SocketChannel>() {
                    @Override
                    protected void initChannel(SocketChannel ch) {
                        ChannelPipeline pipeline = ch.pipeline();
                        // 👇 没有拆包处理器，粘包将出现！
                        pipeline.addLast(new ServerHandler());
                    }
                });

        b.bind(8080).sync();
        System.out.println("Server started on port 8080");
    }
}

class ServerHandler extends SimpleChannelInboundHandler<ByteBuf> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) {
        String received = msg.toString(CharsetUtil.UTF_8);
        System.out.println("数据长度：" + msg.readableBytes());
        System.out.println("原始数据: [" + received + "]");
    }
}
```

```java
public class NettyClient {
    public static void main(String[] args) throws Exception {
        EventLoopGroup group = new NioEventLoopGroup();

        Bootstrap bootstrap = new Bootstrap();
        bootstrap.group(group)
                .channel(NioSocketChannel.class)
                .handler(new ChannelInitializer<SocketChannel>() {
                    @Override
                    protected void initChannel(SocketChannel ch) {
                        ch.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelActive(ChannelHandlerContext ctx) {
                                // 连续发多条消息，粘包一定会出现
                                for (int i = 0; i < 5; i++) {
                                    String msg = "msg" + i + "\n";
                                    ctx.writeAndFlush(Unpooled.copiedBuffer(msg, CharsetUtil.UTF_8));
                                }
                            }
                        });
                    }
                });

        bootstrap.connect("127.0.0.1", 8080).sync();
    }
}
```

服务端打印

```shell
Server started on port 8080
数据长度：25
原始数据: [msg0
msg1
msg2
msg3
msg4
]
```

这里错误情况,正常情况应该是

```shell
原始数据:[msg0]
原始数据:[msg1]
原始数据:[msg2]
.....
```

### 方案1:换行符或自定义分隔符

在服务端配置

```java
// 按 \n 拆
new LineBasedFrameDecoder(1024); 
// 按 "--" 拆
new DelimiterBasedFrameDecoder(1024, Unpooled.wrappedBuffer("--".getBytes())); 
```

```java
public class NettyServer {
    public static void main(String[] args) throws Exception {
        EventLoopGroup boss = new NioEventLoopGroup();
        EventLoopGroup worker = new NioEventLoopGroup();

        ServerBootstrap b = new ServerBootstrap();
        b.group(boss, worker)
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<SocketChannel>() {
                    @Override
                    protected void initChannel(SocketChannel ch) {
                        ChannelPipeline pipeline = ch.pipeline();
                        pipeline.addLast(new LineBasedFrameDecoder(1024));
//                        pipeline.addLast(new DelimiterBasedFrameDecoder(1024, Unpooled.wrappedBuffer("--".getBytes())));
                        // 👇 没有拆包处理器，粘包将出现！
                        pipeline.addLast(new ServerHandler());
                    }
                });

        b.bind(8080).sync();
        System.out.println("Server started on port 8080");
    }
}

class ServerHandler extends SimpleChannelInboundHandler<ByteBuf> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) {
        String received = msg.toString(CharsetUtil.UTF_8);
        System.out.println("数据长度：" + msg.readableBytes());
        System.out.println("原始数据: [" + received + "]");
    }
}
```

控制台打印,这是正常的

```shell
Server started on port 8080
数据长度：4
原始数据: [msg0]
数据长度：4
原始数据: [msg1]
数据长度：4
原始数据: [msg2]
数据长度：4
原始数据: [msg3]
数据长度：4
原始数据: [msg4]
```

### 方案2:固定长度

```java
// 客户端每条消息必须正好 8 字节，否则解析错。
pipeline.addLast(new FixedLengthFrameDecoder(8));
```

### 方案三：LengthFieldBasedFrameDecoder

