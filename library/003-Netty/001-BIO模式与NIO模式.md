#### 传统BIO模式：每次连接都占用一个线程
---
    ServerSocket server = new ServerSocket(9090, 20);

    System.out.println("step1: new ServerSocket(9090)");

    //死循环
    while (true){
      Socket client = server.accept(); // 阻塞1：等待客户端连接，没有客户端连接的情况下将一直等待
      System.out.println("step2:client\t" + client.getPort());

      new Thread(() -> {
        InputStream in = null;
        try{
          in = client.getInputStream();
          BufferedReader reader = new BufferedReader(new InputStreamReader(in));
          while (true){
            String dataline = reader.readLine(); //阻塞2：建立一个新的进程防止在该客户端没有输入时不影响其它的客户端连接

            if (null != dataline){
              System.out.println(dataline);
            } else {
              client.close();
              break;
            }
          }
          System.out.println("客户端断开");
        } catch (IOException e){
          e.printStackTrace();
        }
        }).start();

    }
BIO模式：每连接占用一个线程，cpu单位时间内，每一个线程轮一下，除了要跑进程的逻辑，还要有一系列其它的内核操作，这样比较浪费cpu的资源（因为进程线程的数量较多）。  
#### BIO弊端: 关键问题在于阻塞。采用NIO(非阻塞)的方式如下：  
---
    public static void main(String[] args) throws Exception {  
      LinkedList<SocketChannel> clients = new LinkedList<>();

      // 用于监听客户端的连接，它是所有客户端连接的父管道
      ServerSocketChannel ss = ServerSocketChannel.open();
      // 绑定监听端口，设置连接为非阻塞模式
      ss.bind(new InetSocketAddress(9090));
      ss.configureBlocking(false);

      while (true){
        // 接受客户端的连接
        Thread.sleep(1000);
        SocketChannel client = ss.accept();
        //accept 调用内核了：1.如果没有客户端连接进来，在BIO的时候一直卡着，但是在NIO，不卡着，返回-1
        //如果来客户端的连接，accept返回的是这个客户端的fd
        //非阻塞简单来说就是代码能往下走了，只不过有不同的情况

        if (client == null){
          ...
        } else {
          client.configureBlocking(false);
          int port = client.socket().getPort();
          System.out.println("client..port: " + port);
          clients.add(client);
        }

        // 通过缓冲区进行读写
        ByteBuffer buffer = ByteBuffer.allocateDirect(4096);

        // 遍历已经连接进来的客户端能不能读写数据
        for (SocketChannel c: clients){
          int num = c.read(buffer);
          if (num > 0){
            buffer.flip();
            byte[] aaa = new byte[buffer.limit()];
            buffer.get(aaa);

            String b = new String(aaa);
            System.out.println(c.socket().getPort() + ":" + b);
            buffer.clear();
          }
        }
      }

    }
采取这样的方式减少了cpu资源的浪费。但是也存在弊端。  
#### 弊端：
假设有一万个client连接，系统调用的复杂度为O(n)，可能有效的调用很少，可能很多的系统调用浪费了；  
