Http协议的重要性相信不用我多说了，HttpClient相比传统JDK自带的URLConnection，增加了易用性和灵活性（具体区别，日后我们再讨论），它不仅是客户端发送Http请求变得容易，而且也方便了开发人员测试接口（基于Http协议的），即提高了开发的效率，也方便提高代码的健壮性。因此熟练掌握HttpClient是很重要的必修内容，掌握HttpClient后，相信对于Http协议的了解会更加深入。

org.apache.commons.httpclient.HttpClient与org.apache.http.client.HttpClient的区别
>Commons的HttpClient项目现在不再被开发,已被Apache HttpComponents项目HttpClient和的HttpCore模组取代，提供更好的性能和更大的灵活性。  
![](https://img2018.cnblogs.com/blog/1012917/201912/1012917-20191201114500417-987375842.png)

一、简介
---
HttpClient是Apache Jakarta Common下的子项目，用来提供高效的、最新的、功能丰富的支持HTTP协议的客户端编程工具包，并且它支持HTTP协议最新的版本和建议。HttpClient已经应用在很多的项目中，比如Apache Jakarta上很著名的另外两个开源项目Cactus和HTMLUnit都使用了HttpClient。
下载地址: https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient

二、特性
---
>1. 基于标准、纯净的java语言。实现了Http1.0和Http1.1
>2. 以可扩展的面向对象的结构实现了Http全部的方法（GET, POST, PUT, DELETE, HEAD, OPTIONS, and TRACE）。
>3. 支持HTTPS协议。
>4. 通过Http代理建立透明的连接。
>5. 利用CONNECT方法通过Http代理建立隧道的https连接。
>6. Basic, Digest, NTLMv1, NTLMv2, NTLM2 Session, SNPNEGO/Kerberos认证方案。
>7. 插件式的自定义认证方案。
>8. 便携可靠的套接字工厂使它更容易的使用第三方解决方案。
>9. 连接管理器支持多线程应用。支持设置最大连接数，同时支持设置每个主机的最大连接数，发现并关闭过期的连接。
>10. 自动处理Set-Cookie中的Cookie。
>11. 插件式的自定义Cookie策略。
>12. Request的输出流可以避免流中内容直接缓冲到socket服务器。
>13. Response的输入流可以有效的从socket服务器直接读取相应内容。
>14. 在http1.0和http1.1中利用KeepAlive保持持久连接。
>15. 直接获取服务器发送的response code和 headers。
>16. 设置连接超时的能力。
>17. 实验性的支持http1.1 response caching。
>18. 源代码基于Apache License 可免费获取。

三、使用方法
---
使用HttpClient发送请求、接收响应很简单，一般需要如下几步即可。
>1. 创建HttpClient对象。
>2. 创建请求方法的实例，并指定请求URL。如果需要发送GET请求，创建HttpGet对象；如果需要发送POST请求，创建HttpPost对象。
>3. 如果需要发送请求参数，可调用HttpGet、HttpPost共同的setParams(HetpParams params)方法来添加请求参数；对于HttpPost对象而言，也可调用setEntity(HttpEntity entity)方法来设置请求参数。
>4. 调用HttpClient对象的execute(HttpUriRequest request)发送请求，该方法返回一个HttpResponse。
>5. 调用HttpResponse的getAllHeaders()、getHeaders(String name)等方法可获取服务器的响应头；调用HttpResponse的getEntity()方法可获取HttpEntity对象，该对象包装了服务器的响应内容。程序可通过该对象获取服务器的响应内容。
>6. 释放连接。无论执行方法是否成功，都必须释放连接

**相关jar包**
>commons-cli-1.2.jar  
commons-codec-1.9.jar  
commons-logging-1.2.jar  
fluent-hc-4.5.1.jar  
httpclient-4.5.1.jar  
httpclient-cache-4.5.1.jar  
httpclient-win-4.5.1.jar  
httpcore-4.4.3.jar  
httpcore-ab-4.4.3.jar  
httpcore-nio-4.4.3.jar  
httpmime-4.5.1.jar  
jna-4.1.0.jar  
jna-platform-4.1.0.jar  

四、实例
---
### GET请求
```java
/**
 * 发送 get请求
 */ 
public void get() { 
    CloseableHttpClient httpclient = HttpClients.createDefault(); 
    try { 
        // 创建httpget.   
        HttpGet httpget = new HttpGet("http://www.baidu.com/"); 
        System.out.println("executing request " + httpget.getURI()); 
        // 执行get请求.   
        CloseableHttpResponse response = httpclient.execute(httpget); 
        try { 
            // 获取响应实体   
            HttpEntity entity = response.getEntity(); 
            System.out.println("--------------------------------------"); 
            // 打印响应状态   
            System.out.println(response.getStatusLine()); 
            if (entity != null) { 
                // 打印响应内容长度   
                System.out.println("Response content length: " + entity.getContentLength()); 
                // 打印响应内容   
                System.out.println("Response content: " + EntityUtils.toString(entity)); 
            } 
            System.out.println("------------------------------------"); 
        } finally { 
            response.close(); 
        } 
    } catch (ClientProtocolException e) { 
        e.printStackTrace(); 
    } catch (ParseException e) { 
        e.printStackTrace(); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } finally { 
        // 关闭连接,释放资源   
        try { 
            httpclient.close(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
}
```
### POST请求
```java
/**
 * 发送 post请求访问本地应用并根据传递参数不同返回不同结果
 */ 
public void post() { 
    // 创建默认的httpClient实例.   
    CloseableHttpClient httpclient = HttpClients.createDefault(); 
    // 创建httppost   
    HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceJ.action"); 
    // 创建参数队列   
    List<NameValuePair> formparams = new ArrayList<NameValuePair>(); 
    formparams.add(new BasicNameValuePair("type", "house")); 
    UrlEncodedFormEntity uefEntity; 
    try { 
        uefEntity = new UrlEncodedFormEntity(formparams, "UTF-8"); 
        httppost.setEntity(uefEntity); 
        System.out.println("executing request " + httppost.getURI()); 
        CloseableHttpResponse response = httpclient.execute(httppost); 
        try { 
            HttpEntity entity = response.getEntity(); 
            if (entity != null) { 
                System.out.println("--------------------------------------"); 
                System.out.println("Response content: " + EntityUtils.toString(entity, "UTF-8")); 
                System.out.println("--------------------------------------"); 
            } 
        } finally { 
            response.close(); 
        } 
    } catch (ClientProtocolException e) { 
        e.printStackTrace(); 
    } catch (UnsupportedEncodingException e1) { 
        e1.printStackTrace(); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } finally { 
        // 关闭连接,释放资源   
        try { 
            httpclient.close(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
} 
```
### 表单提交
```java
/**
 * post方式提交表单（模拟用户登录请求）
 */ 
public void postForm() { 
    // 创建默认的httpClient实例.   
    CloseableHttpClient httpclient = HttpClients.createDefault(); 
    // 创建httppost   
    HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceJ.action"); 
    // 创建参数队列   
    List<NameValuePair> formparams = new ArrayList<NameValuePair>(); 
    formparams.add(new BasicNameValuePair("username", "admin")); 
    formparams.add(new BasicNameValuePair("password", "123456")); 
    UrlEncodedFormEntity uefEntity; 
    try { 
        uefEntity = new UrlEncodedFormEntity(formparams, "UTF-8"); 
        httppost.setEntity(uefEntity); 
        System.out.println("executing request " + httppost.getURI()); 
        CloseableHttpResponse response = httpclient.execute(httppost); 
        try { 
            HttpEntity entity = response.getEntity(); 
            if (entity != null) { 
                System.out.println("--------------------------------------"); 
                System.out.println("Response content: " + EntityUtils.toString(entity, "UTF-8")); 
                System.out.println("--------------------------------------"); 
            } 
        } finally { 
            response.close(); 
        } 
    } catch (ClientProtocolException e) { 
        e.printStackTrace(); 
    } catch (UnsupportedEncodingException e1) { 
        e1.printStackTrace(); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } finally { 
        // 关闭连接,释放资源   
        try { 
            httpclient.close(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
} 
```
### 文件上传
```java
/**
 * 上传文件
 */ 
public void upload() { 
    CloseableHttpClient httpclient = HttpClients.createDefault(); 
    try { 
        HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceFile.action"); 

        FileBody bin = new FileBody(new File("F:\\image\\sendpix0.jpg")); 
        StringBody comment = new StringBody("A binary file of some kind", ContentType.TEXT_PLAIN); 

        HttpEntity reqEntity = MultipartEntityBuilder.create().addPart("bin", bin).addPart("comment", comment).build(); 

        httppost.setEntity(reqEntity); 

        System.out.println("executing request " + httppost.getRequestLine()); 
        CloseableHttpResponse response = httpclient.execute(httppost); 
        try { 
            System.out.println("----------------------------------------"); 
            System.out.println(response.getStatusLine()); 
            HttpEntity resEntity = response.getEntity(); 
            if (resEntity != null) { 
                System.out.println("Response content length: " + resEntity.getContentLength()); 
            } 
            EntityUtils.consume(resEntity); 
        } finally { 
            response.close(); 
        } 
    } catch (ClientProtocolException e) { 
        e.printStackTrace(); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } finally { 
        try { 
            httpclient.close(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
} 
```
### SSL链接
```java
/**
 * HttpClient连接SSL
 */ 
public void ssl() { 
    CloseableHttpClient httpclient = null; 
    try { 
        KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType()); 
        FileInputStream instream = new FileInputStream(new File("d:\\tomcat.keystore")); 
        try { 
            // 加载keyStore d:\\tomcat.keystore   
            trustStore.load(instream, "123456".toCharArray()); 
        } catch (CertificateException e) { 
            e.printStackTrace(); 
        } finally { 
            try { 
                instream.close(); 
            } catch (Exception ignore) { 
            } 
        } 
        // 相信自己的CA和所有自签名的证书 
        SSLContext sslcontext = SSLContexts.custom().loadTrustMaterial(trustStore, new TrustSelfSignedStrategy()).build(); 
        // 只允许使用TLSv1协议 
        SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext, new String[] { "TLSv1" }, null, 
                SSLConnectionSocketFactory.BROWSER_COMPATIBLE_HOSTNAME_VERIFIER); 
        httpclient = HttpClients.custom().setSSLSocketFactory(sslsf).build(); 
        // 创建http请求(get方式) 
        HttpGet httpget = new HttpGet("https://localhost:8443/myDemo/Ajax/serivceJ.action"); 
        System.out.println("executing request" + httpget.getRequestLine()); 
        CloseableHttpResponse response = httpclient.execute(httpget); 
        try { 
            HttpEntity entity = response.getEntity(); 
            System.out.println("----------------------------------------"); 
            System.out.println(response.getStatusLine()); 
            if (entity != null) { 
                System.out.println("Response content length: " + entity.getContentLength()); 
                System.out.println(EntityUtils.toString(entity)); 
                EntityUtils.consume(entity); 
            } 
        } finally { 
            response.close(); 
        } 
    } catch (ParseException e) { 
        e.printStackTrace(); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } catch (KeyManagementException e) { 
        e.printStackTrace(); 
    } catch (NoSuchAlgorithmException e) { 
        e.printStackTrace(); 
    } catch (KeyStoreException e) { 
        e.printStackTrace(); 
    } finally { 
        if (httpclient != null) { 
            try { 
                httpclient.close(); 
            } catch (IOException e) { 
                e.printStackTrace(); 
            } 
        } 
    } 
} 
```
原文链接：<https://www.cnblogs.com/jepson6669/p/9358244.html>