---
layout: post
title: 基于servlet、bootstrap、ajax的模拟用户增删改查
description: 刚刚接触bootstrap前端框架做的一个demo
category: blog
---

效果图：<br>
![004](/images/bootstrap/004.png)
<br>
<br>

一、环境搭建
-
<br>
1.下载 Bootstrap，将压缩包中的文件夹放入WebContent目录下<br>
2.搭建 Bootstrap 的基本的模板
![001](/images/bootstrap/001.png)
3.导入依赖JQuery插件的jar包
![002](/images/bootstrap/002.png)
<br>
<br>

二、目录结构
-
<br>
![003](/images/bootstrap/003.png)
<br>
<br>

三、具体代码
-
<br>
table.jsp
```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>

<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
<script type="text/javascript" src="js/bootstrap.js"></script>
<link href="css/bootstrap.css" rel="stylesheet">

<script type="text/javascript">
function load(){
	$.ajax({
		url:'UserServlet',
		type:'post',
		data:{'type':'login'},
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

function add(){
	$.ajax({
		url:'UserServlet',
		type:'post',
		data:{'type':'add','name':document.getElementById("name").value,'age':document.getElementById("age").value,'school':document.getElementById("school").value,'id':document.getElementById("id").value},
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

function del(id){
	$.ajax({
		url:'UserServlet',
		type:'post',
		data:{'type':'del','idi':id},
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

function update(id){
	$.ajax({
		url:'UserServlet',
		type:'post',
		data:{'type':'update','name':document.getElementById("name").value,'age':document.getElementById("age").value,'school':document.getElementById("school").value,'id':document.getElementById("id").value,'idi':id},
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

function getid(id) { 

	if(id=="add"){
		$("b").html("<div class='modal-header'><button type='button' class='close' data-dismiss='modal' aria-label='Close' id='add'><span aria-hidden='true'>&times;</span></button><h4 class='modal-title' id='myModalLabel'>添加信息</h4></div><div class='modal-body'><form action='' method='post'>编号：<input type='text' name='id' id='id'><br><br>姓名：<input type='text' name='name' id='name'><br><br>年龄：<input type='text' name='age' id='age'><br><br>学校：<input type='text' name='school' id='school'><br><br></form></div><div class='modal-footer'><button type='button' class='btn btn-default' data-dismiss='modal'>关闭</button><button type='button' class='btn btn-primary' data-dismiss='modal' onclick='add();'>确定</button></div>");
	} else if(id.slice(0,3)=="del")
	{
		$("b").html("<div class='modal-header'><button type='button' class='close' data-dismiss='modal' aria-label='Close' id='add'><span aria-hidden='true'>&times;</span></button><h4 class='modal-title' id='myModalLabel'>删除信息</h4></div><div class='modal-body'>是否删除？</div><div class='modal-footer'><button type='button' class='btn btn-default' data-dismiss='modal'>关闭</button><button type='button' class='btn btn-primary' data-dismiss='modal' onclick='del("+id.slice(3,4)+");'>确定</button></div>");
	}else if(id.slice(0,6)=="update")
	{
		$("b").html("<div class='modal-header'><button type='button' class='close' data-dismiss='modal' aria-label='Close' id='add'><span aria-hidden='true'>&times;</span></button><h4 class='modal-title' id='myModalLabel'>修改信息</h4></div><div class='modal-body'><form action='' method='post'>编号：<input type='text' name='id' id='id'><br><br>姓名：<input type='text' name='name' id='name'><br><br>年龄：<input type='text' name='age' id='age'><br><br>学校：<input type='text' name='school' id='school'><br><br></form></div><div class='modal-footer'><button type='button' class='btn btn-default' data-dismiss='modal'>关闭</button><button type='button' class='btn btn-primary' data-dismiss='modal' onclick='update("+id.slice(6,7)+");'>确定</button></div>");
	}
} 

</script>
</head>
<body onload="load()">
<div class="container">
'
	<div class="row">
		<div class="col-lg-8" class="visible-desktop" >
			<p>

			</p>		
						<button type="button" class="btn btn-primary btn-md" data-toggle="modal" data-target="#myModal" id="add" onclick="return getid(this.id)" >
						  添加
						</button>							
						<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
						  <div class="modal-dialog" role="document">
						    <div class="modal-content">  
						        
						        <b>
						        
						        </b>
						      
						    </div>
						  </div>
						</div>				
			

		</div>
	</div>
</div>
</body>
</html>
```

User.java
```java
package com.qst.user;

public class User {

	private Integer id;
	private String name;
	private String age;
	private String school;
	
	public User(Integer id, String name, String age, String school) {
		super();
		this.id = id;
		this.name = name;
		this.age = age;
		this.school = school;
	}
	
	public User(String name, String age, String school) {
		super();
		this.name = name;
		this.age = age;
		this.school = school;
	}
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getAge() {
		return age;
	}
	public void setAge(String age) {
		this.age = age;
	}
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}
	
}
```

UserDAO.java
```java
package com.qst.dao;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import com.qst.user.User;

//使用全局静态里列表模拟数据库
public class UserDAO {

	public static List<User> list;
	//这里声明一个静态块，就是在类加载的时候，已经存入，优先于Main（）方法
	static {
		list = new ArrayList<>();
		list.add(new User(1,"张三","20","齐鲁"));
		list.add(new User(2,"张三2","21","齐鲁2"));
		list.add(new User(3,"张三3","22","齐鲁3"));
	}
}
```

UserServlet.java
```java
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setCharacterEncoding("UTF-8");
		List<User> list =  UserDAO.list; 
		User user;
		String type = request.getParameter("type");
		
		if(type.equals("login")) {	
			// 创建Jackson插件的ObjectMapper对象;
			ObjectMapper mapper = new ObjectMapper();
			// 将一个Java对象转换成JSON
			mapper.writeValue(response.getWriter(), list);
		}else if(type.equals("update")){
			//获取list下标
			//Integer listid = Integer.parseInt(request.getParameter("listid"));
			//获取修改的数据
			int i = Integer.parseInt(request.getParameter("idi"));
			Integer id = Integer.parseInt(request.getParameter("id"));
			String name = request.getParameter("name");
			String age = request.getParameter("age");
			String school = request.getParameter("school");
			user=new User(id,name,age,school);
			list.set(i, user);
			// 创建Jackson插件的ObjectMapper对象;
			ObjectMapper mapper = new ObjectMapper();
			// 将一个Java对象转换成JSON
			mapper.writeValue(response.getWriter(), list);
		}else if(type.equals("add")) {
			Integer id = Integer.parseInt(request.getParameter("id"));
			String name = request.getParameter("name");
			String age = request.getParameter("age");
			String school = request.getParameter("school");
			user=new User(id,name,age,school);
			list.add(user);
			// 创建Jackson插件的ObjectMapper对象;
			ObjectMapper mapper = new ObjectMapper();
			// 将一个Java对象转换成JSON
			mapper.writeValue(response.getWriter(), list);
		}else if(type.equals("del")) {
			//获取list下标
			int i = Integer.parseInt(request.getParameter("idi"));
			System.out.println(i);
			list.remove(i);
			// 创建Jackson插件的ObjectMapper对象;
			ObjectMapper mapper = new ObjectMapper();
			// 将一个Java对象转换成JSON
			mapper.writeValue(response.getWriter(), list);
		}
	}
```
