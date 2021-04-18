# pitaya [![Build Status][7]][8] [![Coverage Status][9]][10] [![GoDoc][1]][2] [![Docs][11]][12] [![Go Report Card][3]][4] [![MIT licensed][5]][6]

[1]: https://godoc.org/github.com/topfreegames/pitaya?status.svg
[2]: https://godoc.org/github.com/topfreegames/pitaya
[3]: https://goreportcard.com/badge/github.com/topfreegames/pitaya
[4]: https://goreportcard.com/report/github.com/topfreegames/pitaya
[5]: https://img.shields.io/badge/license-MIT-blue.svg
[6]: LICENSE
[7]: https://travis-ci.org/topfreegames/pitaya.svg?branch=master
[8]: https://travis-ci.org/topfreegames/pitaya
[9]: https://coveralls.io/repos/github/topfreegames/pitaya/badge.svg?branch=master
[10]: https://coveralls.io/github/topfreegames/pitaya?branch=master
[11]: https://readthedocs.org/projects/pitaya/badge/?version=latest
[12]: https://pitaya.readthedocs.io/en/latest/?badge=latest

Pitaya是一个简单、快速、轻量级的游戏服务器框架，通过[C SDK](https://github.com/topfreegames/libpitaya) 为iOS、Android、Unity等提供集群支持和客户端SDK。
它为分布式多人游戏服务器应用提供了一个基本的开发框架。

## 快速开始

### 准备工作

* [Go](https://golang.org/) >= 1.10
* [etcd](https://github.com/coreos/etcd) (用于服务发现)
* [nats](https://github.com/nats-io/nats.go) (可选，用于发送和接收rpc，可以替换成grpc的实现。)
* [docker](https://www.docker.com) (可选：用于在容器上运行 etcd 和 nats 。)

### 安装

克隆代码仓库
```
git clone https://github.com/topfreegames/pitaya.git
```
设置pitaya依赖
```
make setup
```

### Hacking pitaya

下面是一个运行Pitaya的例子:

启动etcd (该命令需要docker-compose和容器环境) ，当然你也可以不使用docker来运行etcd。)
```
cd ./examples/testing && docker-compose up -d etcd
```
从 cluster_grpc 示例中运行连接器前端服务器。
```
make run-cluster-grpc-example-connector
```
从 cluster_grpc 示例中运行room后端服务器。
```
make run-cluster-grpc-example-room
```

现在应该有2个pitaya服务器在运行，一个是前端连接器，一个是后端服务。可以使用pitaya的repl客户端[pitaya-cli](https://github.com/topfreegames/pitaya-cli) 来发送请求。

```
$ pitaya-cli
Pitaya REPL Client
>>> connect localhost:3250
connected!
>>> request room.room.entry
>>> sv-> {"code":0,"result":"ok"}
```

## 运行测试
```
make test
```
该命令将同时运行单元测试和e2e测试。

## 贡献
#TODO

## 作者
* **TFG Co** - Initial work

## 许可证
[MIT License](./LICENSE)

## 鸣谢
* [nano](https://github.com/lonnng/nano) authors for building the framework pitaya is based on.
* [pomelo](https://github.com/NetEase/pomelo) authors for the inspiration on the distributed design and protocol

## 安全

如果您发现了安全漏洞，请发邮件至 security@tfgco.com。

## 资源

- 其他pitaya相关的项目
  + [libpitaya-cluster](https://github.com/topfreegames/libpitaya-cluster)
  + [libpitaya](https://github.com/topfreegames/libpitaya)
  + [pitaya-admin](https://github.com/topfreegames/pitaya-admin)
  + [pitaya-bot](https://github.com/topfreegames/pitaya-bot)
  + [pitaya-cli](https://github.com/topfreegames/pitaya-cli)
  + [pitaya-protos](https://github.com/topfreegames/pitaya-protos)

- 文档
  + [API参考](https://godoc.org/github.com/topfreegames/pitaya)
  + [详细文档](https://pitaya.readthedocs.io/en/latest/)

- Demo
  + [用pitaya和WebSocket实现一个100行代码的聊天室](./examples/demo/chat) (改编自 [nano](https://github.com/lonnng/nano) 的示例 )
  + [Pitaya集群模式示例](./examples/demo/cluster)
  + [使用protobuf的Pitaya集群模式示例](./examples/demo/cluster_protobuf)
