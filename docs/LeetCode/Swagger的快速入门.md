---
title: Swagger的快速入门
date: 2018-11-28 14:58:02
tags: [Swagger, 入门]
category: [golang学习]
---
# 简介
Swagger：*REST APIs文档生成工具*。Swagger 是一个规范和完整的框架，用于**生成、描述、调用和可视化 RESTful 风格的 Web 服务**。

是一种API设计工具，能更好地实现前后端分离：[Swagger - 前后端分离后的契约](https://link.jianshu.com/?t=http://www.cnblogs.com/whitewolf/p/4686154.html)。

<!--more-->

一些环境部署与搭建：

[让接口测试成为合格的桥梁——本地搭建 Swagger-UI 环境搭建](https://link.jianshu.com/?t=https://testerhome.com/topics/8168)

[Swagger入门教程](https://link.jianshu.com/?t=http://www.cnblogs.com/JoiT/p/6378086.html)
主要使用`swagger-ui` 和 `swagger-editor`。`swagger-ui`展示`swagger-editor`生成的api文档，api文档格式可以是`yaml或json`。（感觉是swagger-ui可以在线测试使用，交互；swagger-editor只是生成文档）

## 有关Swagger的一些个人理解：
Swagger是REST APIs文档生成工具。

主要的工作量应该在于Swagger文档的编写，而文档的编写需要遵循一定的规范，即`Swagger API Spec`。`Swagger API Spec`是Swagger用来描述`REST API`的语言，可以用YAML和JSON表示。

Swagger文档的编写可以采用以下方式：

（1）手动编写Swagger文档。要对SwaggerSpec很熟悉。可以利用在线编辑器Swagger editor（http://editor.swagger.io/#/）验证YAML格式的内容是否违反Swagger spec，Swagger editor会标出错误并且给予格式提醒。所以说手动编写要对YAML或者JSON语法非常熟悉。但是这种方法相对好上手。

（2）Swagger文档应该可以从代码注释中自动生成。目前在参考这个教程：http://michal.karzynski.pl/blog/2016/06/19/building-beautiful-restful-apis-using-flask-swagger-ui-flask-restplus/?utm_source=tuicool&utm_medium=referral。因为对API和FLASK等相关内容不太熟悉，还在学习中。

Swagger-UI是一套HTML/CSS/JS框架用于解析遵守Swagger spec的JSON或YML文件，展示swagger-editor生成的API文档，还可以在其中调试API。这是官方demo的地址：http://petstore.swagger.io/。

Swagger-UI界面也可以进行修改，包括进行中文配置等。

下面是一个比较好的Swagger-UI说明：[Swagger-UI用户手册](http://www.cnblogs.com/baiyunchen/p/5895513.html) 。

# FLASK
[使用python的Flask实现一个RESTful API服务器端](https://link.jianshu.com/?t=http://www.cnblogs.com/vovlie/p/4178077.html)

1. 近些年来，REST成为web services和APIs的标准架构。
2. python的Flask框架可以轻松实现一个RESTful服务。

## Flask python web框架。
1. Flask很简单，micro-framework，所有有很多extensions配合使用，可以实现更多的功能。
2. Flask-RESTPlus：更方便地搭建REST APIs。**核心功能是，可以结合Swagger UI自动生成交互性的API文档**。
3. API的组织方式： `API namespaces`， `RESTful resources` 和 `HTTP methods`。
4. `Namespaces`：允许API定义划分为多个文件，每一个是API的一部分，用于不同的 URL prefix（前缀）。
5. `RESTful resources`：组织API成不同的endpoints（与不同的数据类型有关）

-------
另外需要搞懂的一些点
# 1. RESTful API
先理解API接口！！！
[想问一下什么是API接口，具体是什么意思？ -- 知乎](https://www.zhihu.com/question/38594466)

API（Application Programming Interface，应用程序编程接口）是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。
简单的说，就是通过某一预先定义的渠道读/写数据的方式。

下面这篇算是解决了我的困惑==

[利用「接口」做产品时我们该如何思考？](http://www.woshipm.com/pd/200895.html)

-------
一般接口包含以下几个内容：

- 接口地址：顾名思义就是接口的地址，以网址的形式展现，你通过发送请求给这个网址来对接口进行交互操作。
- 请求方法：
- 请求参数：即传输参数的时候要带的一些参数，一般文档中会用表格的形式清晰的说明。当我向接口发送携带请求参数的请求时，都要携带什么字段，规则是什么。
- 返回内容：返回内容一般会以json或是XML的形式返回。
- 错误代码:

REST -- REpresentational State Transfer 直接翻译：表现层状态转移。

[怎样用通俗的语言解释REST，以及RESTful？--知乎](https://www.zhihu.com/question/28557115)



REST描述的是在网络中client和server的一种交互形式；REST本身不实用，实用的是如何设计 RESTful API（REST风格的网络接口）；【其实是要设计一种合适的API，RESTful的】

[理解RESTful架构](https://link.jianshu.com/?t=http://www.ruanyifeng.com/blog/2011/09/restful.html)

总结一下什么是RESTful架构：

（1）每一个URI代表一种资源；

（2）客户端和服务器之间，传递这种资源的某种表现层；

（3）客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。

- 总结： URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。
    - 看Url就知道要什么
    - 看http method就知道干什么
    - 看http status code就知道结果如何

-------
简洁版 

- 0. REST不是"rest"这个单词，而是几个单词缩写。但即使那几个单词说出来，也无法理解在说什么 -_-!! （不是要贬低人，是我自己也理解困难）；
- 1. REST描述的是在网络中client和server的一种交互形式；REST本身不实用，**实用的是如何设计 RESTful API（REST风格的网络接口**）；
- 2. Server提供的RESTful API中，URL中只使用**名词来指定资源**，原则上不使用动词。“资源”是REST架构或者说整个网络处理的核心。
    - 比如：http://api.qc.com/v1/newsfeed: 获取某人的新鲜; 
    - http://api.qc.com/v1/friends: 获取某人的好友列表;
    - http://api.qc.com/v1/profile: 获取某人的详细信息;
- 3. 用HTTP协议里的动词来实现资源的添加，修改，删除等操作。
- 即通过HTTP动词来实现资源的状态扭转：1.GET-用来获取资源，POST-用来新建资源（也可以用于更新资源），PUT-用来更新资源，DELETE-用来删除资源。
    - 比如：DELETE http://api.qc.com/v1/friends: 删除某人的好友 （在http parameter指定好友id）
    - POST http://api.qc.com/v1/friends: 添加好友
    - UPDATE http://api.qc.com/v1/profile: 更新个人资料
    - 禁止使用： GET http://api.qc.com/v1/deleteFriend 图例：
- 4. Server和Client之间传递某资源的一个表现形式，比如用JSON，XML传输文本，或者用JPG，WebP传输图片等。当然还可以压缩HTTP传输时的数据（on-wire data compression）。
- 5. 用 HTTP Status Code传递Server的状态信息。比如最常用的 200 表示成功，500 表示Server内部错误等。
- 主要信息就这么点。最后是要解放思想，Web端不再用之前典型的PHP或JSP架构，而是改为前段渲染和附带处理简单的商务逻辑（比如AngularJS或者BackBone的一些样例）。
- Web端和Server只使用上述定义的API来传递数据和改变数据状态。格式一般是JSON。iOS和Android同理可得。由此可见，Web，iOS，Android和第三方开发者变为平等的角色通过一套API来共同消费Server提供的服务。

# 2. Mock server

