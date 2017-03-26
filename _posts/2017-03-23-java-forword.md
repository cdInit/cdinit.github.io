---
layout: post
title:  "servlet中forword请求转发小结"
categories: java
tags: java
author: Init
---

* content
{:toc}

代码：两个servlet，由servlet1请求转发（req.getRequestDispatcher("/forword/s2").forward(req, resp);）到servlet2中，访问servlet1观察结果小结：
1）浏览器地址栏的路径没有发生改变。依然是servlet1的资源名称
2）只发送了一次请求
3）共享同一个请求，请求中共享数据。比如servlet1中有id参数，servlet2中也能接收到
4）最终页面响应输出由servlet2来控制。
5）req.getRequestDispatcher("/forword/s2").forward(req, resp)中不加斜线req.getRequestDispatcher("forword/s2").forward(req, resp)找的是相对路径。常用绝对路径。
6）只能访问当前应用中的资源，不能跨域跳转
7）浏览器不能直接访问WEB-INF中的资源，但是可以通过请求转发的方式访问。
请求用的servlet1的请求，响应用的servlet2的响应




``` sh

@WebServlet("/forword/s1")
public class ForwordServlet extends HttpServlet{
	private static final long serialVersionUID = 1L;
	@Override
	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		PrintWriter out = resp.getWriter();
		System.out.println("servlet1 log ...");
		out.print("servlet1...web...before<br>");
		//请求转发
		req.getRequestDispatcher("/forword/s2").forward(req, resp);
		System.out.println("servlet 1 ... after");
		out.print("servlet1...web...after");
		out.print("<br> servlet1...forword...after");
	}
}


```


``` sh

@WebServlet("/forword/s2")
public class ForwordServlet2 extends HttpServlet{
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
