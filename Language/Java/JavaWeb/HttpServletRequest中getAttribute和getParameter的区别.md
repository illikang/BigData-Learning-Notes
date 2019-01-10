# HttpServletRequest中getAttribute和getParameter的区别

## 简单总结
1. 有setAttribute()方法，没有setParameter()方法。
2. getAttribute()返回的是Object类型，getParameter()返回的是String类型。
3. getParameter()方法传递的数据，会从Web客户端传到Web服务器端，代表HTTP请求数据；setAttribute()和getAttribute()方法传递的数据只会存在于Web容器内部，在具有转发关系的Web组件之间共享。即`request.getAttribute()方法返回request范围内存在的对象，而request.getParameter()方法是获取http提交过来的数据`。

## 详细说明
1. getParameter()用于获取POST/GET传递的参数值，用于获取页面中通过URL传递的参数和表单元素。比如，某个表单提交的数据，http://a.jsp?id=123中的123。用于客户端重定向时，即`点击了链接`或`提交按钮`是传值用。

2. getAttribute()是对你页面中自己定义的对象进行操作。在JSP的EL语句${username}就相当于getAttribute("username")。所以，当我们需要在页面上显示某数据时，就用setAttribute()把它放到request中。


## 实例
1. 当两个Web组件之间为链接关系时，被链接的组件通过getParameter()方法来获得请求参数。例如：
```
<a href="/authenticate.jsp?username=weiqin">authenticate.jsp </a>
```
或者
```
<form name="form1" method="post" action="authenticate.jsp">
   请输入用户姓名：<input type="text" name="username">
   <input type="submit" name="Submit" value="提交">
</form>
```
在authenticate.jsp中通过request.getParameter("username")方法来获得请求参数username:
```
<% String username=request.getParameter("username"); %>
```
2. 当两个Web组件之间为转发关系时，转发目标组件通过getAttribute()方法来和转发源组件共享request范围内的数据。假定authenticate.jsp和hello.jsp之间为转发关系。authenticate.jsp希望向hello.jsp传递当前的用户名字，如何传递这一数据呢？先在authenticate.jsp中调用 setAttribute()方法：
```
<%
String username=request.getParameter("username");
request.setAttribute("username"，username);
...
<jsp:forward page="hello.jsp" />
%>
```
在hello.jsp中通过getAttribute()方法获得用户名字：
```
<% String username=(String)request.getAttribute("username"); %>
Hello: <%=username %>
```
