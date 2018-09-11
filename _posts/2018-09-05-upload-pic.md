---
layout: post
title: from表单上传图片,并且显示
description: jsp、servlet
category: blog
---

实现图片的上传、显示，限制文件大小和文件类型（例如不能为.exe、.bat等）
-
jsp:
```java
<body>
    <form action="FileServlet" method="post" enctype="multipart/form-data">
      <input type="file" name="file">
      <input type="submit" name="upload" value="上传">
    </form>
</body>
```

FileServlet:
```java
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	// 设置请求字符编码
        request.setCharacterEncoding("UTF-8");
        //设置响应字符编码
	response.setContentType("text/html;charset=utf-8");
        //获取响应字符输出流
	PrintWriter out=response.getWriter();
		
        //存储路径
        String savePath = request.getServletContext().getRealPath("/uploadFile");
        Part part = request.getPart("file");
       
        //获取HTTP头信息headerInfo
        String headerInfo = part.getHeader("content-disposition");
        //从HTTP头信息中获取文件路径然后获取文件名
        String filePath = headerInfo.substring(headerInfo.lastIndexOf("=") + 2, headerInfo.length() - 1);
        String fileName = filePath.substring(filePath.lastIndexOf("\\")+1);
    	//限制上传文件大小和限制文件类型不能为.exe、.bat 
    	boolean f = fileName.endsWith(".exe");
    	boolean f1 = fileName.endsWith(".bat");	
    	if(part.getSize()>1024*1024) {
    		out.print("<script language='javascript'>alert('文件太大，不能上传！');window.location='upload.jsp';</script>");
    	}else {
    		if(f!=true && f1!=true) {
        		part.write(savePath+"\\"+fileName);
        		out.print("<script language='javascript'>alert('上传成功！');</script>");
        	}else {
                out.print("<script language='javascript'>alert('文件类型不能为.exe或.bat！');window.location='upload.jsp';</script>");
        	}
    	}  	

    	//显示图片
    	out.print("<script language='javascript'> document.write(\"<img src='uploadFile/"+fileName+"'"+"height='120' weight='120' alt='' />\");</script>");
	}
```
