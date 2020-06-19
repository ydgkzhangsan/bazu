# SpringMVC

## 介绍

> Spring 为展现层提供的基于 MVC 设计理念的优秀的 
>
> Web 框架，是目前最主流的 MVC 框架之一 Spring3.0 后全面超越 Struts2，成为最优秀的 MVC 框架
>
>  Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请求的控制器，而无须实现任何接口。 
>
> 支持 REST 风格的 URL 请求
>
> 采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性

### MVC

是软件工程中的软件架构，**并不是设计模式**。

MVC： M（model,模型） V （view,视图） C （controller, 控制器）

模型用于保存模型数据

视图用于显示数据

控制器用户处理请求，业务

## HelloWorld

- 创建一个工程

- 导入相关jar包（如果使用的是idea下载的方式，会自动下载到你自己的工程中，只需调整jar包位置即可）

  ![./images/snipaste20200618_095924.png](images/snipaste20200618_095924.png)

- 需要编写配置文件

  - web.xml配置文件 （如果使用IDEA下载的方式创建的项目会自动生成配置文件）

    ```XML
    <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <servlet>
      <servlet-name>dispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:dispatcherServlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
      <servlet-name>dispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```

  - 编写 SpringMVC 的核心配置文件

    - 配置自动扫描的包

      ```XML
      <!--
          配置自动扫描的包
          会自动扫描bask-package指定的包下被指定注解修饰的类，扫描到之后，会将其添加到IOC容器中
      -->
          <context:component-scan base-package="com.ydkg.springmvc"></context:component-scan>
      ```

    - 配置视图控制器

      ```XML
      <!--
              配置视图控制器
              通过XML的方式将SpringMVC中现有的类添加到IOC容器。
          -->
          <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
              <!--
                  为prefix指定值 前缀
              -->
              <property name="prefix" value="/WEB-INF/pages/" />
              <!--
                  为 suffix 指定值 后缀
              -->
              <property name="suffix" value=".jsp" />
          </bean>
      ```

- 编写控制器

  ```java
  @Controller
  public class HelloWorldController { 
      /*
      @RequestMapping 是用来映射请求的，如果浏览器发送的请求和RequestMapping中的值一致，就使用该注解
      标注的方法处理这个请求
       */
      @RequestMapping("/hello")
      public String hello(){
          System.out.println("HelloWorld!");
          return "success";
      }
  }
  ```

- 在WEB-INFO目录下创建一个success.jsp

  ![](./images/snipaste20200618_101809.png)

### 关于DispacherServlet 路径的配置

```Xml
<servlet>
  <servlet-name>dispatcher</servlet-name>
  <!--
            DispatcherServlet 是SpringMVC的核心
        -->
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <!--
            可以使用初始化参数指定 SpringMVC 核心配置文件的路径
            如果没有指定SpringMVC核心配置文件的路径，则默认是在WEB-INF目录下去找
            当前 <servlet-name> -servlet.xml文件作为SpringMVC的核心配置文件
        -->
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:dispatcher-servlet.xml</param-value>
  </init-param>
  <!--
            表示Servlet在web容器加载的时候就开始生效
        -->
  <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```



## HelloWorld原理

![](./images/snipaste20200618_103209.png)

## @RequestMapping

- 可以修饰在方法处，也可以修饰在类定义处

>  类定义处：提供初步的请求映射信息。相对于  WEB 应用的根目录–
>
>  方法处：提供进一步的细分映射信息。相对于类定义处的 URL。若– 类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于 WEB 应用的根目录

### 属性

- value : 指请求的url, 根据请求的url确定请求受理的方法

- method : 根据请求的方式确定请求受理的方法

  ```Java
  @RequestMapping(value = "/hello", method = {RequestMethod.POST})
  public String hello(){
    System.out.println("HelloWorld!");
    return "success";
  }
  ```

  注：如果请求方法不匹配会出现以下情况

  ![](./images/snipaste20200618_113023.png)

  **以下两个属性作为了解。**

- params : 根据请求携带的参数确定受理请求的方法

  ```Java
  /*
  params属性
              根据请求的参数确定请求受理的方法
              params属性是一个字符串类型的数组，支持一些简单的表达式 !  =  !=
  */
  @RequestMapping(value = "/testParam", params = {"name!=zhangsan","age"})
  public String testParam(){
    return "success";
  }
  ```

- headers : 根据请求头中携带的参数确定请求受理的方法

  ```Java
  /*
  headers 属性
              根据请求头中的参数确定请求受理的方法
              headers 属性是一个字符串类型的数组，支持一些简单的表达式 !  =  !=
  */
  @RequestMapping(value = "/testHeaders",headers = {"Accept-Language=zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7"})
  public String testHeaders(){
    return "success";
  }
  ```

## 统配符映射

```Java
/*
请求路径支持通配符映射
            使用一个通配符路径映射请求到受理请求的方法
            ? 表示匹配一个任意字符
            * 表示匹配一层路径中的任意多个字符
            ** 表示匹配多层路径中的任意字符
*/
@RequestMapping("/**/testAnt?")
public String testAnt(){
  return "success";
}
```

## @PathVariable 注解

>```
>@PathVariable 的使用
>    如果需要映射路径中的参数，
>    首先需要使用占位符将参数取名, 例如 {占位名}
>    其次需要在目标受理请求的入参中使用@PathVariable注解将参数绑定给方法入参
>```

```html
<body>
    <a href="/testPathVariable/2">Test PathVariable</a>
  </body>
```

```java
@RequestMapping("/testPathVariable/{id}")
public String testPathVariable(@PathVariable("id") Integer id){
  System.out.println("从路径中获取的参数为:" + id);
  return "success";
}
```

## Rest 请求

>  HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源

### 发送 PUT 及 DELETE 请求

- 配置好 HiddentHttpMethodFilter 过滤器

  ```xml
  <!--
          配置 HiddenHttpMethodFilter
              用于将浏览器发送的 POST 请求转换为 PUT 及 DELETE 请求
      -->
  <filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

- 需要发送一个 POST 请求

- 需要在 POST 请求中携带一个请求参数 _method 值是 PUT 或 DELETE

  ```HTML
  Delete 请求: <form method="post" action="/testRest">
    <input type="hidden" name="_method" value="DELETE" />
    <input type="submit" value="Test DELETE">
  </form>
  <hr>
  Put 请求：<form method="post" action="/testRest">
    <input type="hidden" name="_method" value="PUT" />
    <input type="submit" value="Test Put">
  </form>
  ```

## 获取请求参数

使用@RequestParam注解作为目标方法的入参，用于获取请求中的参数

属性：

- value 请求中参数的名称 

  注： 如果，请求中的参数和目标方法的入参名一致，可以省略@RequestParam注解

- required 表示参数是否是必须的

- defaultValue 参数的默认值

  **出现以下异常的原因为，没有传递相应的参数**

![](images/snipaste20200618_152814.png)



## @RequestHeader

获取请求报文中的参数。使用方式同@RequestParam

```Java
@RequestMapping("/testRequestHeader")
public String testRequestHeader(@RequestHeader("Accept-Language") String lg,
                                @RequestHeader("Cookie") String cookie){
  System.out.println("lg = " + lg);
  System.out.println("cookie = " + cookie);
  return "success";
}
```

## @CookieValue

获取Cookie中的数据， 使用方式同@RequestParam

```Java
@RequestMapping("/testCookieValue")
public String testCookieValue(@CookieValue("JSESSIONID") String jid){
  System.out.println("jid = " + jid);
  return "success";
}
```

## 使用普通的Java类作为入参

> 可以使用普通的java类作为方法的入参。会将请求参数中和Java类属性名称相同的参数赋值为对应的属性

```Java
@RequestMapping("/testPojo")
public String testPojo(Person person){
  System.out.println("person = " + person);
  return "success";
}
```

前台表单：![](images/snipaste20200618_155646.png)

打印结果：![](images/snipaste20200618_155735.png)

## 使用原生的ServletAPI

可以在目标受理请求的方法入参中加入原生的ServletAPI作为入参，即可使用原生ServletAPI

支持的ServletAPI有

- HttpServletRequest
- HttpServletResponse
- HttpSession
- java.security.Principal
- Locale• InputStream
- OutputStream
- Reader
- Writer

### 原理：

在调用目标受理请求方法时，SpringMVC会判断参数中是否有指定原生ServletAPI类型的参数，如果有，就传入给目标方法，所以目标方法即可使用了

```Java
if (!ServletRequest.class.isAssignableFrom(parameterType) && !MultipartRequest.class.isAssignableFrom(parameterType)) {
  if (ServletResponse.class.isAssignableFrom(parameterType)) {
    this.responseArgumentUsed = true;
    nativeResponse = webRequest.getNativeResponse(parameterType);
    if (nativeResponse == null) {
      throw new IllegalStateException("Current response is not of type [" + parameterType.getName() + "]: " + response);
    } else {
      return nativeResponse;
    }
  } else if (HttpSession.class.isAssignableFrom(parameterType)) {
    return request.getSession();
  } else if (Principal.class.isAssignableFrom(parameterType)) {
    return request.getUserPrincipal();
  } else if (Locale.class == parameterType) {
    return RequestContextUtils.getLocale(request);
  } else if (InputStream.class.isAssignableFrom(parameterType)) {
    return request.getInputStream();
  } else if (Reader.class.isAssignableFrom(parameterType)) {
    return request.getReader();
  } else if (OutputStream.class.isAssignableFrom(parameterType)) {
    this.responseArgumentUsed = true;
    return response.getOutputStream();
  } else if (Writer.class.isAssignableFrom(parameterType)) {
    this.responseArgumentUsed = true;
    return response.getWriter();
  } else {
    return super.resolveStandardArgument(parameterType, webRequest);
  }
```

snipaste20200618_165323.png

## 处理模型数据

### 处理模型数据之一

使用ModelAndView作为受理请求方法的返回值

```Java
/*
    处理模型数据之一：
        使用 ModelAndView 作为目标受理请求方法的返回值时，可以向ModelAndView中添加模型数据，
    添加的模型数据会自动的输出到Request域对象中
     */
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
  ModelAndView model = new ModelAndView("success");
  model.addObject("school","yuandagaoke");
  return model;
}
```

### 处理模型数据之二

使用 **Map**、HashMap、 LindedHashMap、ModeMap、ExtendedModeMap、Model作为方法入参

```Java
 /*
    处理模型数据之二：
       使用 Map 或 Model 作为目标方法的入参  非常重要

       因为SpringMVC为我们传入的参数为 BindingAwareModeMap
       所以可以使用它的父类接受这个参数，HashMap LindedHashMap、ModeMap、
       ExtendedModeMap都可以作为受理请求方法入参来处理模型数据
     */
@RequestMapping("/testModel")
public String testModel(Model model){
  model.addAttribute("school","ydgk");

  return "success";
}
```

### 处理模型数据之三

**使用 @SessionAttributes 注解将模型中的数据暂存到 HttpSession 中**

```Java
@Controller
/*
    @SessionAttributes 注解可以将模型中的数据暂存到 HttpSession 中
        value 属性是根据模型中数据的键名称暂存的
        types 属性模型中数据的类型暂存
 */
@SessionAttributes(value = {"user"}, types = {String.class})
public class DispatcherController {

    @RequestMapping("/testSessionAttribute")
    public String testSessionAttribute(Map map){
        map.put("user",new User(1,"zhangsan","zhangsan@qq.com",18));
        map.put("school", "ydgk");
        return "success";
    }
}
```



### 处理模型数据之四

使用 @ModelAttribute 注解

- **在方法定义上使用 @ModelAttribute 注解：Spring MVC在调用目标处理方法前，会先逐个调用在方法级上标注了 @ModelAttribute 的方法。** 


- 在方法的入参前使用 @ModelAttribute 注解：
  - 可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数
  - 绑定到对象中，再传入入参 将方法入参对象添加到模型中

```Java
/*
    @ModelAttribute 注解可以修饰方法
        修饰方法时，会在任何目标受理请求方法调用之前调用
        在 ModelAttribute注 解修饰的方法中可以向模型中存入数据，
        存入的数据可能会作为前台数据存入的模板

    注意： SpringMVC 在调用受理请求方法之前会在模型中取出参数类型首字母小写的数据，作为方法的入参。

    总结ModelAttribute注解作用流程：
        1、从数据库中取出 User , 将 User 存入模型对象中
        2、SpringMVC 一定在某个时刻将模型中的数据取出，作为接收前台表单中传入的值。
        3、SpringMVC将模型中的 user 作为方法的入参
     */
@ModelAttribute
public void getUser(@RequestParam("id") Integer id,
                    Map map){
  System.out.println("DispatcherController.getUser");
  if(id != null){
    //从数据库中取出对应的对象    并将对象存入模型中
    map.put("user",new User(1,"zhangsan","zhangsan@qq.com",18));
  }
}

@RequestMapping("/testModelAttribute")
public String testModelAttribute(User user){
  System.out.println("修改User为："+ user);
  return "success";
}
```

### 由@SessionAttributes 注解引发的异常

如果目标受理请求的方法入参是一个POJO时，SpringMVC会先自动从模型中取出POJO类型首字母小写的模型数据使用，如果模型中不存在对应的数据，则会判断受理请求方法所在类是否被@SessionAttributes 注解修饰，如果修饰了，就会从HttpSession取出POJO类型首字母小写的键对应的值，如果取不到，就会报以下异常。

![](images/snipaste20200619_102208.png)

## ModelAttribute注解源码解析

```Java
private WebDataBinder resolveModelAttribute(String attrName, MethodParameter methodParam, ExtendedModelMap implicitModel, NativeWebRequest webRequest, Object handler) throws Exception {
  		// 将方法传入的 attrName 赋值给name, 传入的 attrName 是由入参是否被 @ModelAttribute 注解的value属性决定
        String name = attrName;
  		// 如果参数没有被@ModelAttribute注解修饰
        if ("".equals(attrName)) {
          // 会根据参数类型的首字母小写确定 name
            name = Conventions.getVariableNameForParameter(methodParam);
        }

        Class<?> paramType = methodParam.getParameterType();
        Object bindObject;
  		// 判断模型中是否存在 name 对应的数据
        if (implicitModel.containsKey(name)) {
          	// 如果存在
            bindObject = implicitModel.get(name);
          // 如果不存在，继续判断受理请求方法所在类是否被@SessionAttributes注解修饰
        } else if (this.methodResolver.isSessionAttribute(name, paramType)) {
          // 如果被修饰了... SpringMvc会尝试从HttpSession中获取name对应的数据给到bindObject
            bindObject = this.sessionAttributeStore.retrieveAttribute(webRequest, name);
            if (bindObject == null) {
              // 如果在Session中依然没有取出对应的值，则抛出异常
                this.raiseSessionRequiredException("Session attribute '" + name + "' required - not found in session");
            }
          // 如果在模型中没有name对应的数据，并且方法所在类也没有被@SessionAttributes注解修饰
        } else {
          	// 直接通过反射创建新的实例作为创建WebDataBinder的参考
            bindObject = BeanUtils.instantiateClass(paramType);
        }
		// 通过确定bindObject 和 name 创建 webDataBinder  前台表单中传入的数据都是通过数据绑定器绑定的。
        WebDataBinder binder = this.createBinder(webRequest, bindObject, name);
        this.initBinder(handler, name, binder, webRequest);
        return binder;
    }
```





























