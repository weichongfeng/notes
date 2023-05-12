# AMQP协议

### 什么是AMQP

AMQP全称：Advanced Message Queuing Protocol(高级消息队列协议)。是应用层协议的一个开发标准，为面向消息的中间件设计。

### AMQP生产者流转过程

![](./images/rabbitMQ-1.png)

### AMQP消费者流转过程

![](./images/rabbitMQ-2.png)

# RabbitMQ的角色分类

   - 超级管理员（administrator）：可登录管理控制台，可查看所有的信息，并且可以对用户，策略（policy）进行操作；
   - 监控者（monitoring）：可登录管理控制台，同时可以查看rabbitmq节点的相关信息（进程数，内存使用情况，磁盘使用情况等）；
   - 策略制定者（policymaker）：可登录管理控制台，同时可以对policy进行管理。但无法查看节点的相关信息;
   - 普通管理者（management）：仅可登陆管理控制台，无法看到节点信息，也无法对策略进行管理。
   - 其他：无法登录管理控制台，同城就是普通的生产者和消费者；

# RabbitMQ的核心概念

#### Broker
就是server，接受客户端的连接，表示消息队列服务器实体

#### Connection
和具体的Broker的网络连接。

#### Message
消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括 routing-key（路由键）、 priority（相对于其他消息的优先权）、 delivery-mode（指出该消息可能需要持久性存储）等。

#### Channel
信道， 多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的 TCP 连接内地虚拟连接， AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。客户端可以建立多个channel，每个channel表示一个会话任务。

#### Publisher
消息的生产者，也是一个向交换器发布消息的客户端应用程序。

#### Exchange（将消息路由给队列 ）
交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。

#### Binding（消息队列和交换器之间的关联）
绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，虚拟机根据他来确定如何路由 一条消息，所以可以将交换器理解成一个由绑定构成的路由表。

#### Queue
消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。 一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走 。

#### Consumer
消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

#### Virtual Host
虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。

# RabbitMQ整体架构

![](./images/rabbitMQ-3.png)

# RabbitMQ的运行流程

![](./images/rabbitMQ-4.png)

# RabbitMQ支持消息的模式

![](./images/rabbitMQ-5.png)

# 交换机的模式

交换机可以理解成具有路由表的路由程序，仅此而已。每个消息都有一个称为路由键（routing key）的属性，就是一个简单的字符串。RabbitMQ提供了四种Exchange模式：fanout,direct,topic,header 。 header模式在实际使用中较少，这里只说一下前三种模式.

#### fanout
fanout 模式就是广播模式，这种模式就是所有的消息来了会转发到所有绑定此交换机的队列上，这种模式转发消息是最快的。

#### Direct
Direct这个单词本意就是直接的意思，顾名思义，Direct 模式就是指定队列模式， 消息来了，只发给指定的 Queue, 其他Queue 都收不到，这里是将一个队列绑定到交换机上，要求该消息与一个特定的路由键完全匹配，一对一的匹配才会转发

#### Topic
topic模式，又称主题模式，将路由键和某模式进行匹配。此时队列需要绑定要一个模式上。符号“ # ”匹配一个或多个词，符号“ * ”匹配不多不少一个词。因此“abc.#”能够匹配到“abc.def.xyz”，但是 “ abc. * ” 只会匹配到“abc.def”,一定是规则匹配才会转发。

# 五种模式的简单实现

### Hello World

![](./images/rabbitMQ-6.png)

   - [send.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/send.go)

   - [receive.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/receive.go)。

### Work Queues

![](images/rabbitMQ-7.png)

   - [send.go](http://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/new_task.go)
   - [receive.go](http://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/worker.go)

### Publish/Subscribe

![](images/rabbitMQ-8.png)

   - [send.go](http://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/emit_log.go)
   - [receive.go](http://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/receive_logs.go)

### Routing

![](images/rabbitMQ-9.png)

   - [send.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/emit_log_direct.go)
   - [receive.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/receive_logs_direct.go)

### Topics

![](images/rabbitMQ-10.png)

   - [send.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/emit_log_topic.go)
   - [receive.go](https://github.com/rabbitmq/rabbitmq-tutorials/blob/main/go/receive_logs_topic.go)


# RabbitMQ高级-过期时间TTL

过期时间TTL表示可以对消息设置预期的时间，在这个时间内都可以被消费者接收获取；过了之后消息将自动被删除。RabbitMQ可以对消息和队列设置TTL。目前有三种种方法可以设置。

   - 通过命令行工具

    rabbitmqctl set_policy TTL ".*"  '{"message-ttl":60000}' --apply-to queues

   - 通过队列属性设置，队列中所有消息都有相同的过期时间。

    args := make(map[string]interface{})
    args["x-message-ttl"] =  60000
    channel.queueDeclare( "myqueue" , false , false , false , args)

   - 对消息进行单独设置，每条消息TTL可以不同。

    channel.PublishWithContext(
		ctx,
		exchange,
		//要设置
		routerKey,
		true,
		false,
		amqp.Publishing{
                ContentType: "text/plain",
                Body:        []byte(message),
                Expiration: "6000",
		})

	return err

如果上述三种方法同时使用，则消息的过期时间以两者之间TTL较小的那个数值为准。消息在队列的生存时间一旦超过设置的TTL值，就称为dead message被投递到死信队列， 消费者将无法再收到该消息。

# RabbitMQ高级-死信队列

DLX，全称为Dead-Letter-Exchange , 可以称之为死信交换机，也有人称之为死信邮箱。当消息在一个队列中变成死信(dead message)之后，它能被重新发送到另一个交换机中，这个交换机就是DLX ，绑定DLX的队列就称之为死信队列。
消息变成死信，可能是由于以下的原因：

   - 消息被拒绝
   - 消息过期
   - 队列达到最大长度

DLX也是一个正常的交换机，和一般的交换机没有区别，它能在任何的队列上被指定，实际上就是设置某一个队列的属性。当这个队列中存在死信时，Rabbitmq就会自动地将这个消息重新发布到设置的DLX上去，进而被路由到另一个队列，即死信队列。
要想使用死信队列，只需要在定义队列的时候设置队列参数 x-dead-letter-exchange 指定交换机即可。

#### 注意

   - 如果设置了队列的TTL，那么消息一旦超过了这个时间就会被丢弃（如果有死信队列会被丢弃到死信队列）
  
   - 如果设置的是消息的TTL，消息过期了不一定马上被抛弃。因为消息是否过期是在即将投递到消费者之前判定的，当队列出现消息积压的情况时，已过期的消息仍然能在队列中存活。

![](images/rabbitMQ-11.png)


# RabbitMQ高级-延迟队列

延迟队列是用来存放需要在指定时间被处理的元素的队列。如果我们希望一条消息在指定时间到了以后或之前处理，可以使用延迟队列。

延迟队列常见的使用场景：订单一段时间内未支付则自动取消、预定会议开始前十分钟通知与会人员参加会议。

TTL是RabbitMQ中一个消息或者队列的属性，表明一条消息或者一个队列中所有消息的最大存活时间。单位是ms

如果一条消息设置了TTL属性，或者一条消息进入了设置TTL属性的队列，则这条消息如果在TTL设置的时间内没有被消费，就会成为“死信”。

如果同时配置了消息的TTL和队列的TTL，较小的值会被使用。

### DLX+TTL实现延迟队列

我们可以使用消息和队列的TTL的属性和死信队列，实现延迟队列。思路是将过期队列转发到死信队列，消费者只要消费死信队列中的消息即可，就实现了延迟队列的功能。

比较队列TTL和消息TTL两种方式:

   - 队列设置TTL属性实现延迟队列，这种方式中，当队列到了过期时间，一定会被放到死信队列。但是这种方式不够灵活，如果想要改变延迟时间，就需要新建队列，面对大量不同时间需求，无法实现。

   - 消息设置TTL属性实现延迟队列，这种方式足够灵活，可以满足任意的延迟时间的需求。但是这种方式的严重缺陷是如果队列中消息积压，会导致过期的消息无法被丢弃（放到死信队列），导致死信队列的消费者无法按时收到消息。

    比如，如果第一个消息的过期时间较长，第二个消息的过期时间较短，
    则两个消息如果先后发送，会同时被消费者收到。
    因为RabbitMQ只检查当前第一个消息，直到等到第一个消息到了TTL时间，
    才会被死信队列收到，然后第二个消息被处理。

    正确的结果应该是根据消息的TTL，第二个消息先到达。

为了解决TTL消息的这种缺陷，我们可以使用插件实现延迟队列，满足不同延迟时间的需求。

### RabbitMQ插件实现延迟队列

#### 安装插件

```rabbitmq_delayed_message_exchange```插件正是为了解决TTL消息无法及时死亡的问题。

在https://www.rabbitmq.com/community-plugins.html 地址下载插件并安装。

下载完以后，将.ez后缀的文件复制到```usr/lib/rabbitmq/lib/rabbitmq_server-3.x.x/plugins```目录，这个目录用于存放RabbitMQ的插件。

进入到这个目录，然后执行语句：

```rabbitmq-plugins enable rabbitmq_delayed_message_exchange```

即可安装插件。

安装成功以后，在web管理界面，添加交换机可以看到新增的```x-delayed-message```类型。

#### 实现原理

如图，使用插件实现延迟队列的原理是创建一个类型为```x-delayed-message```的交换机（延迟交换机），这个类型的交换机支持延迟投递机制，即消息传递到以后先暂存到mnesia表中，到达指定的延迟时间以后才将消息投递出去。

如图，使用插件实现延迟队列的原理是创建一个类型为```x-delayed-message```的交换机（延迟交换机），这个类型的交换机支持延迟投递机制，即消息传递到以后先暂存到mnesia表中，到达指定的延迟时间以后才将消息投递出去。

![](images/rabbitMQ-12.png)


# RabbitMQ高级-发布确认

发布确认用于确保消息的可靠投递，如果消息投递成功，返回确认信息，如果投递失败，返回失败信息并确保消息不会丢失。

#### 发布确认

发布确认的回调方法可以判断出交换机是否收到消息，当交换机宕机或其他原因导致交换机没有收到生产者发出的消息时，回调方法能够做出反馈。

##### 单个确认发布
单个确认发布，即对每一条消息进行同步确认，生产者发布一条消息后只有它被确认发布后，后续的消息才能继续发布。

##### 批量确认发布
批量确认发布是指根据批次大小确认发布，这种方式的缺点是当发生故障导致发布出现问题时，不知道哪个消息出问题。

##### 异步确认发布
异步确认发布是效率和可靠性最高的。对于已确认消息和未确认消息，异步确认方式都能够处理。

异步确认发布主要是通过addConfirmListener方法监听确认和未确认的消息，使用哈希表记录所有发布的消息，对于成功确认的消息从哈希表中删除，剩下的是未确认的消息。

创建信道，配置异步通道

   ```
   ch, err := conn.Channel()

	if err != nil {
		return err
	}

	// 异步确认消息
	err = ch.Confirm(false)

	if err != nil {
		return err
	}

	confirms := ch.NotifyPublish(make(chan amqp.Confirmation, 1))
   ```

生产者代码

   生产者代码可以指定消息id，即回调接口中消息的id。

   ```
   //2.发送消息
   err = c.Ch.PublishWithContext(
      ctx,
      exchange,
      //要设置
      routerKey,
      true,
      false,
      amqp.Publishing{
         ContentType: "text/plain",
         Body:        []byte(message),
      })

   return err
   ```

实现回调接口

   ```
   func (c *Connection) NotifyAck(confirms chan amqp.Confirmation) {

      for ack := range confirms {
         if ack.Ack {
            fmt.Printf("confirmed delivery with delivery tag: %d", ack.DeliveryTag)
         } else {
            fmt.Printf("confirmed delivery of delivery tag: %d", ack.DeliveryTag)
         }
      }
   }
   ```
**如果只开启发布确认模式的话，当交换机收到生产者发送的消息后，会发布确认消息给生产者，如果发现路由不通，则会直接丢弃消息，此时生产者处于不知情状态，就会造成消息丢失。**

#### 回退消息

回退消息用在消息无法被路由（队列宕机）的情况。

   ```
   returnChan := c.Ch.NotifyReturn(make(chan amqp.Return, 1))

	go func() {
		for ack := range returnChan {
			g.Log().Info(ctx, fmt.Sprintf("消息消息被回退:消息id:%:%s", ack.MessageId, string(ack.Body)))
		}
	}()
   ```

生产者和消费者的实现并无特殊。当发送的消息无法被路由（比如队列宕机、RoutingKey错误等）时，会调用回退方法。

通过发布确认机制，生产者可以得知消息是否被交换机接收；通过回退消息机制，生产者可以得知消息消息是否被分发到队列中。

### 备份交换机

通过回退消息，我们可以感知到无法被路由的消息，但是只能把消息回退，如果像处理非常复杂。想要处理无法被路由的消息，需要使用备份交换机。

备份交换机可以理解为RabbitMQ中交换机的备胎，当交换机收到一条不可路由消息时，将会把这条消息转发到备份交换机中。通常备份交换机的类型为Fanout，这样就能将不可路由消息广播到所有和它绑定的队列中。

备份交换机除了添加备份队列以外，还可以添加一个报警队列，这样如果有无法被路由的消息，报警队列的消费者可以发出警报信息进行提示。

![](./images/rabbitMQ-13.png)






# RabbitMQ高级-集群

假设有三个个rabbitmq节点，分别为rabbit-1、 rabbit-2、rabbit-3，rabbit-1作为主节点，rabbit-2、rabbit-3作为从节点。