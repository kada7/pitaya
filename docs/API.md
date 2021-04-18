Pitaya API
==========

## Handler

Handler是Pitaya的核心功能之一，它们是负责接收来自客户端的请求并进行处理的实体，
如果该方法是Request类型的Handler，则返回响应，如果该方法是Notify类型的Handler，则什么也不返回。

### Handler函数签名

Handler必须是struct的公共方法，并且有如下函数签名。

输入参数
* `context.Context`：请求的上下文，其中包含客户端的会话。
* `pointer或[]byte`：请求的有效载荷（_optional_）。

Notify Handler不返回任何内容，而Request Handler必须返回结果。
* `pointer或[]byte`: 响应的有效载荷。
* `error`：错误变量。


### 注册Handler组件

必须通过调用`pitaya.Register`方法来注册Handler组件的实例。
可以通过调用`pitaya/component`.WithName(`"handlerName"`)来定义Handler组件的名字，
可以通过使用`pitaya/component`.WithNameFunc(`func(string) string`)来重命名方法。

客户端可以通过调用`serverType.handlerName.methodName`来调用Handler。


### 路由消息

消息由pitaya转发到相应的服务器类型，可以通过调用`pitaya.AddRoute`为应用程序添加自定义路由器，它需要两个参数。

* `serverType`：要转发的目标请求的服务器类型。
* `routingFunction'：路由函数，签名为`func(*session.Session, *route.Route, []byte, map[string]*cluster.Server) (*cluster.Server, error)`，它接收用户的会话、请求的路由、消息和给定类型的有效服务器的地图，关键是服务器的ID。

然后，服务器将使用路由功能，当路由请求到给定的服务器类型。


### 生命周期方法

Handler可以选择性地实现以下生命周期方法： 

* `Init()` - Pitaya在初始化应用程序时调用。
* `AfterInit()` - Pitaya在初始化应用程序后调用。
* `BeforeShutdown()` -- -- Pitaya在关闭组件时调用，但在调用关闭之前调用。
* `Shutdown()` -- -- 在关闭开始后由Pitaya调用。


### Handler example

下面是一个非常简单的处理程序定义的例子，完整的工作例子，请查看[集群演示](../examples/demo/cluster)

```go
import (
  "github.com/topfreegames/pitaya"
  "github.com/topfreegames/pitaya/component"
)

type Handler struct {
  component.Base
}

type UserRequestMessage struct {
  Name    string `json:"name"`
  Content string `json:"content"`
}

type UserResponseMessage {
}

type UserPushMessage{
  Command string `json:"cmd"`
}

// 在服务初始化时调用此函数（不一定要实现此方法）
func (h *Handler) Init() {}

// 在初始化结束后调用此函数（不一定要实现此方法）
func (h *Handler) AfterInit() {}

// TestRequest can be called by the client by calling <servertype>.testhandler.testrequest
func (h *Handler) TestRequest(ctx context.Context, msg *UserRequestMessage) (*UserResponseMessage, error) {
  return &UserResponseMessage{}, nil
}

func (h *Handler) TestPush(ctx context.Context, msg *UserPushMessage) {
}

func main() {
  pitaya.Register(
    &Handler{}, // struct to register as handler
    component.WithName("testhandler"), // name of the handler, used by the clients
    component.WithNameFunc(strings.ToLower), // naming conversion scheme to be used by the clients
  )

  ...
}

```
## Remotes

Remotes是Pitaya的核心功能之一，它们是负责从其他Pitaya服务器接收RPC的实体。

###签名

Remotes必须是struct的公共方法，并且有如下签名。

参数
* `context.Context`：请求的上下文。
* `proto.Message`：请求的有效载荷（_optional_）。

远端方法必须返回： 
* `proto.Message`: 请求的响应。
* `proto.Message`: protobuf格式的响应有效载荷。
* `error`：错误变量。


### 注册Remotes

应用程序必须使用远程组件的实例调用`pitaya.RegisterRemote`明确注册远程组件。远程的名称可以通过调用`pitaya/component`.WithName(`"remoteName"`)来定义，方法可以通过使用`pitaya/component`.WithNameFunc(`func(string) string`)来重命名。

服务器可以通过调用`serverType.remoteName.methodName`来调用Remotes。


### RPC调用

在服务器之间发送 RPC 时，有两个选项。
* **只指定服务器类型**。在这种情况下，Pitaya将随机选择一个可用的服务器。
* **指定服务器类型和ID**。在这种情况下，Pitaya将向指定的服务器发送RPC。


### 生命周期方法

Remotes可以选择性地实现以下生命周期方法： * `Init()` - Pitaya在初始化应用程序时调用。

* `Init()` - Pitaya在初始化应用程序时调用。
* `AfterInit()` - Pitaya在初始化应用程序后调用。
* `BeforeShutdown()` -- -- Pitaya在关闭组件时调用，但在调用关闭之前调用。
* `Shutdown()` -- -- 在关闭开始后由Pitaya调用。

### Remotes示例

完整的工作示例，请查看[集群演示](https://github.com/topfreegames/pitaya/tree/master/examples/demo/cluster) 。
