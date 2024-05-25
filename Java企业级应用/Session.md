## 1.Session会话技术介绍

**Session是服务器端的会话技术**，服务器会为每个浏览器开辟一块空间，这块内存空间使用HttpSession对象表示。由于内存空间HttpSession对象是每个浏览器独享的，所以多个用户在访问的时候，都可以将信息保存在HttpSession对象中。**每个HttpSession对象都有一个sessionId，服务器在创建或者获取HttpSession对象之后会将HttpSession对象的sessionId写到浏览器的Cookie中，Cookie的key是JSESSIONID，当浏览器再次访问服务器的时候会将名为JSESSIONID的Cookie带过来，服务器就可以通过sessionId找到对应的HttpSession对象**

Session的作用在同一次会话中实现多个Servlet之间数据共享，但是不同于Cookie，Session的会话数据保存在服务器端，而不是浏览器，当服务器关闭或重启之后Session的数据就没有了

ServletContext、HttpServletRequest、HttpSession都是域对象，域对象用于多个Servlet之间共享数据的，但是它们的作用域是不同的，因此就有各自不同的应用场景,但是因为它们都是存储在服务器端，所以当服务器关闭或重启之后域对象存储的数据就没有了。

*   HttpServletRequest的作用域是在一次请求中且必须结合请求转发实现多个Servlet之间共享数据

*   **HttpSession**的作用域是在一次会话中实现多个Servlet之间共享数据，用户打开浏览器之后，浏览不同的网页（背后会发出多个请求），**直到浏览器关闭**的这个过程叫会话，**一次会话的特点是有多个请求**

*   **ServletContext**的作用域是在服务器的当前项目中有效，可以实现在当前项目中实现共享数据

Session的应用场景

1.  保存用户信息

2.  验证码验证

## 2. Session实现会话数据共享

Session实现会话数据共享就是在多个Servlet之间共享数据

## 3.Session的执行原理和销毁时机

### 执行原理

```java
public HttpSession getSession(); //返回与此请求关联的当前回话，或者如果没有会话，则创建一个
```

当客户端执行request.getSession()方法，首先会判断请求的请求头中是否有名为JESSIONID的Cookie

1.  若没有，那么服务器就会创建HttpSession对象，并且将HttpSession对象的ID以Cookie的形式保存到浏览器，Cookie的name就是JESSIONID

2.  若有，那么服务器就会根据这个值在服务器中查找对应的HttpSession对象

这个保存在服务器中，浏览器关闭不会销毁HttpSession对象，只会删除Cookie（没有设置时间的情况下），没有JESSIONID，这样就会找不到HttpSession对象（这样会造成大量的重复HttpSession对象，就引出了Session的销毁）

### 销毁时机

1.  Session闲置30分钟之后销毁

2.  服务器关闭或者重启之后会销毁

3.  调用HttpSession对象的invalidate()方法

## 4.实现关闭浏览器还能获取Session

可以设置保存在浏览器端的名为JESSIONID的Cookie的maxAge()，由于Session在闲置30分钟后服务器会将其销毁，因此有效时间不能大于30分钟

