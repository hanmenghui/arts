### ARTS

本篇为ARTS第五篇

Algorithm、Review、Technique、Share

----

#### Algorithm

题目： 选择中

Code

```

```

---



#### Review####

文章：[Understanding the Differences between AMQP & JMS](http://www.wmrichards.com/amqp.pdf)

本文从JMS和AMQP的历史开始，根据不同场景的需求，对比两者在Message Routing，Message Model以及Message Structure的实现方式上异同对JMS和AMAP做了对比。这篇文章并没有神人的具体的协议细节，若对两者个各自实现想有更深入的了解换需要查看更多资料。

在消息中间件上，本人研究的很少，虽然知道它的应用场景，但实际中没有对此有过多了解，这篇文章算是我对此的一个开始。因为此篇文章仅仅是对两者做了一个笼统的对比，也只是能让读者在不了解两者测情况下有一个大概的认识，所以后续我也会从各自的底层是线上有一个更深入的学习。

下面我会就我的所理解做一个总结。

##### What Is Messaging?

Messaging 是组件或者应用之间通讯/交流的方式。消息客户端可以向外发送消息或者接受消息。消息中间件是分布式消息通讯实现松耦合，发送者和接受者也无需同时在线，实际上，接收双发无需彼此知道对方，它们仅仅需要知道彼此支持的消息格式，以及使用的destination。应用之间的通讯方式有很多，从基于WSDL的Web Service到SOA。WSDL虽然可以跨语言跨平台通讯，但接口一旦设定好，接口的变动就会直接影响到调用方；SOA我们知道通常用于同一种开发语言之间的通讯，且调用方也必须知道对方的接口方法。

##### When Can You Use Messaging？

1.消息发送发不依赖于消息接受放的接口定义。

2.消息发送方的运行不依赖已消费方是否也在运行。

3.消息发送者不期许消息发送后理解收到反馈。

###### For example

1.当库存数量低于设定的最低剩余数量时，“库存部门”发送一个消息给“工厂组件”告诉“生产部门”加紧生产。

2.“工厂部门”发生消息到“零件部门“生产所需要的零件。

3.”零件部门“转过来可以发送消息给自己的库存部门加紧生产或者采购零件。

4.生产部门和零件部门又可以同时发生消息给财务部门更新预算。

![Diagram showing messaging between various departments in an enterprise](https://docs.oracle.com/javaee/6/tutorial/doc/figures/jms-msgEnterpriseApp.gif)

###### JMS History

```
When the JMS API was introduced in 1998, its most important purpose was to allow Java applications to access existing messaging-oriented middleware (MOM) systems, such as MQSeries from IBM. Since that time
```

##### JMS AND AMQP

过去十多年里，JMS为Java应用之间提供了灵活的消息通讯并成为Java中的一个标准。同时在实际中，很多开源的可用于商业的消息中间件实现也内置了JMS消息同其他平台或者语言之间的通讯的消息转换。在Java平台你也可以通过改动很小的配置来把一个JMS message broker 替换成其它的。

这么看来业界真的需要消息中间标准吗？

JMS为Java平台提供了强壮成熟的消息通讯API，通过JMS你可以通过改动很小的配置来切换实现JMS标准的不同message broker。它同时也支持Java平台上不同语言的通讯，比如Scala和Groovy。

JMS定义了Java平台上的消息通讯标准，但不适用与非Java平台之间的消息通讯,比如Java 同Ruby ，或者非Java平台间的通讯，例如.Net 同Ruby。

如果你仅仅用于Java平台间的通讯，在这种情况下你可以使用JMS API 作为发送者和接受者的client，并可以选用一种实现了JMS API的message broker（比如Active MQ)来储存和发送消息。

现在假设你在Java 应用发送消息到Ruby应用（也可以反过来），由于Ruby上无法使用JMS API ，你不得不寻找一个可以吧JMS message 转换成  Ruby 支持的消息协议。Ruby平台上使用最广的是STOMP 协议，你就得寻找一个即支持JMS同时又支持STOMP的message broker。

当然，这种情况下你可以选择使用ActiveMQ，它使用的OpenWire 协议同时被JMS和STOMP所支持，同时可以把JMS的消息格式转换成STOMP的消息、虽然这种方式可行，但也带来了很大的缺陷：1.两边的协议可能所同时支持的消息类型有限（例如，ObjectMessage，StreamMessage 等等）2.在上面这种场景里，你不得不限制所能选择的Message broker，比如ActiveMQ支持JMS到STOMP的消息转换。最后，两边的协议所支持的自定义属性，消息头很可能也不一致。

需要一个跨平台跨语言的消息通讯系统正是AMQP出现的原因，JMS提供了一个Java平台上的消息通讯标准，AMQP则定义了跨平台跨语言的消息通讯标准，它定义了消息结构以及如何跨平台的通讯。

So what API and corresponding client library should you use for AMQP? Which AMQP message broker shou you use? Guess what- ***It doesn't matter***  意思就是你可以选择任一AMQP API 和AMQP message broker。

###### Message Routing

Message Routing 描述了消息是如何从 producer（sender）到达消息consumer（receiver）的。AMQP和JMS之间有很大的不同。

JMS 使用了一个简单的Routing scheme，发送者和接收这双方的消息交换通过连接到同一名称的queue或者topic。

```Java
queue=session.createQueue("message1_queue");
sender=session.createSender(queue);
msg=session.createTextMessage("test");
sender.send(msg)
```

一个消息消费者通过连接到同一个queue（message1_queue）来接收queue中的消息

```Java
queue=session.createQueue("message1_queue");
receiver=session.createReceiver(queue);
msg=receiver.receive(100000);
```

在JMS中你也可以定义一个selector来进一步控制consumer可以在queue中接收的消息。在使用了selector时，consumer只能接收对应的selector所标注的消息。

```Java
queue=session.createQueue("message1_queue");
receiver=session.createReceiver(queue);
msg=receiver.receive(100000,"state='MA'");
```

![Image result for queue producer consumer jms](https://javainsider.files.wordpress.com/2012/09/jms1.png)

AMQP 对于消息的routing和JMS相比有一点不同。相比于JMS发送一个消息到queue，AMQP发送消息到 “extrange” ，extrange和queue通过被称做 bidings的方式进行绑定。一个binding指定了一个extrange中的消息到达那一个queue。同时，消息消费者会绑定到一个queue。

![img](https://wx1.sinaimg.cn/mw690/4c774c9dgy1ftqu8irauwj209c0x0te4.jpg)

```Java
channel.exchangeDeclare("orders","direct",true)
String routeingKey="orders.us.ma";
byte[] msg="test".getBytes();
channel.basicPublish("orders",routeingKey,null,msg)
```

对于消息消费方，要接受这条消息，首先要定义一个queue,然后绑定这特queue到这个名字是orders的exchange上。

```Java
String queue="ma_orders";
channel.queueDeclare("ma_orders",...);
channel.queueBind(queue,"orders","orders.us.ma");
channel.basicConsume(queue,...);
delivery=consumer.nextDelivery();
```

注意 消息消费方有责任绑定queue到exchange。在AMQP中消息消费方不知道消息会发送到哪个Queue。这被称为消费者驱动 consumer-driven messaging.

对于JM和AMQP两者routing上的优劣，需要进一步去学习了解。

###### Message Model Differences

在JMS中有两种消息通讯类型: Point-to-Point message 和Publish-and-Subscribe(topic)。

Point-to-Point：当发送方发送一个消息到一个queue，它只能被一个consumer所接收。

Publish-and-Subscribe：一个消息被所有消费方所接收。 

AMQP提供了5中消息类型，其中有两种AMQP的实现者必须支持：Direct Exchange 和 Fanout Exchange，也可以同时选择性的实现其它三种：Topic Exchange，Headers Exchange，System Exchange。

Direct Exchange 接近于JMS的Point-to-Point 消息通讯类型，这两者的最大差别是，在AMQP中你可以绑定多个direct queues 到这个exchange，意思就是可以同时有多个消费者，而在JMS中你需要保证只能有一个consumer。

Fanout Exchange ，Topoic Exchange和Headers Exchange 本质上和JMS的Publish-and-Subscrible类似。Fanout Exchange 会无条件接收所有binging到exchange的消息，忽略routeingKey；

Topic Exchange : queue 提供一个binding 参数绑定到 exchange，但可以使用通配符（* 或者#） 例如”orders.us.*“

Headers Exchange: 他所用Headers参数替换routeingKey。

###### Message Structure Differences

AMQP和JMS的消息结构非常类似。JMS的消息分成三个部分：header section 包含了不可变的JMS消息头（例如：JMSMessageID、JMSTimestamp，等等）；一个可变的应用程序可自定义的KEY-VLUE消息对；最后是消息体部分：Object,Map,Text,Bytes和Steam五中消息体中的任何一个。

AMQP消息的划分和JMS极为类似。不过值得注意的是AMQP仅支持一种消息体类型：bytes；

###### Reference

[Java Message Service Concepts](https://docs.oracle.com/javaee/6/tutorial/doc/bncdr.html)

[Explanation of the Hub and Spoke Business Model](https://brandongaille.com/explanation-of-the-hub-and-spoke-business-model/)

[Message broker](https://en.wikipedia.org/wiki/Message_broker)

#### Share

AMQP和RabbitMQ入门 

http://www.infoq.com/cn/articles/AMQP-RabbitMQ