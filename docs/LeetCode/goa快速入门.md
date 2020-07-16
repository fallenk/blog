---
title: goa快速入门
date: 2018-11-30 19:20:57
tags: [Goa入门, 编程]
category: [golang学习]
---

# 简介
**配置环境，学习新东西请看官网教程，官网教程，官网教程！重要的事情强调三遍！！！！**
国内的博客坑实在太多了，耐心点，看英文官方教程！！

<!--more-->

## 代理
go get命令在提取一些工程或依赖时（如golang.org域名）被墙掉。通过使用vpn或代理的方法可以解决。

1. 买VPN
    这个就不多说了，买vpn，相当于直接连接。

2. 使用代理
    1. 通过shell环境变量

    `export http_proxy=http://ip:port`
    2. 通过设置git代理

    `git config –global http.proxy 'http://127.0.0.1:port'`
    
    如果是ssh代理上网，127.0.0.1+ 代理端口号。

## go命令
go build 加上可以编译的go源文件可以得到一个可执行文件。
go install 在编译源代码之后还安装到指定的目录
go get 从指定源上面下载或者更新指定的代码和依赖，并对他们进行编译和安装

go build : 编译出可执行文件
go install : go build + 把编译后的可执行文件放到GOPATH/bin目录下
go get : git clone + go install

-------
以上是一些准备工作。介绍goa, **目的 + 结构 + 使用**
[gao](https://github.com/goadesign/goa)是基于微服务的go语言框架，能够有效帮助开发人员**快速开发基于微服务的系统**。它通过**DSL和代码生成器**来生成样**板代码和辅助套件**(如文档，客户端模块，客户端工具等)。这些生成数据均基于服务的设计描述，goa遵循单一数据源(Single Source of Truth, SSOT)原则，任何对设计的改变，都将自动反映到系统各处。

goa可以分为三个部分：

- **goa的设计语言是内置DSL**，用于描述微服务的设计
- **goa代码生成器**，用于根据DSL描述生成代码模块，辅助工具，和文档等
- goa利用生成代码和用户代码来**实现一个服务**，并提供一个完全可插拨的框架

goa的特点：

- **重视框架设计(Design-Based)**，将框架，文档，胶水代码和辅助工具作为一个整体来设计和描述
- 为用户生成了大量的代码(框架代码，胶水代码，测试代码，客户端工具等等)，上手快速
- **DSL，代码生成器**，用户代码均使用Go语言编写，并且前两者使用plugin实现，可以替换
- 基于微服务，对RESTful API有非常好的支持，方便构建更高效，易于扩展的HTTP服务器

# 二. 使用

## 1. 安装

1. 安装 golang
    按照golang官网配置go的环境变量；！！
2. Install goa and goagen:
    `go get -u github.com/goadesign/goa/...`
        
## 2. DSL服务设计 Design
The first thing to do when writing a goa service is to **describe the API using the goa design language**. Create a new directory under `$GOPATH/src` for the new goa service, for example `$GOPATH/src/cellar`. In that directory **create a design sub directory and the file `design/design.go`** with the following content:

```go
package design                                     // The convention consists of naming the design
                                                   // package "design"
import (
        . "github.com/goadesign/goa/design"        // Use . imports to enable the DSL
        . "github.com/goadesign/goa/design/apidsl"
)

var _ = API("cellar", func() {                     // API defines the microservice endpoint and
        Title("The virtual wine cellar")           // other global properties. There should be one
        Description("A simple goa service")        // and exactly one API definition appearing in
        Scheme("http")                             // the design.
        Host("localhost:8080")
})

var _ = Resource("bottle", func() {                // Resources group related API endpoints
        BasePath("/bottles")                       // together. They map to REST resources for REST
        DefaultMedia(BottleMedia)                  // services.

        Action("show", func() {                    // Actions define a single API endpoint together
                Description("Get bottle by id")    // with its path, parameters (both path
                Routing(GET("/:bottleID"))         // parameters and querystring values) and payload
                Params(func() {                    // (shape of the request body).
                        Param("bottleID", Integer, "Bottle ID")
                })
                Response(OK)                       // Responses define the shape and status code
                Response(NotFound)                 // of HTTP responses.
        })
})

// BottleMedia defines the media type used to render bottles.
var BottleMedia = MediaType("application/vnd.goa.example.bottle+json", func() {
        Description("A bottle of wine")
        Attributes(func() {                         // Attributes define the media type shape.
                Attribute("id", Integer, "Unique bottle ID")
                Attribute("href", String, "API href for making requests on the bottle")
                Attribute("name", String, "Name of wine")
                Required("id", "href", "name")
        })
        View("default", func() {                    // View defines a rendering of the media type.
                Attribute("id")                     // Media types may have multiple views and must
                Attribute("href")                   // have a "default" view.
                Attribute("name")
        })
})
```

上面的DSL主要用到的接口：

- API: 描述一个Service及其地址，协议规范等
- Resource: 定义一个资源及其一系列相关的操作(Action)，以及这些操作所共用的一些属性
- Action: 定义针对于某个资源的操作，包括方法(GET,POST等)，URL(可有多个)，参数(goa自动做类型检查，值检查等)等
- Response: 定义一个响应，包括响应模板和承载内容(payload)，在代码中决定调用那个响应模板
- MediaType: 定义Response返回的数据结构，一个Media可以有多个View，可在Response中指定返回的View

goa本身DSL设计是RESTful的，通过Go的匿名函数，提供了非常强大的描述能力，如参数定义，参数检查，传输媒体，响应模板等。goa**基于服务提供功能**，**每个API定义一个服务(Service)**，**每个服务有若干资源(Resource)**，**每个资源对应若干操作(Action)**，**每个操作(Action)有多种响应(Response)**，**每个响应可能返回不同媒介(Media)的不同视图(View)**。当然goa提供了更好的层级控制和继承关系(如上例，Response返回的视图继承于Resource中定义的默认媒介(BottleMedia)的默认视图(default))。更详细的DSL设计文档参考[goa dsl design](https://goa.design/design/overview/)和[goa dsl api](https://goa.design/reference/goa/design/apidsl)。

## 3. 生成代码
通过goa根据单个DSL文件，即可生成一整套框架代码：

```
cd src/cellar
goagen bootstrap -d cellar/design
```
goa会生成一堆代码，主要包括四个目录两个文件：

- app目录: 根据DSL，生成若干类，并将**底层的HTTP服务器和DSL中的资源，路由结合起来**
- client目录: 配套的client包，包含**对媒介类型的定义**，和**对请求响应的编解码**
- tool目录：根据client包生成的**控制台工具**，用于**模拟客户端发送请求**
- swagger目录：包含**对整个服务(API)的总体描述**(Json和Yaml格式)
- main.go文件：主文件，**挂载资源路由(BottleController)，启动服务**
- bottle.go文件：**bottle资源的逻辑处理**，即BottleController的Action实现

当改变DSL文件并再次用goagen生成代码时，goagen只会重新生成框架代码(app,client,tool,swagger)，而不会覆盖逻辑代码(main.go和bottle.go以及其它自定义文件)，做到框架与逻辑分离。

得到这些文件之后，我们直接编辑bottle.go，完善bottle资源的Action逻辑即可：

```go
// Show runs the show action.
func (c *BottleController) Show(ctx *app.ShowBottleContext) error {
	// BottleController_Show: start_implement

	// Put your logic here
    if ctx.BottleID == 0 {
        return ctx.NotFound()
    }

    bottle := app.GoaExampleBottle{
        ID : ctx.BottleID,
        Name : fmt.Sprintf("Bottle #%d", ctx.BottleID),
        Href : app.BottleHref(ctx.BottleID),
    }

	// BottleController_Show: end_implement
	return ctx.OK(&bottle)
}

```
至此，服务器就已经设计好了，剩下的HTTP Server，消息编解码，参数检查，路由，响应模板，甚至测试工具，gagen都已经为你做好了。

## 4. 运行和测试
运行服务器：

```
cd src/cellar
go build -o cellar
./cellar
2016/09/20 00:26:41 [INFO] mount ctrl=Bottle action=Show route=GET /bottles/:bottleID
2016/09/20 00:26:41 [INFO] listen transport=http addr=:8080
```

通过curl测试：

```
# 404 NOT FOUND
curl -i localhost:8080/bottles/0
# 200 一个有效的BottleMedia View
curl -i localhost:8080/bottles/1
# 400 无效参数 得到参数检查错误提示
curl -i localhost:8080/bottles/n
```

通过celler-cli工具测试：

```
cd src/cellar/tool/cellar-cli
go build -o cellar-cli
# 使用帮助
./cellar-cli 
# show bottle 命令的用法
./cellar-cli show bottle
# 发送HTTP请求 cellar-cli中集成了服务的地址信息
./cellar-cli show bottle /bottles/1
```

最终我们只写了**几十行的DSL和几行逻辑代码**，就得到了一个基于微服务，RESTful风格的HTTP服务器，**附以完整的客户端代码，测试工具**，甚至服务API描述。更关键的是，这一套环境是SSOT(Single Source of Truth)的，更改一份DSL服务描述文件，整个服务器底层代码，胶水代码，测试环境，甚至API描述都会重新生成(不会影响到已有的逻辑代码)，这让整个服务保持高度一致性和可控性。

最后，以一段[goa github](https://github.com/goadesign/goa)上的描述收尾：


> There are a number of good Go packages for writing modular web services out there so why build another one? Glad you asked! The existing packages tend to focus on providing small and highly modular frameworks that are purposefully narrowly focused. The intent is to keep things simple and to avoid mixing concerns.

> This is great when writing simple APIs that tend to change rarely. However there are a number of problems that any non trivial API implementation must address. Things like request validation, response media type definitions or documentation are hard to do in a way that stays consistent and flexible as the API surface evolves.

> goa takes a different approach to building these applications: instead of focusing solely on helping with implementation, goa makes it possible to describe the design of an API in an holistic way. goa then uses that description to provide specialized helper code to the implementation and to generate documentation, API clients, tests, even custom artifacts.

完整示例参考goa learn guide和goa github.

# 三. 总结
goa的优点：

- 先进的理念：Design-Based, DSL, Micro-Service, RESTful API，Plugins等
- DSL，代码生成器，用户代码，辅助工具等一整套环境都用Go实现
- **一份服务设计(DSL文件)，生成了包括框架代码，辅助(胶水)代码，测试代码，客户端工具等一整套环境**(SSOT)
- 文档齐全，社区活跃度高

