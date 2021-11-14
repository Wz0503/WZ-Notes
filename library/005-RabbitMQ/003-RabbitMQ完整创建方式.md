#### 通过代码进行完整创建：  
#### 生产者  
```
public class Producer {
    public static void main(String[] args) {
        //1、创建连接工厂并设置属性
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("127.0.0.1");
        connectionFactory.setPort(5672);
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");
        connectionFactory.setVirtualHost("/");

        //2、创建连接Connection
        Connection connection = null;
        Channel channel = null;

        try {
            //3、从连接工厂中获取连接
            connection = connectionFactory.newConnection("生产者");

            //4、从连接中获取通道Channel
            channel = connection.createChannel();

            //5、准备发送消息的内容
            String message = "hello world, hahaha";

            //6、准备交换机
            String exchangeName = "direct_message_exchange";

            //定义路由key
            String routeKey = "";
            //指定交换机的类型
            String type = "direct";

            //声明交换机
            //所谓的持久化指交换机会不会随着服务器重启造成丢失，如果是true代表不丢失，false代表重启会丢失
            channel.exchangeDeclare(exchangeName, type, true);

            //声明队列
            channel.queueDeclare("queue5", true, false, false, null);
            channel.queueDeclare("queue6", true, false, false, null);
            channel.queueDeclare("queue7", true, false, false, null);

            //绑定交换机和队列的关系
            channel.queueBind("queue5", exchangeName, "order");
            channel.queueBind("queue6", exchangeName, "order");
            channel.queueBind("queue7", exchangeName, "course");

            //7、发送消息给中间件rabbitmq-server
            //各参数：交换机exchange、队列名称/routingkey、属性配置、发送消息的内容
            channel.basicPublish(exchangeName, "order", null, message.getBytes());
            System.out.println("消息发送成功！");
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("发送消息异常！");
        } finally {
            // 释放连接关闭通道
            if (channel != null && channel.isOpen()){
                try{
                    channel.close();
                } catch (Exception ex){
                    ex.printStackTrace();
                }
            }
        }
    }
}
```

#### 消费者  
```
public class Consumer {

    private static Runnable runnable = new Runnable() {
        public void run() {
            // 创建连接工厂
            ConnectionFactory connectionFactory = new ConnectionFactory();
            // 设置连接属性
            connectionFactory.setHost("127.0.0.1");
            connectionFactory.setPort(5672);
            connectionFactory.setVirtualHost("/");
            connectionFactory.setUsername("guest");
            connectionFactory.setPassword("guest");

            // 获取队列的名称
            final String queueName = Thread.currentThread().getName();
            Connection connection = null;
            Channel channel = null;

            try{
                // 从连接工厂中获取连接
                connection = connectionFactory.newConnection("生产者");
                // 从连接中获取通道channel
                channel = connection.createChannel();

                String message = "hello world1";
                String exchangeName = "fanout-exchange";
                String routeKey = "";
                String type = "fanout";

                Channel finalChannel = channel;
                finalChannel.basicConsume(queueName, true, new DeliverCallback() {
                    public void handle(String s, Delivery delivery) throws IOException {
                        System.out.println(delivery.getEnvelope().getDeliveryTag());
                        System.out.println(queueName + "：收到的消息是：" + new String(
                                delivery.getBody(), "UTF-8"));
                    }
                }, new CancelCallback() {
                    public void handle(String s) throws IOException {

                    }
                });

                System.out.println(queueName + ": 开始接受消息");
                System.in.read();
            } catch (Exception e){
                e.printStackTrace();
                System.out.println("发送消息出现异常。。。");
            } finally {
                // 释放连接关闭通道
                if (channel != null && channel.isOpen()){
                    try{
                        channel.close();
                    } catch (Exception e){
                        e.printStackTrace();
                    }
                }
                if (connection != null && connection.isOpen()){
                    try {
                        connection.close();
                    } catch (Exception e){
                        e.printStackTrace();
                    }
                }
            }
        }
    };

    public static void main(String[] args) {
        //启动三个线程去执行
        new Thread(runnable, "queue5").start();
        new Thread(runnable, "queue6").start();
        new Thread(runnable, "queue7").start();
    }
}
```
