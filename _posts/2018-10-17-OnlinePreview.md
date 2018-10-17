---
layout: post
title: java实现附件预览（openoffice+swftools+flexpaper）
description: 在线预览技术
category: blog
---

一、主要原理：
-
1.通过第三方工具openoffice,将word、excel、ppt、txt等文件转换为pdf文件<br>
2.通过swfTools将pdf文件转换成swf格式的文件<br>
3.通过FlexPaper文档组件在页面上进行展示
<br>

二、安装包下载
-
1.openoffice是Apache下的一个开放免费的文字处理软件<br>
2.SWFTools是一组用来处理Flash的swf文件的工具包,我们使用它将pdf文件转成swf文件<br>
3.FlexPaper是一个开源轻量级的在浏览器上显示各种文档的组件<br>
4.JODConverter一个Java的OpenDocument 文件转换器，在此我们只用到它的jar包<br>
[下载地址](https://pan.baidu.com/s/1YDeOgOMU-V0ayyIWUSYJqg)（提取码：9kfk）
<br>

三、开发过程
-
1.新建项目<br>
将flexpaper文件中的js文件夹(包含了flexpaper_flash_debug.js，flexpaper_flash.js,jquery.js,这三个js文件主要是预览swf文件的插件)拷贝至网站根目录;将FlexPaperViewer.swf拷贝至网站根目录下(该文件主要是用在网页中播放swf文件的播放器)。<br>
目录结构如下图：<br>
![001](/images/OnlinePreview/001.png)
注：需创建upload文件夹<br>

2.创建fileUpload.jsp<br>
```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>文档在线预览系统</title>
<style>  
    body {margin-top:100px;background:#fff;font-family: Verdana, Tahoma;}  
    a {color:#CE4614;}  
    #msg-box {color: #CE4614; font-size:0.9em;text-align:center;}  
    #msg-box .logo {border-bottom:5px solid #ECE5D9;margin-bottom:20px;padding-bottom:10px;}  
    #msg-box .title {font-size:1.4em;font-weight:bold;margin:0 0 30px 0;}  
    #msg-box .nav {margin-top:20px;}  
</style>

</head>
<body>
<div id="msg-box">  
    <form name="form1" method="post" enctype="multipart/form-data" action="docUploadConvertAction.jsp">  
        <div class="title">  
            请上传要处理的文件，过程可能需要几分钟，请稍候片刻。  
        </div>
        <p>  
            <input name="file1" type="file">  
        </p>
        <p>  
            <input type="submit" name="Submit" value="上传">  
        </p>  
    </form>  
</div>
</body>
</html>
```

3.创建docUploadConvertAction.jsp<br>
```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    <%@page import="java.io.*"%>
<%@page import="java.util.Enumeration"%>
<%@page import="com.oreilly.servlet.MultipartRequest"%>
<%@page import="com.oreilly.servlet.multipart.DefaultFileRenamePolicy"%>
<%@page import="com.qst.DocConverter"%>
<%
//文件上传采用cos组件上传，可更换为commons-fileupload上传，文件上传后，保存在upload文件夹  
//获取文件上传路径  
String saveDirectory =application.getRealPath("/")+"upload";
//打印上传路径信息  
System.out.println(saveDirectory);  
//每个文件最大50m  
int maxPostSize = 50 * 1024 * 1024 ;
//采用cos缺省的命名策略，重名后加1,2,3...如果不加dfp重名将覆盖  
DefaultFileRenamePolicy dfp = new DefaultFileRenamePolicy();
//response的编码为"UTF-8",同时采用缺省的文件名冲突解决策略,实现上传,如果不加dfp重名将覆盖 
MultipartRequest multi = new MultipartRequest(request, saveDirectory, maxPostSize,"UTF-8",dfp);
//MultipartRequest multi = new MultipartRequest(request, saveDirectory, maxPostSize,"UTF-8");
//输出反馈信息 
Enumeration files = multi.getFileNames();
while (files.hasMoreElements()) { 
    System.err.println("ccc");
    String name = (String)files.nextElement();  
    File f = multi.getFile(name);
    if(f!=null){
   	 	String fileName = multi.getFilesystemName(name);
        //获取上传文件的扩展名 
        String extName=fileName.substring(fileName.lastIndexOf(".")+1);
        //文件全路径
        String lastFileName= saveDirectory+"\\" + fileName;  
        //获取需要转换的文件名,将路径名中的'\'替换为'/'
        String converfilename = saveDirectory.replaceAll("\\\\", "/")+"/"+fileName;  
        System.out.println(converfilename);
      	//调用转换类DocConverter,并将需要转换的文件传递给该类的构造方法  
        DocConverter d = new DocConverter(converfilename);
      	//调用conver方法开始转换，先执行doc2pdf()将office文件转换为pdf;再执行pdf2swf()将pdf转换为swf;  
        d.conver();
      	//调用getswfPath()方法，打印转换后的swf文件路径  
        System.out.println(d.getswfPath());
      	//生成swf相对路径，以便传递给flexpaper播放器  
        String swfpath = "upload"+d.getswfPath().substring(d.getswfPath().lastIndexOf("/"));
        System.out.println(swfpath);  
        //将相对路径放入sessio中保存 
        session.setAttribute("swfpath", swfpath);  
        out.println("上传的文件:"+lastFileName);
        out.println("文件类型"+extName);  
        out.println("<hr>");
    }
}
%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
<style>  
    body {margin-top:100px;background:#fff;font-family: Verdana, Tahoma;}  
    a {color:#CE4614;}  
    #msg-box {color: #CE4614; font-size:0.9em;text-align:center;}  
    #msg-box .logo {border-bottom:5px solid #ECE5D9;margin-bottom:20px;padding-bottom:10px;}  
    #msg-box .title {font-size:1.4em;font-weight:bold;margin:0 0 30px 0;}  
    #msg-box .nav {margin-top:20px;}  
</style>
</head>
<body>
	<div>  
        <form name="viewForm" id="form_swf" action="documentView.jsp" method="POST">  
            <input type='submit' value='预览' class='BUTTON SUBMIT'/>  
        </form>  
    </div>
</body>
</html>
```

4.创建documentView.jsp
```java
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>  
<%
	String swfFilePath=session.getAttribute("swfpath").toString();
%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
<script type="text/javascript" src="js/jquery.js"></script>
<script type="text/javascript" src="js/flexpaper_flash.js"></script>
<script type="text/javascript" src="js/flexpaper_flash_debug.js"></script>
<style type="text/css" media="screen">
            html, body  { height:100%; }
            body { margin:0; padding:0; overflow:auto; }
            #flashContent { display:none; }
        </style>

<title>文档在线预览系统</title>
</head>
<body>   
        <div style="position:absolute;left:50px;top:10px;">  
            <a id="viewerPlaceHolder" style="width:820px;height:650px;display:block"></a>  
              
            <script type="text/javascript">   
                var fp = new FlexPaperViewer(     
                         'FlexPaperViewer',  
                         'viewerPlaceHolder', { config : {  
                         SwfFile : '<%=swfFilePath%>',  
                         Scale : 0.6,   
                         ZoomTransition : 'easeOut',  
                         ZoomTime : 0.5,  
                         ZoomInterval : 0.2,  
                         FitPageOnLoad : true,  
                         FitWidthOnLoad : false,  
                         FullScreenAsMaxWindow : false,  
                         ProgressiveLoading : false,  
                         MinZoomSize : 0.2,  
                         MaxZoomSize : 5,  
                         SearchMatchAll : false,  
                         InitViewMode : 'SinglePage',  
                           
                         ViewModeToolsVisible : true,  
                         ZoomToolsVisible : true,  
                         NavToolsVisible : true,  
                         CursorToolsVisible : true,  
                         SearchToolsVisible : true,  
                          
                         localeChain: 'en_US'  
                         }});  
            </script>              
        </div>  
</body>
</html>
```

5.创建转换类DocConverter.java
```java
package com.qst;

import java.io.BufferedInputStream;
import java.io.BufferedReader;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

import com.artofsolving.jodconverter.DocumentConverter;
import com.artofsolving.jodconverter.openoffice.connection.OpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.connection.SocketOpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.converter.OpenOfficeDocumentConverter;

/**
 * doc docx格式转换
 */
public class DocConverter {
	private static final int environment = 1;// 环境 1：windows 2:linux
	private String fileString;// (只涉及pdf2swf路径问题)
	private String outputPath = "";// 输入路径 ，如果不设置就输出在默认的位置
	private String OpenOffice_HOME = "C:/Program Files (x86)/OpenOffice 4/";// OpenOffice的安装目录
	private String fileName;
	private File pdfFile;
	private File swfFile;
	private File docFile;

	public DocConverter(String fileString) {
		ini(fileString);
	}

	/**
	 *重新设置file       
	 *        
	 * @param fileString       
	 */
	public void setFile(String fileString) {
		ini(fileString);
	}

	/**
	 * 初始化
	 * 
	 * @param fileString
	 */
	private void ini(String fileString) {
		this.fileString = fileString;
		fileName = fileString.substring(0, fileString.lastIndexOf("."));
		docFile = new File(fileString);
		pdfFile = new File(fileName + ".pdf");
		swfFile = new File(fileName + ".swf");
	}

	/**
	 * 转为PDF
	 * 
	 * @param file
	 */
	private void doc2pdf() throws Exception {
		if (docFile.exists()) {
			if (!pdfFile.exists()) {
				OpenOfficeConnection connection = new SocketOpenOfficeConnection(8100);
				try {
					connection.connect();
					DocumentConverter converter = new OpenOfficeDocumentConverter(connection);
					converter.convert(docFile, pdfFile);
					System.out.println("****pdf转换成功，PDF输出：" + pdfFile.getPath() + "****");
					// close the connection
					connection.disconnect();
				} catch (java.net.ConnectException e) {
					e.printStackTrace();
					System.out.println("****swf转换器异常，openoffice服务未启动！****");
					throw e;
				} catch (com.artofsolving.jodconverter.openoffice.connection.OpenOfficeException e) {
					e.printStackTrace();
					System.out.println("****swf转换器异常，读取转换文件失败****");
					throw e;
				} catch (Exception e) {
					e.printStackTrace();
					throw e;
				}
			} else {
				System.out.println("****已经转换为pdf，不需要再进行转化****");
			}
		} else {
			System.out.println("****swf转换器异常，需要转换的文档不存在，无法转换****");
		}
	}

	/**
	 * 转换成 swf
	 */
	@SuppressWarnings("unused")
	private void pdf2swf() throws Exception {
		Runtime r = Runtime.getRuntime();
		if (!swfFile.exists()) {
			if (pdfFile.exists()) {
				if (environment == 1) {// windows环境处理
					try {
						Process p = null;
						List command = new ArrayList();
						command.add("E:/OnlinePreview/SWFTools/pdf2swf.exe");
						command.add("\"" + pdfFile.getPath() + "\"");
						command.add("\"" + swfFile.getPath() + "\"");
						command.add("-T");
						command.add("9");
						p = new ProcessBuilder(command).start();
						// 出现线程堵塞
						dealWith(p);
						System.out.print(loadStream(p.getInputStream()));
						System.err.print(loadStream(p.getErrorStream()));
						System.out.print(loadStream(p.getInputStream()));
						System.err.println("****swf转换成功，文件输出：" + swfFile.getPath() + "****");
						if (pdfFile.exists()) {
							pdfFile.delete();
						}
					} catch (IOException e) {
						e.printStackTrace();
						throw e;
					}
				} else if (environment == 2) {// linux环境处理
					try {
						Process p = r.exec("pdf2swf " + pdfFile.getPath() + " -o " + swfFile.getPath() + " -T 9");
						System.out.print(loadStream(p.getInputStream()));
						System.err.print(loadStream(p.getErrorStream()));
						System.err.println("****swf转换成功，文件输出：" + swfFile.getPath() + "****");
						if (pdfFile.exists()) {
							pdfFile.delete();
						}
					} catch (Exception e) {
						e.printStackTrace();
						throw e;
					}
				}
			} else {
				System.out.println("****pdf不存在,无法转换****");
			}
		} else {
			System.out.println("****swf已经存在不需要转换****");
		}
	}

	static String loadStream(InputStream in) throws IOException {

		int ptr = 0;
		in = new BufferedInputStream(in);
		StringBuffer buffer = new StringBuffer();

		while ((ptr = in.read()) != -1) {
			buffer.append((char) ptr);
		}
		return buffer.toString();
	}

	/**
	 * 转换主方法
	 */
	@SuppressWarnings("unused")
	public boolean conver() {

		if (swfFile.exists()) {
			System.out.println("****swf转换器开始工作，该文件已经转换为swf****");
			return true;
		}

		if (environment == 1) {
			System.out.println("****swf转换器开始工作，当前设置运行环境windows****");
		} else {
			System.out.println("****swf转换器开始工作，当前设置运行环境linux****");
		}
		try {
			// StartOpenOffice();
			doc2pdf();
			pdf2swf();
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
		System.out.println("文件存在吗？" + swfFile);
		if (swfFile.exists()) {
			System.out.println("存在");
			return true;
		} else {
			System.out.println("不存在");
			return false;
		}
	}

	/**
	 * 返回文件路径
	 * 
	 * @param s
	 */
	public String getswfPath() {
		if (swfFile.exists()) {
			String tempString = swfFile.getPath();
			tempString = tempString.replaceAll("\\\\", "/");
			System.out.println("最后文件路径为" + tempString);
			return tempString;
		} else {
			return "文件不存在";
		}
	}

	/**
	 * 设置输出路径
	 */
	public void setOutputPath(String outputPath) {
		this.outputPath = outputPath;
		if (!outputPath.equals("")) {
			String realName = fileName.substring(fileName.lastIndexOf("/"), fileName.lastIndexOf("."));
			if (outputPath.charAt(outputPath.length()) == '/') {
				swfFile = new File(outputPath + realName + ".swf");
			} else {
				swfFile = new File(outputPath + realName + ".swf");
			}
		}
	}

	/**
	 * pdf转换swf出现线程堵塞的处理方法
	 */
	private void dealWith(final Process pro) {
		// 下面是处理堵塞的情况
		try {
			new Thread() {
				public void run() {
					BufferedReader br1 = new BufferedReader(new InputStreamReader(pro.getInputStream()));
					String text;
					try {
						while ((text = br1.readLine()) != null) {
							System.out.println(text);
						}
					} catch (IOException e) {
						e.printStackTrace();
					}
				}
			}.start();
		} catch (Exception e) {
			e.printStackTrace();
		}

		try {
			new Thread() {
				public void run() {
					BufferedReader br2 = new BufferedReader(new InputStreamReader(pro.getErrorStream()));// 这定不要忘记处理出理时产生的信息，不然会堵塞不前的
					String text;
					try {
						while ((text = br2.readLine()) != null) {
							System.err.println(text);
						}
					} catch (IOException e) {
						e.printStackTrace();
					}
				}
			}.start();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

四、部署发布
-
1.开启OpenOffice服务<br>
openoffice安装目录OpenOffice 4\program下打开dos窗口，输入以下代码来启动服务：<br>
```
soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard
```

2.部署当前web应用<br>
启动tomcat，部署当前web应用，地址栏输入：<br>
```
http://localhost:8080/OnlinePreview/documentUpload.jsp
```
