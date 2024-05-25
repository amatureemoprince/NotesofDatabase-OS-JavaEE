



## 1.Servlet的实现

动态web资源开发，技术有两种：Servlet 和 JSP ；

- 什么是Servlet开发

  *Servlet是[Sun公司](https://www.baidu.com/s?wd=Sun公司&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)提供的一门用于开发动态web资源的技术；*

- 如何用Servlet开发一个动态web资源（即如何编写一个servlet类）

  Sun公司在其 Api 中提供了一个Servlet接口，用户若想开发一个动态的Web资源（即开发一个java程序向浏览器输出数据），只需要完成以下两个步骤： 
  1、编写一个java类，实现Servlet接口 ；

  2、把开发好的java类部署到[服务器](https://www.baidu.com/s?wd=服务器&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)中 ；

## 2.向浏览器写数据

​	两种方式，字符流和字节流

​	字节流：

```Java
@WebServlet("/DataStreamServlet")
public class DataStreamServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        try {
            // 设置响应内容类型
            response.setContentType("text/plain");

            // 获取响应的输出流
            OutputStream outputStream = response.getOutputStream();

            // 向输出流写入数据
            outputStream.write("这是直接写入浏览器的数据".getBytes());

            // 关闭流
            outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

​	字符流：

```java
@WebServlet("/DataStreamServlet")
public class DataStreamServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        try {
            // 设置响应内容类型
            response.setContentType("text/plain");

            // 获取响应的输出流
            PrintWriter writer = response.getWriter();

            // 向输出流写入数据
            writer.write("这是直接写入浏览器的数据");

            // 关闭流
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 3.转发

### 3.1.请求转发

​	请求转发，地址栏资源名不会改变，并且数据共享，只请求一次

```java
@WebServlet("/forward")
public class forwardServlet extends HttpServlet {
    @Override
    protected void service(@NotNull HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		//请求转发
        req.getRequestDispatcher("/Servlet01").forward(req, resp);

		//重定向
        resp.sendRedirect("https://baidu.com");

    }
}

```

### 3.2.重定向

​	重定向，地址栏资源改变，数据不共享，请求两次

## 4.Cookie对象

​	Cookie是浏览器提供的一种技术，通过服务器可以将一些只用保存在客户端中的数据存在本地计算机中，这样是可以提高网页的处理效率的，而且减少了服务器的负担。但是这些数据是服务器保存在客户端的的，所以安全性很差。常见的是用用于网页中记住你的密码。

### 	4.1.Cookie的创建和发送

​			通过new Cookie(“name”, “value”);创建一个名为name，值为value的Cookie对象，通过response.addCookie(cookie);发送给客户端，并保存在客户端，当下次再访问时服务器再将Cookie带回服务器。

```Java
//创建Cookie对象
Cookie cookie = new Cookie("uname", "liujie");
//发送Cookie对象
response.addCookie(cookie);
```

###  	4.2.Cookie的获取

​			用下面的方法返回的是Cookie对象的数组，那么如果你想要指定的对象可以进行循环判断

```java
Cookie[] cookies = request.getCookies();
//得到的是客户端的所有Cookie对象
```

### 	4.3.Cookie的销毁

​			cookie中的默认的Maxage的值时-1，那么关闭浏览器就默认失效，与服务器是否关闭无关。

​	

## 5.Servlet的三大域对象

​	1.request作用域

​		在一次请求中有效，在请求转发有效（通过有数据的进行不中断转发都叫在一次请求中有效，无论是请求转发多少次）

​	2.session作用域

​		在一次会话中有效，可以有多次请求，无论是请求转发或者重定向都有效，session销毁后失效

​	3.servletContext作用域

​		在整个应用程序中有效，服务器关闭失效

## 6.Servlet的生命周期

（1）实例化——new：服务器第一次被访问时，加载一个Servlet容器，而且只会被加载一次。
（2）初始化——init：创建完Servlet容器后，会调用仅执行一次的init()初始化方法，用于初始化Servlet对象，无论多少台客户端在服务器运行期间访问都不会再执行init()方法。
（3）执行处理——service()方法：HttpServlet的抽象类提供了doGet()、doPost()……等方法。对应了request请求的发送方法，与之相匹配：
（4）销毁——destroy：在服务器关闭或重启时，Servlet会调用destroy方法来销毁，将Servlet容器标记为垃圾文件，让GC做回收处理。

加载和实例化阶段（loading and instantiation）：当 Servlet 容器启动或者第一次请求某个 Servlet 时，会加载并创建 Servlet 对象的实例。在此阶段，容器会调用 ServletContext 的 getServlet 方法来获取 Servlet 实例，并调用其 init 方法进行初始化。在 init 方法中，Servlet 可以进行一些初始化工作，如加载配置文件、建立数据库连接等。

就绪阶段（ready）：当 Servlet 初始化完成后，容器会将其放入就绪状态，表示它已经准备好处理客户端请求了。

请求处理阶段（request handling）：当客户端发起请求时，Servlet 容器会为每个请求创建一个新的线程，并调用 Servlet 的 service 方法处理请求。在 service 方法中，Servlet 可以读取请求数据、进行业务处理，并生成响应数据发送给客户端。

销毁阶段（destruction）：当 Servlet 容器关闭或者 Web 应用程序被卸载时，会调用 Servlet 的 destroy 方法，此时 Servlet 会执行一些清理工作，如关闭数据库连接、保存会话数据等。在销毁阶段结束后，Servlet 实例将被销毁并释放资源。

![img](https://img-blog.csdnimg.cn/img_convert/e75a5f17464ceb6d332fcb7525593be8.png)

------------------------------------------------

## 7.HttpSession对象

## 8.ServletContext对象

## 9.文件的上传和下载

- **上传：**	从本地上传至服务器

```Java
@MultipartConfig
@WebServlet("/uploadServlet")
public class uploadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //因为前端需要传中文到后端所以需要设置编码方式
        req.setCharacterEncoding("UTF-8");
        String name = req.getParameter("username");
        System.out.println(name);
        //得到文件
        Part part = req.getPart("file");
        //得到文件名
        String fileName = part.getSubmittedFileName();
        //获取想要存放文件的位置
        String realPath = req.getServletContext().getRealPath("/upload/");
        //存储文件
        part.write(realPath + fileName);
    }
}
```

@MultipartConfig在接受二进制的表单数据时必须写这个注释

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>上传文件</title>
</head>
<body>
<form action="uploadServlet" method="post" enctype="multipart/form-data">
    姓名：<input type="text" name="username"><br>
    文件：<input type="file" name="file"><br>
    <button>提交</button>
</form>
</body>
</html>
```

enctype="multipart/form-data"这个是将表单以二进制上传

需要说明的一点是在服务器中寻找将文件存放的位置，上面代码中的

**“/upload/”**这样的是在web根目录下找一个名为upload的文件

（在有些情况下在编译后的项目中不会出现这个文件，此时是找不到这个资源的，所以下载不下来，这时的解决情况就是在编译后的项目中自己创建一个upload项目，或者将编译后的项目删掉重新编译）

**‘‘/’’**这样的就是直接在项目中下载文件，这样是没有层次感的

**“/upload”**这个仍然是在目录下下载文件，但是会将下载的文件名前添加upload

- **下载：**	从服务器下载到本地

```Java
@WebServlet("/download")
public class DownloadServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //将请求进行UTF-8编码
        req.setCharacterEncoding("UTF-8");
        //获取文件的名字
        String name = req.getParameter("myFile");
        System.out.println("文件名为：" + name);
        //判断文件名是否有效
        if(name == null || name.isEmpty()){
            System.out.println("请输入文件名!");
            return ;
        }
        //得到这个/file/的完整路径，这样就可以在后面添加想要需找的文件，看是不是在这个服务器中
        String realPath = req.getServletContext().getRealPath("/file/");
        System.out.println("realPath:" + realPath);
        //将路径下的数据形成一个文件
        File file = new File(realPath + name);
        if(file.exists() && file.isFile()){
            //设置浏览器无法识别，这样就可以进行下载了
            resp.setContentType("application/x-msdownload");
            resp.setHeader("Content-Disposition", "attachment;filename=" + name);
            //获取文件输入流，从文件中输入到服务器内部
            InputStream in = new FileInputStream(file);
            //获取字节输出流
            ServletOutputStream out = resp.getOutputStream();
            byte[] bytes = new byte[1024];
            int lens = 0;
            while ((lens = in.read(bytes)) != -1){
                out.write(bytes, 0, lens);
            }
            in.close();
            out.close();
        }else{
            System.out.println("你要下载的文件不存在！");
        }
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>下载</title>
</head>
<body>
<h2>可识别文件</h2>
<a href="file/cup.jpg">杯子</a>
<a href="hello.jsp">页面</a>
<h2>不可识别文件</h2>
<a href="file/fsnotifier.exe">exe文件</a><br>
<hr>
<%--
从上面可知浏览器识别的不能下载，不能识别的就可以下载
想要无论什么都可下载的话，可以设置download属性
--%>
<h2>download属性</h2>
<a href="file/cup.jpg" download="cup.jpg"> 设置了download属性</a>
<form action="download">
    文件：<input type="text" name="myFile"><br>
    <button>下载</button>
</form>
</body>
</html>
```

## 10.Servlet中的Request对象

- Request.getParameter(“参数名”);获得参数的值；Request.getParameterValue(“参数名”);通常用于提交复选框的数据
- 一系列get方法，可以获得相应的信息
- Request.setCharacterEncoding(“编码方式（UTF-8）”);设置请求来时的编码方式
- Request.getRequestDispatcher(“想要跳转到的资源”).forward(req, resp);请求转发
- Request.setAttribute(“参数名”, “参数值”);设置一个Request域对象和对应的值，可以用于在跳转时传输数据
- Request.getAttribute(“参数名”);获得设置的参数值

## 11.Servlet中的Response对象

- PrintWriter writer = Response.getWriter();获得一个响应字符流，可以通过writer响应一段文字或代码之类的
- ServletOutputStream outStream = Response.getOutputStream();获得响应字节流，可以传输文件之类的数据
- Response.setContentType(“text/html; charset = UTF-8”);在响应时就用这个方法，分号前的表示响应回去的页面类型
- Response.sendRedirect(“想访问的资源”);重定向，跳转到想访问的资源