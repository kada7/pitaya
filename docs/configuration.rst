*************
配置
*************

Pitaya使用Viper来控制其配置。下面我们按主题来描述配置变量的划分。我们认为这些配置的默认值可以满足大多数场景，但某些场景下可能需要改变。

Service Discovery
=================

这些配置值用于设置etcd服务发现模块。
 只有当应用程序在集群模式下运行时，才需要设置这些值。

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.cluster.sd.etcd.dialtimeout
    - 5s
    - time.Time
    - 传递给服务发现etcd客户端的拨号超时时间。
  * - pitaya.cluster.sd.etcd.endpoints
    - localhost:2379
    - string
    - 逗号分隔的 etcd 端点列表
  * - pitaya.cluster.sd.etcd.user
    - 
    - string
    - 连接到etcd的用户名
  * - pitaya.cluster.sd.etcd.pass
    - 
    - string
    - 连接到etcd的密码
  * - pitaya.cluster.sd.etcd.heartbeat.ttl
    - 60s
    - time.Time
    - etcd lease 的心跳间隔。
  * - pitaya.cluster.sd.etcd.grantlease.timeout
    - 60s
    - time.Duration
    - etcd lease 的超时时间
  * - pitaya.cluster.sd.etcd.grantlease.maxretries
    - 15
    - int
    - 最多尝试etcd lease的次数
  * - pitaya.cluster.sd.etcd.grantlease.retryinterval
    - 5s
    - time.Duration
    - 每次尝试etcd lease之间的时间间隔
  * - pitaya.cluster.sd.etcd.revoke.timeout
    - 5s
    - time.Duration
    - etcd的撤销功能超时
  * - pitaya.cluster.sd.etcd.heartbeat.log
    - false
    - bool
    - 是否应该在调试模式下记录etcd的心跳日志
  * - pitaya.cluster.sd.etcd.prefix
    - pitaya/
    - string
    - 前缀用于避免不同的pitaya应用之间的冲突，服务器必须有相同的前缀才能够看到对方
  * - pitaya.cluster.sd.etcd.syncservers.interval
    - 120s
    - time.Duration
    - 服务发现模块执行的服务器同步的时间间隔。
  * - pitaya.cluster.sd.etcd.shutdown.delay
    - 10ms
    - time.Duration
    - 从服务发现中取消注册后等待关机的时间
  * - pitaya.cluster.sd.etcd.servertypeblacklist
    - nil
    - []string
    - 服务发现应该忽略的服务器类型列表。
  * - pitaya.cluster.sd.etcd.syncserversparallelism
    - 10
    - int
    - 在etcd初始化时用于获取服务器信息使用的goroutine数量。

RPC 服务
===========

只有当RPC服务启用了给定类型时，才需要设置配置。

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.buffer.cluster.rpc.server.nats.messages
    - 75
    - int
    - nats RPC服务器在开始丢弃传入消息之前接受的缓冲区大小。
  * - pitaya.buffer.cluster.rpc.server.nats.push
    - 100
    - int
    - nats RPC服务器为推送消息创建的缓冲区大小。
  * - pitaya.cluster.rpc.client.grpc.dialtimeout
    - 5s
    - time.Time
    - gRPC客户端建立连接的超时时间
  * - pitaya.cluster.rpc.client.grpc.lazyconnection
    - false
    - bool
    - gRPC客户端是否应该使用懒惰连接，即只有在向该服务器发出请求时才连接。
  * - pitaya.cluster.rpc.client.grpc.requesttimeout
    - 5s
    - time.Time
    - 请求gRPC客户端的RPC调用超时。
  * - pitaya.cluster.rpc.client.nats.connect
    - nats://localhost:4222
    - string
    - Nats客户端使用的地址
  * - pitaya.cluster.rpc.client.nats.connectiontimeout
    - 5s
    - time.Duration
    - nats客户端建立连接的超时时间。
  * - pitaya.cluster.rpc.client.nats.requesttimeout
    - 5s
    - time.Time
    - 使用nats客户端的RPC调用请求超时。
  * - pitaya.cluster.rpc.client.nats.maxreconnectionretries
    - 15
    - int
    - 客户端重新连接到nats的最大重试次数。
  * - pitaya.cluster.rpc.server.nats.connect
    - nats://localhost:4222
    - string
    - Nats服务器的地址
  * - pitaya.cluster.rpc.server.nats.connectiontimeout
    - 5s
    - time.Duration
    - nats服务器建立连接的超时。
  * - pitaya.cluster.rpc.server.nats.maxreconnectionretries
    - 15
    - int
    - 服务器重新连接到nats的最大重试次数。
  * - pitaya.cluster.rpc.server.grpc.port
    - 3434
    - int
    - gRPC服务器监听的端口。
  * - pitaya.concurrency.remote.service
    - 30
    - int
    - nats RPC服务在远程服务上处理消息的goroutine数量。
  * - pitaya.worker.redis.url
    - localhost:6379
    - string
    - 用来注册pitaya worker的redis的地址
  * - pitaya.worker.redis.pool
    - 10
    - string
    - 与Redis保持的连接数
  * - pitaya.worker.redis.password
    - ""
    - string
    - 用来注册pitaya worker的redis的密码
  * - pitaya.worker.concurrency
    - 1
    - int
    - 执行job的worker数量
  * - pitaya.worker.namespace
    - ""
    - string
    - worker命名空间，可用于蓝绿部署中的不同堆栈。
  * - pitaya.worker.retry.enabled
    - true
    - bool
    - 如果为真且在最大时间内出错，则重试job
  * - pitaya.worker.retry.max
    - 5
    - int
    - 最大重试job次数
  * - pitaya.worker.retry.exponential
    - 2
    - int
    - Retry job after backoff of nRetry**2
  * - pitaya.worker.retry.minDelay
    - 0
    - int
    - 等待后退重试job的最短时间。
  * - pitaya.worker.retry.maxDelay
    - 10
    - int
    - 等待后退重试工作的最大时间。
  * - pitaya.worker.retry.maxRandom
    - 10
    - int
    - 后退过程中随机等待的时间

连接
==========

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.handler.messages.compression
    - true
    - bool
    - 客户端和服务器之间的信息是否应该被压缩
  * - pitaya.heartbeat.interval
    - 30s
    - time.Time
    - 保持客户端连接的心跳间隔
  * - pitaya.conn.ratelimiting.interval
    - 1s
    - time.Duration
    - 计算请求的时间窗口
  * - pitaya.conn.ratelimiting.limit
    - 20
    - int
    - 一个时间段内允许的最大请求数
  * - pitaya.conn.ratelimiting.forcedisable
    - false
    - bool
    - 如果为真，即使在添加WithWrappers时也会忽略速率限制。

Metrics Reporting
=================

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.metrics.statsd.enabled
    - false
    - bool
    - 是否启用statsd报告
  * - pitaya.metrics.statsd.host
    - localhost:9125
    - string
    - 要发送指标的statsd服务器的地址
  * - pitaya.metrics.statsd.prefix
    - pitaya.
    - string
    - 向statsd报告的指标前缀
  * - pitaya.metrics.statsd.rate
    - 1
    - int
    - statsd 指标率
  * - pitaya.metrics.prometheus.enabled
    - false
    - bool
    - 是否应启用prometheus报告
  * - pitaya.metrics.prometheus.port
    - 9090
    - int
    - 暴露prometheus指标的端口。
  * - pitaya.metrics.constTags
    - map[string]string{}
    - map[string]string
    - 要添加到报告指标的常量标签
  * - pitaya.metrics.additionalTags
    - map[string]string{}
    - map[string]string
    - 报告指标的附加标签，map的key标签value为默认值。
  * - pitaya.metrics.periodicMetrics.period
    - 15s
    - string
    - 报告系统指标的时期
  * - pitaya.metrics.custom.counters
    - []map[string]interface{}
    - []map[string]interface
    - 自定义指标计数器
  * - pitaya.metrics.custom.counters[].Subsystem
    - ""
    - string
    - 自定义计数器的子系统名称
  * - pitaya.metrics.custom.counters[].Name
    - ""
    - string
    - 自定义计数器名称，不得为空
  * - pitaya.metrics.custom.counters[].Help
    - ""
    - string
    - 用于解释自定义计数器是什么指标，不得为空。
  * - pitaya.metrics.custom.counters[].Labels
    - []string{}
    - []string
    - 指标将会携带的自定义计数器的标签
  * - pitaya.metrics.custom.gauges
    - []map[string]interface{}
    - []map[string]interface
    - 自定义指标的仪表盘
  * - pitaya.metrics.custom.gauges[].Subsystem
    - ""
    - string
    - 自定义仪表盘的子系统名称
  * - pitaya.metrics.custom.gauges[].Name
    - ""
    - string
    - 自定义仪表盘的名称，不得为空
  * - pitaya.metrics.custom.gauges[].Help
    - ""
    - string
    - 用于解释自定义的仪表盘是什么指标，不得为空。
  * - pitaya.metrics.custom.gauges[].Labels
    - []string{}
    - []string
    - 指标携带的自定义仪表盘标签
  * - pitaya.metrics.custom.summaries
    - []map[string]interface{}
    - []map[string]interface
    - 自定义指标摘要
  * - pitaya.metrics.custom.summaries[].Subsystem
    - ""
    - string
    - 自定义摘要子系统名称
  * - pitaya.metrics.custom.summaries[].Name
    - ""
    - string
    - 自定义摘要名称，不得为空
  * - pitaya.metrics.custom.summaries[].Help
    - ""
    - string
    - 用于解释自定义的摘要是什么指标，不得为空。
  * - pitaya.metrics.custom.summaries[].Labels
    - []string{}
    - []string
    - 指标携带的自定义摘要标签
  * - pitaya.metrics.custom.summaries[].Objectives
    - map[float64]float64
    - map[float64]float64{0.5: 0.05, 0.9: 0.01, 0.99: 0.001}
    - 自定义摘要目标与量化指标

并发
===========

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.buffer.agent.messages
    - 100
    - int
    - 每个agent接收客户信息的缓冲区大小。
  * - pitaya.buffer.handler.localprocess
    - 20
    - int
    - handler接收并在本地处理的消息的缓冲区大小。
  * - pitaya.buffer.handler.remoteprocess
    - 20
    - int
    - handler接收并转发到远程服务器的消息的缓冲区大小。
  * - pitaya.concurrency.handler.dispatch
    - 25
    - int
    - 在handler service中处理信息的goroutine的数量。

Module
=======

这些配置只有在创建Module时才会使用。建议使用Binding Storage模块与gRPC RPC服务，才能使用所有RPC服务功能。

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.session.unique
    - true
    - bool
    - Pitaya 是否应该为客户强制执行独特的会话，启用独特的会话模块。
  * - pitaya.modules.bindingstorage.etcd.endpoints
    - localhost:2379
    - string
    - 逗号分隔的 etcd 端点列表，要被Bindings Storage模块使用，应该与服务发现 etcd 相同。
  * - pitaya.modules.bindingstorage.etcd.prefix
    - pitaya/
    - string
    - etcd的前缀，应该与服务发现的前缀相同。
  * - pitaya.modules.bindingstorage.etcd.dialtimeout
    - 5s
    - time.Time
    - 建立etcd连接的超时
  * - pitaya.modules.bindingstorage.etcd.leasettl
    - 1h
    - time.Time
    - 自动续约前的etcd lease时间

默认Pipeline
=================

这些配置控制了是否应该启用默认Pipeline

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.defaultpipelines.structvalidation.enabled
    - false
    - bool
    - Pitaya是否应该为处理程序参数启用默认结构验证器。

Groups
=================

这些配置用于组服务的实现。

.. list-table::
  :widths: 15 10 10 50
  :header-rows: 1
  :stub-columns: 1

  * - 配置
    - 默认值
    - 类型
    - 描述
  * - pitaya.groups.etcd.endpoints
    - localhost:2379
    - string
    - 逗号分隔的 etcd 端点列表，供群组 etcd 服务使用。
  * - pitaya.groups.etcd.prefix
    - pitaya/
    - string 
    - etcd中每个Group的key的前缀。
  * - pitaya.groups.etcd.dialtimeout
    - 5s
    - time.Time
    - 建立etcd组连接的超时
  * - pitaya.groups.etcd.transactiontimeout
    - 5s
    - time.Duration
    - 超时完成对Etcd的分组请求
  * - pitaya.groups.memory.tickduration
    - 30s
    - time.Duration
    - 根据这个tick的间隔时间来判断是否要删除组
