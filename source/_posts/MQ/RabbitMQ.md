# 安装

使用docker安装

```shell
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_VHOST=my_vhost  -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin rabbitmq:3-management
```

## 主从架构安装

> 使用rabbitmqctl join_cluster 命令

# RabbitMQ使用

## Java client直接连接

maven依赖

```xml
<dependency>  
    <groupId>com.rabbitmq</groupId>  
    <artifactId>amqp-client</artifactId>  
    <version>5.9.0</version>  
</dependency>
```

### 发送消息

- 创建连接工厂
```java
ConnectionFactory factory = new ConnectionFactory();
```
- 配置连接工厂信息
```java  
factory.setHost("192.168.44.250");        
factory.setPort(5672);        
factory.setUsername("admin");        
factory.setPassword("admin");
```
- 获取连接
```java          
connection = factory.newConnection();
```
- 获取通道
```java
channel = connection.createChannel();
```
- 声明队列
```java
channel.queueDeclare("myQueue",true,false,false,null);
```
声明一个队列：
	1.  参数1：队列名
	2.  参数2：是否是持久化
	3.  参数3：是否排外，如果排外则这个队列只允许一个消费者监听
	4.  参数4：是否自动删除，如果没有消费者连接时，就会自动删除该队列
	5.  参数5：为队列的一些属性设置,一般为null

- 发送消息
```java
channel.basicPublish("","myQueue",null,message.getBytes(StandardCharsets.UTF_8));
```
	-   参数1：为交换机名称，空字符串表示不使用交换机
	-   参数2：为队列名称或RoutingKey，如果指定了交换机名称就是RoutingKey
	-   参数3：具体消息属性，一般为空。
	-   参数4：具体消息字节数组 channel.basicPublish("","myQueue",null,message.getBytes(StandardCharsets.UTF_8));

- 关闭资源
```java
if (channel != null) {  
    try {  
        channel.close();  
    } catch (IOException | TimeoutException e) {  
        e.printStackTrace();  
    }  
}  
if (connection != null) {  
    try {  
        connection.close();  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
}```

### 接收消息

- 创建连接工厂
同发送端

- 配置连接工厂信息
同发送端

- 获取连接
同发送端

- 获取通道
同发送端

- 声明队列
同发送端

- 接收消息
```java
channel.basicConsume("myQueue", true, "", new DefaultConsumer(channel) {  
    @Override  
    public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {  
        String message = new String(body, StandardCharsets.UTF_8);  
        System.out.println(message);  
    }  
});
```

## Spring Boot集成

maven依赖

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-amqp</artifactId>  
</dependency>
```

### 发送消息

- 配置yml
- 配置Exchange Bean
- 配置Queue Bean
- 配置Binding Bean
- 配置RabbitTemplate Bean
- 发送消息
```java
rabbitTemplate.convertAndSend("exchange", "routingKey", "message");
```

### 接收消息

- 配置RabbitListener注解

# 四种交换机类型

## Direct

处理路由键，根据routing key、exchange、queue匹配接收消息
![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202210181016908.png)


## Fanout 常用

不处理路由键，根据exchange、queue匹配接收消息
![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202210181016682.png)


## Topic

按路由键模糊匹配，通配符*、#

![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202210181018174.png)

## Header

不处理路由键，根据消息的header进行匹配

# 如何保存事务不丢失

数据不能重复消费，也不能丢失

## 容易丢失消息的场景

###  生产端丢失消息，生产端将数据发送给mq时因网络或应用突然故障，消息没有正常发送到mq

1. 可以使用rabbitmq的事务消息功能
```java
channel.txSelect();
try{
	//发送消息
	channel.txCommit();
}catch(Exception e){
	channel.txRollback(); //消息回滚
	//重新发送
}
```
缺点：生产者端会阻塞等待是否发送成功，大大降低mq性能

2. 开启异步confirm模式
```java
channel.confirm();

//发送成功回调
public void ack(String messageId){

}

//发送失败回调
public void nack(String message){

}
```
mq接收成功会回调ack方法，失败回调nack方法，一段时间后没有接收到ack消息进行重发。性能提高。

### mq接收到消息后还没被消费，mq服务故障导致数据丢失

设置消息持久化。设置有两个步骤：
1. 创建队列时设置queue持久化
2. 发送消息时指定deliveryMode=2，持久化消息，两个同时开启

写入磁盘成功才会通知生产端ack

### 消费端获取消息，没有正常消费业务异常导致的数据丢失

ack机制

ack异常会出现重复消费的问题

1. 生成唯一messageid，更新messageid对应消息的消费状态判断是否是重复消费
2. 根据具体业务的状态，例如订单状态等

## 对比kafka

kafka生产端也是使用ack回调方式保证生产端到mq消息不丢失。reties配置重发次数。
producer的acks的配置项有3个：
1. acks=0，producer不等待ack结果，broker故障无法接收到消息
2. acks=1，producer等待leader接收结果，leader接收完成返回ack，但flower还没有同步，这时leader故障
会造成数据丢失
3. acks=-1，producer等待所有leader、flower接收同步完成返回ack

At least once：至少一次，ack设置为-1实现，但数据可能重复
At most once：只会被发送一次，ack设置为0实现，但数据可能丢失

0.11之后kafka可通过porducer的enable.idompotence=true开启消息幂等性，支持exactly once
At least once + 幂等性 = Exactly once

消费端手动提交offset

# 消息堆积

## 增加consumer实例

## 配置多线程增强并发能力

- concurrency、prefetch


