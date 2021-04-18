特性
========

Pitaya采用模块化、可配置的架构，这样有助于隐藏扩展应用程序和管理客户端Session和通信的复杂性。

下面介绍其一些核心功能。

## Frontend服务器和Backend服务器

在Cluster模式下，服务器可以是Fronted服务器，也可以是Backend服务器。

Frontend服务器必须指定用于接收传入的客户端连接的监听器，它们能够根据路由逻辑将收到的消息转发到相应的服务器。

Backend服务器不监听连接，它们只接收RPC，转发的客户端消息（sys rpc），也可以是来自其他服务器的RPC（user rpc）。

## Group

群组是存储目标用户信息的结构，允许向群组中的所有用户发送广播信息，也可以根据某些标准向用户的子集发送多播信息。

Group很适合用来创建游戏房间，例如，你只需要把一个游戏房间中的所有玩家放到同一个Group中，然后你就可以向他们所有人广播游戏室的状态。

## Listener

Frontend服务器必须指定一个或多个Listener来处理传入的客户端连接，Pitaya已经实现了TCP和Websocket Acceptor，
还可以向应用程序中添加满足Acceptor接口的自定义Acceptor。

## Acceptor Wrapper

Acceptor Wrapper用于包装Acceptor（如TCP和Websocket），以便在执行消息转发之前读取和更改输入的数据。要创建一个新的Wrapper，
只需实现Wrapper接口（或从BaseWrapper继承结构），并通过使用WithWrappers方法将其添加到Acceptor中。后续将演示如何使用Acceptor Wrapper。

## 限流

限流器通过读取每个玩家连接上的传入数据，以限制请求的吞吐量。超过限制后，请求会被丢弃，直到有空闲时间。在玩家的连接就已经做了请求计数的管理，
Session绑定之前就已经开始。使用的算法是
[Leaky Bucket](https://en.wikipedia.org/wiki/Leaky_bucket#Comparison_with_the_token_bucket_algorithm) 。
这个算法采用了一个漏斗，它的输出流比输入流慢。它将每个请求的时间戳保存在一个`slot`中（总共有`limit`个slot），这个slot在`interval`后再次被释放。

例如：如果限制了间隔1s只能有一个请求(`limit` = 1, `interval` = 1s)，当一个请求在0.2s发生时，下一个请求将在1s后才被pitaya处理（在1.2s时）。

```
0     request
|--------|
   0.2s
0                 available again
|------------------------|
|- 0.2s -|----- 1s ------|
```

## 消息转发

当服务器实例收到客户端消息时，它会通过查看路由来检查目标服务器类型。如果目标服务器类型与接收服务器类型不同，
则实例将消息转发到正确类型的服务器实例。客户端不需要采取任何操作来转发消息，这个过程是由Pitaya自动完成的。

默认情况下，路由功能会随机选择一个目标服务器类型的实例。可以定义自定义函数来改变这种行为。

## 消息推送

可以推送给消息用户，而无需事先了解Session或连接状态的信息。推送消息包含以下内容：
- 消息路由(这样客户端就可以识别来源并正确处理)，
- 消息内容
- 推送目标ID
- 客户端预期连接的服务器类型。

## Module

Module是可以注册到Pitaya应用的实例，它必须实现
[Module接口](https://github.com/topfreegames/pitaya/tree/master/interfaces/interfaces.go#L24) 。
Pitaya负责根据需要调用相应的生命周期方法，可以通过名称检索已经注册的模块。

Pitaya自带了一些已经实现的模块，可以根据需要实现更多的模块。目前Pitaya拥有的模块有：

### Binary

这个模块作为一个子进程启动一个二进制文件，并将其stdout和stderr分别传送到info和错误日志信息中。

### Unique Session

这个模块为 "OnSessionBind "添加了一个回调，检查被绑定的id是否已经在其他Frontend服务器中被绑定。

### Binding storage

该模块实现了gRPC所需实现的功能，以实现在不知道用户连接的服务器的情况下向用户广播Session绑定和推送的功能。

## 监控

Pitaya支持监控报告，它已经实现了对Prometheus和Statsd的支持，并支持实现`Reporter`接口的自定义报告器。Pitaya还支持OpenTracing，
允许轻松整合Jaeger和其他框架。

`Reporter`报告的指标列表是。

- Response time：处理一条消息的时间，单位是纳秒。它是分段的 由路由、状态、服务器类型和响应代码组成。
- Process delay time：开始处理消息的延迟，单位为纳秒。 它是按路由和服务器类型划分的。
- Exceeded Rate Limit：通过超出速率限制，阻塞的请求数。
- Connected clients：目前连接的客户端数量。
- Server count：按服务发现的服务器数量。它是按服务器类型划分。
- Channel capacity：通道的可用容量。
- Dropped messages：rpc服务器丢弃的消息数量，即未处理的消息。
- Goroutines count：当前Goroutines的数量。
- Heap size：当前的堆大小。
- Heap objects count：当前堆上对象的数量。
- Worker jobs retry：当前RPC可靠性工人作业重试的数量。
- Worker jobs total：当前RPC可靠性工作者工作的数量。它是按作业状态划分。
- Worker queue size：当前RPC可靠性工作者作业队列的大小。它是由每个可用的队列来划分的。

### 自定义指标

除了pitaya默认的监控，还可以创建新的指标。如果只使用Statsd report，不需要配置。如果使用Prometheus，则需要添加一个配置来指定度量参数。
更多细节请参见[doc](configuration.html#metrics-reporting) 和这个
[example](https://github.com/topfreegames/pitaya/tree/master/examples/demo/custom_metrics) 。

## Pipeline

Pipeline是中间件，它允许在处理程序请求之前和之后执行方法，它们接收请求的上下文和请求数据并返回请求数据，请求数据被传递给Pipeline中的下一个方法。

## RPCs

Pitaya在Cluster模式下支持RPC调用，有两个组件可以实现，RPC客户端和RPC服务器。目前Pitaya实现的RPC有两种选择，NATS和gRPC，默认是NATS。

有两种类型的RPC，_Sys_和_User_。

### Sys RPCs （系统RPC）

这些是服务器在将处理程序消息转发到相应的服务器类型时所做的RPC。

### User RPCs (用户RPC)

User RPC是在应用程序主动调用另一台服务器中的远程方法时进行的。调用时可以指定目标服务器的ID，也可以让Pitaya根据路由逻辑选择一个目标服务器。

### User Reliable RPCs (用户可靠RPC)

这些都是在应用程序使用worker调用远程时完成的，也就是说，如果发生任何错误，Pitaya会重试RPC。

**重要**：被调用的远程必须是idempotent；另外ReliableRPC不会返回远程的回复，因为它是异步的，如果成功的话，它只返回作业id（jid）。

## 服务器操作模式

Pitaya有两种操作方式：Standalone(单机)模式和Cluster(集群)模式。

### Standalone模式

在Standalone模式下，服务器之间不交互，不使用服务发现，也不支持RPC。这是一个有限的框架版本，当应用程序不需要拥有不同类型的服务器或在它们之间进行通信时，可以使用它。

### Cluster模式

Cluster模式是一种更完整的模式，使用服务发现、RPC客户端和服务器以及应用程序的服务器之间的远程通信。这种模式对于更复杂的应用是有用的，
它可能会受益于在不同的专业类型的服务器之间分割责任。这种模式已经为RPC调用和服务发现提供了默认服务。

## Serializer (Serializer)

Pitaya支持不同类型的消息序列化器，用于发送和接收客户端的消息，默认的序列化器是JSON序列化器，Pitaya对Protobuf序列化器也有本地支持。
可以通过实现`serialize.Serializer`接口来实现新的序列化器。

应用程序可以通过调用`pitaya`包中的`SetSerializer`方法来设置所需的序列化器。

## 服务发现

在Cluster模式下运行的服务器必须有一个服务发现客户端才能工作。Pitaya自带一个使用etcd的默认客户端，如果没有定义其他客户端，则使用该客户端。
服务发现客户端负责注册服务器，更新有效服务器列表，并根据需要提供有关请求服务器的信息。

## Sessions (会话)

客户端建立的每个连接都有一个相关的Session实例，该实例是短暂的，当连接关闭时就会被销毁。Session是Pitaya核心功能的一部分，
可以使用Session与客户端进行异步通信，并在请求之间存储数据。Session的主要特点是:

* **ID绑定** - Session可与用户ID绑定，允许应用程序的其他部分向用户发送消息，而无需知道用户连接到哪个服务器或连接。
* **数据存储** - Session可用于数据存储，在请求之间存储和检索数据。
* **消息传递** - 可以通过Session向连接的用户发送消息，而不需要了解基础连接协议。
* **在请求时可访问** - Handler可以从context实例中获取当前Handler对应的Session
* **Kick** - 可以通过Session的 "Kick "方法将用户从服务器上踢出去。

尽管Session在Frontend服务器和Backend服务器的处理程序请求中都可以访问，但如果是前端或后端Session，它们的行为就有些不同。
这主要是由于Session实际上存在于Frontend服务器中，而只是将其状态的表示发送到Backend服务器。

如果一个Session是从前台服务器访问的，就被认为是前台Session，而从后台服务器访问的就是后台Session。下面对每一种Session进行详细说明:

### Frontend Session

Session与Frontend服务器中的一个连接相关联，可以在建立连接的服务器中通过SessionID或绑定用户ID进行检索，但不能在不同的服务器中检索。

可以对Session的一些生命周期的添加回调，如关闭和绑定。可以是在单个Session上添加回调（用`s.OnClose`），也可以在每个Session上添加回调
（用`OnSessionClose`、`OnSessionBind`和`OnAfterSessionBind`）。

### Backend Session

可以通过Handler的方法来访问后端Session，但它们有一些限制和特殊的特性：
- 必须通过调用`s.PushToFront`来修改Session中的变量， 将其推送到Frontend服务器（在`s.Bind`操作中则不需要）
- 不允许对Session生命周期设置回调
- 不能通过用户ID从Backend服务器检索Session。

