# 列表



# 设计MVC框架

>  手撸一个简单的MVC框架；不用Spring框架，基于Servlet
>
>  https://github.com/fallenk/web-mvc



1. ModelAndView

去定义了上层代码编写Controller的一切接口信息，并且并不要求实现特定接口，只需返回`ModelAndView`对象，该对象包含一个`View`和一个`Model`。实际上`View`就是模板的路径，而`Model`可以用一个`Map<String, Object>`表示，因此，`ModelAndView`定义非常简单：

```java
public class ModelAndView {
    Map<String, Object> model;
    String view;
}
```

比较复杂的是我们需要在MVC框架中创建一个接收所有请求的`Servlet`，通常我们把它命名为`DispatcherServlet`，它总是映射到`/`，然后，根据不同的Controller的方法定义的`@Get`或`@Post`的Path决定调用哪个方法，最后，获得方法返回的`ModelAndView`后，渲染模板，写入`HttpServletResponse`，即完成了整个MVC的处理。



MVC的架构如下：

```ascii
   HTTP Request    ┌─────────────────┐
──────────────────>│DispatcherServlet│
                   └─────────────────┘
                            │
               ┌────────────┼────────────┐
               ▼            ▼            ▼
         ┌───────────┐┌───────────┐┌───────────┐
         │Controller1││Controller2││Controller3│
         └───────────┘└───────────┘└───────────┘
               │            │            │
               └────────────┼────────────┘
                            ▼
   HTTP Response ┌────────────────────┐
<────────────────│render(ModelAndView)│
                 └────────────────────┘
```

`DispatcherServlet`以及如何渲染均由MVC框架实现，在MVC框架之上只需要编写每一个Controller。



我们来看看如何编写最复杂的`DispatcherServlet`。首先，我们需要存储请求路径到某个具体方法的映射：

```
@WebServlet(urlPatterns = "/")
public class DispatcherServlet extends HttpServlet {
    private Map<String, GetDispatcher> getMappings = new HashMap<>();
    private Map<String, PostDispatcher> postMappings = new HashMap<>();
}
```

处理一个GET请求是通过`GetDispatcher`对象完成的，它需要如下信息：

```
class GetDispatcher {
    Object instance; // Controller实例
    Method method; // Controller方法
    String[] parameterNames; // 方法参数名称
    Class<?>[] parameterClasses; // 方法参数类型
}
```

有了以上信息，就可以定义`invoke()`来处理真正的请求, 逻辑非常简单，即通过构造某个方法需要的所有参数列表，使用反射调用该方法后返回结果。



类似的，`PostDispatcher`需要如下信息：

```
class PostDispatcher {
    Object instance; // Controller实例
    Method method; // Controller方法
    Class<?>[] parameterClasses; // 方法参数类型
    ObjectMapper objectMapper; // JSON映射
}
```

和GET请求不同，POST请求严格地来说不能有URL参数，所有数据都应当从Post Body中读取。这里我们为了简化处理，*只支持*JSON格式的POST请求，这样，把Post数据转化为JavaBean就非常容易。



最后，我们来实现整个`DispatcherServlet`的处理流程，以`doGet()`为例：

```
public class DispatcherServlet extends HttpServlet {
    ...
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");
        resp.setCharacterEncoding("UTF-8");
        String path = req.getRequestURI().substring(req.getContextPath().length());
        // 根据路径查找GetDispatcher:
        GetDispatcher dispatcher = this.getMappings.get(path);
        if (dispatcher == null) {
            // 未找到返回404:
            resp.sendError(404);
            return;
        }
        // 调用Controller方法获得返回值:
        ModelAndView mv = dispatcher.invoke(req, resp);
        // 允许返回null:
        if (mv == null) {
            return;
        }
        // 允许返回`redirect:`开头的view表示重定向:
        if (mv.view.startsWith("redirect:")) {
            resp.sendRedirect(mv.view.substring(9));
            return;
        }
        // 将模板引擎渲染的内容写入响应:
        PrintWriter pw = resp.getWriter();
        this.viewEngine.render(mv, pw);
        pw.flush();
    }
}
```

这里有几个小改进：

- 允许Controller方法返回`null`，表示内部已自行处理完毕；
- 允许Controller方法返回以`redirect:`开头的view名称，表示一个重定向。



`framework`包是MVC的框架，完全可以单独编译后作为一个Maven依赖引入。

效果如下



![mvc](https://www.liaoxuefeng.com/files/attachments/1337490191417409/l)

​	这个MVC框架，上层代码使用的公共类如`GetMapping`、`PostMapping`和`ModelAndView`都和Spring MVC非常类似。实际上，我们这个MVC框架主要参考就是Spring MVC，通过实现一个“简化版”MVC，可以掌握Java Web MVC开发的核心思想与原理，对将来直接使用Spring MVC是非常有帮助的。