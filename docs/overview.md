概述
========

Pitaya是一个简单易用的轻量级游戏服务器框架，其灵感来自于
[starx](https://github.com/lonnng/starx) 和[pomelo](https://github.com/NetEase/pomelo) ，
基于[nano](https://github.com/lonnng/nano) 进行了二次开发。

pitaya的旨在为分布式网游应用提供一个基础、强大的开发框架。

## 功能

* **用户会话** -- -- Pitaya支持用户会话，允许将会话与用户ID绑定，设置自定义数据，并在会话活动时从其他地方检索数据。
* **集群支持** - Pitaya默认支持的服务发现和RPC模块，允许不同类型的服务器之间轻松通信。
* **WS和TCP监听器** -- -- Pitaya支持TCP和Websocket接受器，这些接受器是从接收请求的应用程序中抽象出来的。
* **Handler和Remote** - Pitaya允许应用程序指定Handler来接收客户端的消息，这些Handler的Remote可以处理RPC服务器的消息。
  它们都可以指定自定义的init、afterinit和shutdown方法。
* **消息转发** - 当服务器收到处理消息时，可以将消息转发到正确类型的服务器。
* **客户端SDK** - [libpitaya](https://github.com/topfreegames/libpitaya) 是Pitaya的官方客户端SDK。
* **监测** -- -- Pitaya默认支持Prometheus和statsd，并接受实现Reporter接口的其他自定义Reporter。
* **兼容OpenTracing** - Pitaya兼容[OpenTracing](http://opentracing.io/) ，因此可以使用[Jaeger](https://github.com/jaegertracing/jaeger) 或其他兼容框架。
* **自定义模块** - Pitaya已经有一些默认模块，也支持自定义模块。
* **自定义序列化** - Pitaya本机支持JSON和Protobuf消息，并可根据需要添加其他自定义序列化。
* **用其他语言编写兼容的服务器** - 使用 [libpitaya-cluster](https://github.com/topfreegames/libpitaya-cluster) 可以用其他语言编写pitaya兼容的服务器，这些服务器可以在集群中注册并处理RPCs，
  在repo中已经有一个与unity兼容的csharp库和一个python库。
* **用于开发/调试的REPL客户端** - [Pitaya-cli](https://github.com/topfreegames/pitaya-cli) 是一个REPL(解释型)客户端，可以用它轻松开发和调试pitaya服务器。
* **用于集成/压力测试的机器人** - [Pitaya-bot](https://github.com/topfreegames/pitaya-bot) 是一个服务器测试框架，它可以轻松地将用户行为复制到测试用例中，
  可以用机器人验证收到的响应，或者对pitaya服务器进行大规模访问。

## 架构

Pitaya以模块化和可扩展性为核心，同时提供了坚实的基础功能，将客户端交互抽象为的接口。完整的API文档可以在[godoc](https://godoc.org/github.com/topfreegames/pitaya) 查看。

## 谁在使用它

好吧，现在只有我们TFG公司在使用它，但如果能让一个社区围绕着这个项目，那就太好了。希望能尽快收到你们的回复。

## 如何贡献？

就像平常一样。Fork, Hack, Pull Request. 循环往复。另外，不要忘了编写测试和文档。