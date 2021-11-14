#### poll与selector属于一类多路复用器  
#### 瑕疵:  
- 每次循环都有和内核的数据传输，解决办法：
  + 有一些fd，传给内核一次，内核保存，创建内核的一个空间
  + 把fd增加进去
  + 通过循环的方式 等待通知 哪些fd可以读写
- 内核每次都需要遍历这些fd  

#### NIO进行服务端开发的步骤：
* 创建ServerSocketChannel，并配置它为非阻塞模式；
* 绑定监听，配置TCP参数，例如backlog大小；
* 创建一个独立的I/O线程，用于轮询多路复用器Selector；
* 创建Selector，将之前创建的ServerSocketChannel注册到Selector上，监听SelectionKey.ACCEPT；
* 启动I/O线程，在循环体中执行Selector.select()方法，轮询就绪的Channel；
* 当轮询到了处于就绪状态的Channel时，需要对其进行判断，如果是OP_ACCEPT状态，说明是新的客户端接入，则调用ServerSocketChannel.accept()方法接受新的客户端；
* 设置新接入的客户端链路SocketChannel为非阻塞模式，配置其它的一些TCP参数；
* 将SocketChannel注册到Selector，监听OP_READ操作位；
* 如果轮询的Channel为OP_READ，则说明SocketChannel中有新的就绪的数据包需要读取，则构造ByteBuffer对象，读取数据包；
* 如果轮询的Channel为OP_WRITE，说明还有数据没有发送完成，需要继续发送。

#### Netty时间服务器服务端 TimeServer：
```
public class TimeServer{

  public void bind(int port) throws Exception{

    //NioEventLoopGroup是个线程组，包含了一组NIO线程，专门用于网络事件的处理
    //创建两个一个用于服务端接受客户端的连接，另一个用于进行SocketChannel的网络读写
    // 配置服务端的NIO线程组
    EventLoopGroup bossGroup = new NioEventLoopGroup();
    EventLoopGroup workerGroup = new NioEventLoopGroup();
    try{

      //Netty用于启动NIO服务端的辅助启动类，目的是降低服务端的开发复杂度
      ServerBootstrap b = new ServerBootstrap();

      b.group(bossGroup, workerGroup)
        .channel(NioServerSocketChannel.class)
        .option(ChannelOption.SO_BACKLOG, 1024)
        .childHandler(new ChildChannelHandler());

      // 绑定端口，同步等待成功
      ChannelFuture f = b.bind(port).sync();

      // 等待服务端监听端口关闭
      f.channel().closeFuture().sync();
    } finally {
      // 优雅退出，释放线程池资源
      bossGroup.shutdownGracefully();
      workerGroup.shutdownGracefully();
    }
  }

  private class ChildChannelHandler extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel arg0) throws Exception{
      arg0.pipeline().addLast(new TimeServerHandler());
    }
  }

  public static void main(String[] args) throws Exception{
    int port = 8080;
    if (args != null && args.length > 0){
      try{
        port = Integer.valueOf(args[0]);
      } catch (NumberFormatException e){
        //采用默认值
      }
    }
    new TimeServer().bind(port);
  }
}

// Netty时间服务器服务端 TimeServerHandler
public class TimeServerHandler extends ChannelHandlerAdapter{

  @Override
  public void channelRead(ChannelHnadlerContext ctx, Object msg)
    throws Exception {

      // 将msg转换成Netty的ByteBuf对象，类似于JDK中的ByteBuffer对象
      ByteBuf buf = (ByteBuf)msg;

      // 通过ByteBuf的readableBytes方法获取缓冲区可读的字节数，根据可读的字节数创建byte数组
      byte[] req = new byte[buf.readableBytes()];
      // 通过readBytes方法将缓冲区中的字节数组复制到新建的byte数组中
      buf.readBytes(req);

      String body = new String(req, "UTF-8");
      System.out.println(body);
      String currentTime = "QUERY TIME ORDER".equalsIgnoreCase(body) ? new
      java.util.Date (
        System.currentTimeMillis()).toString() : "BAD ORDER";

      ByteBuf resp = Unpooled.copiedBuffer(currentTime.getBytes());
      ctx.write(resp);
  }

  @Override
  public void channelReadComplete(ChannelHandlerContext ctx) throws
  Exception {
    ctx.flush();
  }

  @Override
  public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause){
    ctx.close();
  }
}
```
#### Netty时间服务器客户端 TimeClient
```
public class TimeClient{

  public void connect(int port, String host) throws Exception {
    //配置客户端NIO线程组
    EventLoopGroup group = new NioEventLoopGroup();

    try{
      Bootstrap b = new Bootstrap();
      b.group(group).channel(NioSocketChannel.class)
        .option(ChannelOption.TCP_NODELAY, true)
        .handler(new ChannelInitializer<SocketChannel>() {

          @Override
          public void initChannel(SocketChannel ch) throws Exception {
            ch.pipeline().addLast(new TimeClientHandler());
          }

          });

      // 发起异步连接操作
      ChannelFuture f = b.connect(host, port).sync();

      // 等待客户端链路关闭
      f.channel().closeFuture().sync();
    } finally {
      // 优雅退出，释放NIO线程组
      group.shutdownGracefully();
    }
  }

  public static void main(String[] args) throws Exception {
    int port = 8080;
    if (args != null && args.length > 0){
      try{
        port = Integer.valueOf(args[0]);
      } catch (NumberFormatException e){
        //采用默认值
      }
    }
    new TimeClient().connect(port, "127.0.0.1");
  }
}
```

Netty时间服务端器客户端 TimeClientHandler
```
public class TimeClientHandler extends ChannelHandlerAdapter {

  private static final Logger logger = Logger.getLogger(TimeClientHandler.class.getName());

  private final ByteBuf firstMessage;

  public TimeClientHandler(){
    byte[] req = "QUERY TIME ORDER".getBytes();
    firstMessage = Unpooled.buffer(req.length);
    firstMessage.writeBytes(req);
  }

  @Override
  public void channelActive(ChannelHandlerContext ctx){
    ctx.writeAndFlush(firstMessage);
  }

  @Override
  public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    ByteBuf buf = (ByteBuf)msg;
    byte[] req = new byte[buf.readableBytes()];
    buf.readBytes(req);
    String body = new String(req, "UTF-8");
    System.out.println("Now is :" + body);
  }

  @Override
  public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause){

    // 释放资源
    logger.warning(cause.getMessage());
    ctx.close();
  }
}
```
