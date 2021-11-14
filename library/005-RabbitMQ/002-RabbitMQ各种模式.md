##### 安装RabbitMQ需要依赖Erlang
##### 所有的中间件技术都是基于tcp/ip协议基础之上构建的协议规范，只不过rabbitmq遵循的是AMQP协议

##### 主要有以下几种模式：
* 简单模式  
![](assets/RabbitMQ/000-简单模式.png=300-50)  
简单模式主要指由生产者生产消息，然后全部放入到一个队列中，由消费者依次进行消费  

* 工作模式  
![](assets/RabbitMQ/001-fanout模式.png=300-70)  
工作模式主要是指由生产者生产消息，放入到一个队列中，由多个消费者进行消费  

* 订阅者模式  
![](assets/RabbitMQ/002-订阅者模式.png=300-60)  
订阅者模式主要指生产者生产消息通过一个交换机分发到指定队列，然后由消费者进行消费
订阅者模式示例代码如下:  
  * 生产者
```
public class Producer {

    public static void main(String[] args) {

        // 所有的中间件技术都是基于TCP/IP协议基础之上构建的协议规范，只不过rabbitmq采用的是amqp协议
        // ip 端口

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
            String message = "hello world";

            //6、准备交换机
            String exchangeName = "fanout-exchange";

            //定义路由key
            String routeKey = "";
            //指定交换机的类型
            String type = "fanout";

            //7、发送消息给中间件rabbitmq-server
            //各参数：交换机exchange、队列名称/routingkey、属性配置、发送消息的内容
            channel.basicPublish(exchangeName, routeKey, null, message.getBytes());
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

  * 消费者  
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
        new Thread(runnable, "queue1").start();
        new Thread(runnable, "queue2").start();
        new Thread(runnable, "queue3").start();
    }
}
```

* routing模式  
![](assets/RabbitMQ/003-routing模式.png=300-70)  
订阅者模式主要指生产者生产消息通过一个交换机，交换机通过路由将相应的消息发送到相应的队列中，然后由消费者进行消费  
代码与订阅者模式类似，只不过routeKey不再为空  

* 主题模式  
![](assets/RabbitMQ/004-主题模式.png=300-70)  
主题模式主要指生产者生产消息通过一个交换机，交换机通过主题（模糊匹配）将相应的消息发送到相应的队列中，然后由消费者进行消费，与routing模式类似  
