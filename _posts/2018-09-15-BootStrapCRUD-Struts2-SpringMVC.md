---
layout: post
title: 基于Struts2和SpringMVC框架的bootstrap demo
description: 将上篇文章中的bootstrap demo更改为基于struts2和SpringMVC框架
category: blog
---

将上篇文章中的demo改为基于Struts2和SpringMVC框架的项目，具体改动不大，这里我就粗略讲解一下。[上篇文章地址](http://www.sunzhensky.top/Ajax-BootStrap-CRUD)

Struts2:
-
1.搭建Struts2环境<br>
2.将table.jsp中的url改为配置的相应地址<br>
![001](/images/bootstrap-struts2-springmvc/001.png)
3.将UserServlet.java相关步骤放入com.qst.action.UserAction类中相应的方法中<br>
UserAction.java:
```java
package com.qst.action;
.....
.....

public class UserAction extends ActionSupport implements ServletRequestAware{
	
	List<User> list =  UserDAO.list; 
	HttpServletRequest request;
	User user;
	
	public List<User> getList() {
		return list;
	}

	public void setList(List<User> list) {
		this.list = list;
	}

	public String login() {
		
		return SUCCESS;
	}
	 
	public String add() {
		
		Integer id = Integer.parseInt(request.getParameter("id"));
		String name = request.getParameter("name");
		String age = request.getParameter("age");
		String school = request.getParameter("school");
		user=new User(id,name,age,school);
		list.add(user);
		
		return SUCCESS;
	}

	public String update() {
		
		//获取修改的数据
		int i = Integer.parseInt(request.getParameter("idi"));
		Integer id = Integer.parseInt(request.getParameter("id"));
		String name = request.getParameter("name");
		String age = request.getParameter("age");
		String school = request.getParameter("school");
		user=new User(id,name,age,school);
		list.set(i, user);
		
		return SUCCESS;
	}
	
	public String del() {
		
		//获取list下标
		int i = Integer.parseInt(request.getParameter("idi"));
		System.out.println(i);
		list.remove(i);
		
		return SUCCESS;
	}
	@Override
	public void setServletRequest(HttpServletRequest request) {
	
		this.request = request;
	}

}
```
<br>
<br>

SpringMVC:
-
1.搭建SpringMVC环境<br>
2.将 table.jsp js中的方法进行相应更改，例如update()方法
```java
function update(id){
	$.ajax({
		url:'user/update?idi='+id,
		type:'post',
		data:JSON.stringify({"id":$("#id").val(),"name":$("#name").val(),"age":$("#age").val(),"school":$("#school").val()}),
		contentType:'application/json;charset=UTF-8',
		dataType:'json',
		success:function(data){
			
			var htmlstr="<table class='table table-bordered table-hover'><tr><th>编号</th><th>姓名</th><th>年龄</th><th>学校</th><th>操作</th></tr>";
			for(var i=0;i<data.length;i++){
				var user = data[i];			
				var str = "<tr><td>"+user.id+"</td><td>"+user.name+"</td><td>"+user.age+"</td><td>"+user.school+"</td><td><button type='button' class='btn btn-primary btn-md' data-toggle='modal' data-target='#myModal' id='update"+i+"' onclick='return getid(this.id)'>修改</button><button type='button' class='btn btn-primary btn-md' data-toggle='modal' data-target='#myModal' id='del"+i+"' onclick='return getid(this.id)' >删除</button></td></tr>"
				htmlstr+=str;
			}
			$("p").html(htmlstr+"</table>");
		},
		error:function(){
			alert('服务器执行错误！');
		}
	});
}
```
3.struts.xml（struts2与json数据交互需要导入 struts2-json-plugin jar包）
```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
	<constant name="struts.ui.theme" value="simple" />  
	<constant name="struts.i18n.encoding" value="UTF-8"></constant>
	<constant name="struts.devMode" value="true"></constant>

	<package name="helloworld" extends="struts-default,json-default" namespace="/">
		<global-allowed-methods>regex:.*</global-allowed-methods>
		<action name="UserAction_*" class="com.qst.action.UserAction" method="{1}"> 
			<result type="json"> 
				<!-- 这里指定将被Struts2序列化的属性，该属性在action中必须有对应的getter方法 -->
 				<param name="root">list</param> 
 			</result>
			
		</action>
	</package>
</struts>
```
4.在springmvc-config.xml中进行相关配置：
```java
	<!-- 自动扫描包，实现支持注解的IOC -->
	<context:component-scan base-package="com.qst.controller"/>
	<!-- 支持mvc注解的驱动 -->
	<mvc:annotation-driven/>
	<!-- Spring MVC不处理静态资源 -->
	<mvc:default-servlet-handler/>
```
4..将UserServlet.java相关步骤放入com.qst.controller.UserController类中相应的方法中。这里要注意将json字符串转换为json对象时，要导入gson.jar包<br>
UserController.java:
```java
package com.qst.controller;
.....
.....

@Controller
@RequestMapping("/user")
public class UserController{

	List<User> list =  UserDAO.list; 

	@ResponseBody
	@RequestMapping("/login")
	public List<User> login() {
		
		return list;
	}
		
	@RequestMapping("/add")
	@ResponseBody
	public List<User> add(@RequestBody User user) {

		list.add(user);
		return list;
	}
	
	@RequestMapping("/del")
	@ResponseBody
	public List<User> del(@RequestBody String idi) {
		
		JsonParser jp = new JsonParser();
		//将json字符串转化成json对象
    		JsonObject jo = jp.parse(idi).getAsJsonObject();
    		//获取idi对应的值(获取list下标)
    		int i = Integer.parseInt(jo.get("idi").getAsString());	
		list.remove(i);
		return list;
	}
	
	@RequestMapping("/update")
	@ResponseBody
	public List<User> update(@RequestBody User user,HttpServletRequest request) {

		int i = Integer.parseInt(request.getParameter("idi"));	

		list.set(i, user);
		return list;
	}
	
}
```
