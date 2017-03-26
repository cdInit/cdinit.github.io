---
layout: post
title:  "servlet中forword请求转发小结"
categories: java
tags: java
author: Init
---

* content
{:toc}

servlet1执行完毕后重定向servlet2
语法：
  response.senRedirect(String path);
代码：两个servlet，由servlet1重定向（resp.sendRedirect("/send/s2");）到servlet2中，访问servlet1观察结果小结：
1）浏览器地址栏的路径发生改变，变化为目标资源路径。
2）发送了两个请求
3）因为是不同的请求，所以不会共享数据。比如servlet1中有id参数，servlet2中不能接收到
4）最终页面响应输出由servlet2来控制。
5）不加斜线找的是相对路径。常用绝对路径。
6）允许跨域跳转resp.sendRedirect("http://www.baidu.com");
7）不能访问WEB-INF中的资源

就相当于把目标的URL拷贝到浏览器然后访问



``` sh

@WebServlet("/send/s1")
public class SendRedirectServlet extends HttpServlet{
	private static final long serialVersionUID = 1L;

	@Override
	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		PrintWriter out = resp.getWriter();
		System.out.println("servlet1 log ...");
		out.print("servlet1...web...before<br>");
		//重定向
		resp.sendRedirect("/send/s2");
		System.out.println("servlet 1 ... after");
		out.print("servlet1...web...after");
		out.print("<br> servlet1...forword...after");
	}

}


```


``` sh

@WebServlet("/send/s2")
public class SendRedirectServlet2 extends HttpServlet{
	private static final long serialVersionUID = 1L;

	@Override
	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		PrintWriter out = resp.getWriter();
		System.out.println("servlet2 log ...");
		//请求转发
		out.print("servlet2...web...");
		out.print(req.getParameter("name"));
	}

}




```
