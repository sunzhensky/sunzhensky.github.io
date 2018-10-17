---
layout: post
title: 百度人脸识别技术
description: 简单的调用接口的例子
category: blog
---

1.进入百度云的人脸识别控制台
-
地址：
```
https://console.bce.baidu.com/ai/?_=1528192333418&fromai=1#/ai/face/overview/index
```

2.创建人脸识别应用
-
账号登录成功，您需要创建应用才可正式调用AI能力。应用是您调用API服务的基本操作单元，您可以基于应用创建成功后获取的App ID、API Key及Secret Key，进行接口调用操作，及相关配置。
![001](/images/baidu-FaceAPI/001.png)
创建应用，填写【应用名称】和【应用描述】，即可创建应用（其他选项可以不做操作，使用默认值即可）
![002](/images/baidu-FaceAPI/002.png)
创建完成后，点击“返回应用列表”进行下一步操作。

3.获取App ID、API Key及Secret Key
-
![003](/images/baidu-FaceAPI/003.png)

4.测试之前进行环境的配置
-
使用maven依赖：
```java
<!-- 人脸识别需要的包 -->
<dependency>
    <groupId>com.baidu.aip</groupId>
    <artifactId>java-sdk</artifactId>
    <version>4.6.1</version>
</dependency>		
<!-- 人脸识别需要的包 -->
	
<!-- Base64编解码 -->	
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
    <version>1.10</version>
</dependency>
<!-- Base64编解码 -->
```

5.开始测试
-
以人脸注册为例子：（其他接口可自行测试：接口文档说明地址：http://ai.baidu.com/docs#/Face-Java-SDK/d126963d）
```java
public class FaceAPITest {
		
    //设置APPID/AK/SK
    public static final String APP_ID = "14350223";
    public static final String API_KEY = "H6ezlBVAMbQdLLcMArxArnFN";
    public static final String SECRET_KEY = "HAodENHk6Gik7kEGNGDs98LVNpHSLU4R";
    
    public static void main(String[] args) throws Exception {
        // 初始化一个AipFace
        AipFace client = new AipFace(APP_ID, API_KEY, SECRET_KEY);

        //人脸注册
        facesetAddUser(client);        
    }
    
    /**
     * 人脸注册
     */
    public static void facesetAddUser(AipFace client) throws Exception {
        // 传入可选参数调用接口
        HashMap<String, String> options = new HashMap<String, String>();
//        options.put("user_info", "user's info");
//        options.put("quality_control", "NORMAL");
//        options.put("liveness_control", "LOW");
        
        File file = new File("F:\\test.jpg");
        //将图片进行BASE64加密
        String base64 = getBase64(file);
        System.out.println(base64);
        //人脸注册需要的参数
        String image = base64;
        String imageType = "BASE64";
        String groupId = "group1";
        String userId = "user1";
        
        // 人脸注册
        JSONObject res = client.addUser(image, imageType, groupId, userId, options);
        System.out.println(res.toString(2));
    }
    
    /**
     * BASE64加密
     */
	public static String getBase64(File file) throws Exception{
		Base64 b64 = new Base64();
		FileInputStream fis = new FileInputStream(file);
		byte[] buffer = new byte[(int)file.length()];
		fis.read(buffer);
		fis.close();
			
		return b64.encodeToString(buffer);
	}
	
}
```

6.测试结果
-
如果成功返回如下值，并且人脸库中添加了刚才注册的用户：
![004](/images/baidu-FaceAPI/004.png)
![005](/images/baidu-FaceAPI/005.png)
如果失败，则根据返回的error_code的值，对比官方给出的错误码代表的含义进行调试。（官方错误码地址：http://ai.baidu.com/docs#/Face-ErrorCode-V3/top）
