---
layout:     post             				# 使用的布局（不需要改）
title:      HttpClient之Get   # 标题 
subtitle:    					  				#副标题
date:       2018-07-03  					# 时间
author:     Ian                  			# 作者
header-img: img/ian-bg-red.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---
> 转载：allen的博客：https://blog.csdn.net/qq_20641565/article/details/56509738

之前Z 有写一篇 HttpClient **post**请求的[HttpClientTest](http://uniquezhangqi.top/2018/04/07/%E7%BC%96%E7%A8%8B%E4%B9%8B%E8%B7%AF-HttpClientDemo/)

亲测没问题，下面这个Get请求的：

```java
package com.lijie.hc;

import java.util.ArrayList;
import java.util.List;

import org.apache.http.HttpEntity;
import org.apache.http.NameValuePair;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicHeader;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

public class HttpClientTest {

    public static void main(String[] args) throws Exception {

        //      testGet();

        //      testPost();
    }

    /**
     * httpclient get请求
     * 
     * @throws Exception
     */
    public static void testGet() throws Exception {

        //创建一个httpclient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //创建URIBuilder
        URIBuilder uri = new URIBuilder("http://localhost:8080/api/httpClientTestGet.do");

        //设置参数
        uri.addParameter("id", "10001");

        //创建httpGet对象
        HttpGet hg = new HttpGet(uri.build());

        //设置请求的报文头部的编码
        hg.setHeader(
            new BasicHeader("Content-Type", "application/x-www-form-urlencoded; charset=utf-8"));

        //设置期望服务端返回的编码
        hg.setHeader(new BasicHeader("Accept", "text/plain;charset=utf-8"));

        //请求服务
        CloseableHttpResponse response = client.execute(hg);

        //获取响应码
        int statusCode = response.getStatusLine().getStatusCode();

        if (statusCode == 200) {

            //获取返回实例entity
            HttpEntity entity = response.getEntity();

            //通过EntityUtils的一个工具方法获取返回内容
            String resStr = EntityUtils.toString(entity, "utf-8");

            //输出
            System.out.println("请求成功,请求返回内容为: " + resStr);
        } else {

            //输出
            System.out.println("请求失败,错误码为: " + statusCode);
        }

        //关闭response和client
        response.close();
        client.close();
    }

    public static void testPost() throws Exception {

        //创建一个httpclient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //创建一个post对象
        HttpPost post = new HttpPost("http://localhost:8080/api/httpClientTestPost.do");

        //创建一个Entity，模拟表单数据
        List<NameValuePair> formList = new ArrayList<>();

        //添加表单数据
        formList.add(new BasicNameValuePair("username", "黎杰"));
        formList.add(new BasicNameValuePair("password", "10086"));

        //包装成一个Entity对象
        StringEntity entity = new UrlEncodedFormEntity(formList, "utf-8");

        //设置请求的内容
        post.setEntity(entity);

        //设置请求的报文头部的编码
        post.setHeader(
            new BasicHeader("Content-Type", "application/x-www-form-urlencoded; charset=utf-8"));

        //设置期望服务端返回的编码
        post.setHeader(new BasicHeader("Accept", "text/plain;charset=utf-8"));

        //执行post请求
        CloseableHttpResponse response = client.execute(post);

        //获取响应码
        int statusCode = response.getStatusLine().getStatusCode();

        if (statusCode == 200) {

            //获取数据
            String resStr = EntityUtils.toString(response.getEntity());

            //输出
            System.out.println("请求成功,请求返回内容为: " + resStr);
        } else {

            //输出
            System.out.println("请求失败,错误码为: " + statusCode);
        }

        //关闭response和client
        response.close();
        client.close();

    }
}
```

```java
//工具类

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.URL;
import java.net.URLConnection;
import java.util.List;
import java.util.Map;

import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.alibaba.fastjson.JSONObject;
import com.i78dk.easyloan.core.common.HttpResult;
import com.i78dk.easyloan.core.common.constants.ResultCodeConstants;
import com.i78dk.easyloan.inter.common.mx.TrustAnyHostnameVerifier;
import com.i78dk.easyloan.inter.common.mx.TrustAnyTrustManager;


public class HttpRequestUtil {

    private static final Logger LOGGER = LoggerFactory.getLogger(HttpRequestUtil.class);
    
    /**
     * 连接超时时间
     */
    public static final int CONNECT_TIMEOUT = 60000;
    /**
     * 读取超时时间
     */
    public static final int READ_TIMEOUT = 60000;
    
    /**
     * 向指定https URL 发送POST方法的请求
     * 
     * @param url
     *            发送请求的 URL
     * @param param
     *            请求参数，Json 的形式。
     * @param property
     *            请求header参数，Json 的形式。          
     * @return 所代表远程资源的响应结果
     */
    public static String sendHttpsPost(String url, JSONObject param, JSONObject property) {
       
        PrintWriter out = null;
        BufferedReader in = null;
        HttpsURLConnection conn = null; 
        String result = "";
        try {
        	
            URL realUrl = new URL(url);       
    		
    		SSLContext sc = SSLContext.getInstance("SSL");
    		sc.init(null, new TrustManager[] { new TrustAnyTrustManager() }, new java.security.SecureRandom());
    		conn = (HttpsURLConnection) realUrl.openConnection();
    		// 设置连接超时
    		conn.setConnectTimeout(CONNECT_TIMEOUT);
    		// 设置读取超时
    		conn.setReadTimeout(READ_TIMEOUT);
    		if(null != property){
    			for (Map.Entry<String, Object> entry : property.entrySet()) {
                    String key = entry.getKey();
                    String value = entry.getValue().toString();                    
                    conn.setRequestProperty(key, value);
                }
    			
    		}    		
    		conn.setRequestProperty("Content-Type", "application/json");
    		conn.setRequestProperty("Accept-Charset", "utf-8");
    		conn.setSSLSocketFactory(sc.getSocketFactory());
    		conn.setHostnameVerifier(new TrustAnyHostnameVerifier());
    		conn.setRequestMethod("POST");
    		conn.setDoOutput(true);
    		conn.setDoInput(true);
    		out = new PrintWriter(conn.getOutputStream());

    		// 提交参数
    		String jsonStr = param.toJSONString();

    		out.print(jsonStr);
    		out.flush();
    		
    		in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    		String line;
    		while ((line = in.readLine()) != null) {
    			result += line;
    		}
    		conn.disconnect();
    		
    		return JSONObject.toJSONString(HttpResult.successResult(result));
    		
        } catch (Exception ex) {
            ex.printStackTrace();
            LOGGER.error("sendHttpsPost:发送 POST 请求出现异常！{}", ex.getMessage().toString());
            return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));

        } finally {
            try {
            	if (conn != null) {
            		conn.disconnect();
                }
                if (out != null) {
                    out.close();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException ex) {
                LOGGER.error("sendHttpsPost:io关闭异常!{}", ex.getMessage().toString());
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }
        
    }  
    
    /**
     * 向指定https URL 发送GET方法的请求
     * 
     * @param url
     *            发送请求的 URL
     * @param param
     *            请求参数，请求参数应该是 name1=value1&name2=value2 的形式。
     * @param property
     *            请求header参数，Json 的形式。          
     * @return 所代表远程资源的响应结果
     */
    public static String sendHttpsGet(String url, String param, JSONObject property) {
       
        BufferedReader in = null;
        HttpsURLConnection conn = null;
        String result = "";
        String urlNameString =url;
        		
        try {
        	
        	if(null != param && !"".equals(param)){

            	urlNameString = url + "?" + param;
        	}
        	
            URL realUrl = new URL(urlNameString);
    		
    		SSLContext sc = SSLContext.getInstance("SSL");
    		sc.init(null, new TrustManager[] { new TrustAnyTrustManager() }, new java.security.SecureRandom());
    		conn = (HttpsURLConnection) realUrl.openConnection();
    		// 设置连接超时
    		conn.setConnectTimeout(CONNECT_TIMEOUT);
    		// 设置读取超时
    		conn.setReadTimeout(READ_TIMEOUT);
    		if(null != property){
    			for (Map.Entry<String, Object> entry : property.entrySet()) {
                    String key = entry.getKey();
                    String value = entry.getValue().toString();                    
                    conn.setRequestProperty(key, value);
                }
    			
    		}    		
    		conn.setRequestProperty("Content-Type", "application/json");
    		conn.setRequestProperty("Accept-Charset", "utf-8");
    		conn.setSSLSocketFactory(sc.getSocketFactory());
    		conn.setHostnameVerifier(new TrustAnyHostnameVerifier());
    		conn.setRequestMethod("GET");
    		
    		conn.connect();
    		
    		in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    		String line;
    		while ((line = in.readLine()) != null) {
    			result += line;
    		}
    		conn.disconnect();
    		
    		return JSONObject.toJSONString(HttpResult.successResult(result));
    		
        } catch (Exception ex) {
        	ex.printStackTrace();
            LOGGER.error("sendHttpsGet:发送 Get 请求出现异常！{}", ex.getMessage().toString());            
            return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));
        } finally {
            try {
            	if (conn != null) {
            		conn.disconnect();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException ex) {
                LOGGER.error("sendHttpsGet:io关闭异常!{}", ex.getMessage().toString());         
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }
        
    }  
    
    /**
     * 向指定http URL 发送POST方法的请求
     * 
     * @param url
     *            发送请求的 URL
     * @param param
     *            请求参数，Json 的形式。
     * @return 所代表远程资源的响应结果
     */
    public static String sendHttpPost(String url, String param) {
        
        PrintWriter out = null;
        BufferedReader in = null;
        String result = "";
        try {
        	
            URL realUrl = new URL(url);
            // 打开和URL之间的连接
            URLConnection conn = realUrl.openConnection();
            // 设置连接超时
    		conn.setConnectTimeout(CONNECT_TIMEOUT);
    		// 设置读取超时
    		conn.setReadTimeout(READ_TIMEOUT);
    		conn.setRequestProperty("Content-Type", "application/json");
    		conn.setRequestProperty("Accept-Charset", "utf-8");
            // 发送POST请求必须设置如下两行
            conn.setDoOutput(true);
            conn.setDoInput(true);
            // 获取URLConnection对象对应的输出流
            out = new PrintWriter(conn.getOutputStream());
            // 发送请求参数
            out.print(param);
            // flush输出流的缓冲
            out.flush();
            // 定义BufferedReader输入流来读取URL的响应
            in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    		String line;
    		while ((line = in.readLine()) != null) {
    			result += line;
    		}
    		
    		return JSONObject.toJSONString(HttpResult.successResult(result));
    		
        } catch (Exception ex) {
        	ex.printStackTrace();
            LOGGER.error("sendHttpPost:发送 POST 请求出现异常！{}", ex.getMessage().toString());
            return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));
           
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException ex) {
                LOGGER.error("sendHttpPost:IO关闭异常!{}", ex.getMessage().toString());
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }
    }  
    
    /**
     * 向指定http URL发送GET方法的请求
     * 
     * @param url
     *            发送请求的URL
     * @param param
     *            请求参数，请求参数应该是 name1=value1&name2=value2 的形式。
     * @return URL 所代表远程资源的响应结果
     */
    public static String sendHttpGet(String url, String param) {
        String result = "";
        BufferedReader in = null;
        try {
            String urlNameString = url + "?" + param;
            URL realUrl = new URL(urlNameString);
            // 打开和URL之间的连接
            URLConnection connection = realUrl.openConnection();
            // 设置连接超时
            connection.setConnectTimeout(CONNECT_TIMEOUT);
    		// 设置读取超时
            connection.setReadTimeout(READ_TIMEOUT);
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("Accept-Charset", "utf-8");
            // 建立实际的连接
            connection.connect();
            // 获取所有响应头字段
            Map<String, List<String>> map = connection.getHeaderFields();
            // 遍历所有的响应头字段
            for (String key : map.keySet()) {
                System.out.println(key + "--->" + map.get(key));
            }
            // 定义 BufferedReader输入流来读取URL的响应
            in = new BufferedReader(new InputStreamReader(
                    connection.getInputStream()));
            String line;
            while ((line = in.readLine()) != null) {
                result += line;
            }
            
            return JSONObject.toJSONString(HttpResult.successResult(result));
            
        } catch (Exception ex) {
        	ex.printStackTrace();
        	LOGGER.error("发送 Get 请求出现异常！{}", ex.getMessage().toString());
        	return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));
        	
        } finally {
            try {
                if (in != null) {
                    in.close();
                }
            } catch (Exception ex) {
                ex.printStackTrace();
                LOGGER.error("IO关闭异常!{}", ex.getMessage().toString());
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }
        
    }
    
    /**
     * 向指定https URL 发送GET方法的请求
     * 
     * @param url
     *            发送请求的 URL
     * @param param
     *            请求参数，请求参数应该是 name1=value1&name2=value2 的形式。
     * @param property
     *            请求header参数，Json 的形式。          
     * @return 所代表远程资源的响应结果
     */
    public static String sendHttpsGetGzip(String url, String param, JSONObject property) {
       
        BufferedReader in = null;
        HttpsURLConnection conn = null;
        String result = "";
        String urlNameString =url;
        		
        try {
        	
        	if(null != param && !"".equals(param)){

            	urlNameString = url + "?" + param;
        	}
        	
            URL realUrl = new URL(urlNameString);
    		
    		SSLContext sc = SSLContext.getInstance("SSL");
    		sc.init(null, new TrustManager[] { new TrustAnyTrustManager() }, new java.security.SecureRandom());
    		conn = (HttpsURLConnection) realUrl.openConnection();
    		// 设置连接超时
    		conn.setConnectTimeout(CONNECT_TIMEOUT);
    		// 设置读取超时
    		conn.setReadTimeout(READ_TIMEOUT);
    		if(null != property){
    			for (Map.Entry<String, Object> entry : property.entrySet()) {
                    String key = entry.getKey();
                    String value = entry.getValue().toString();                    
                    conn.setRequestProperty(key, value);
                }
    			
    		}    		
    		conn.setRequestProperty("Content-Type", "application/json");
    		conn.setRequestProperty("Accept-Charset", "utf-8");
    		conn.setSSLSocketFactory(sc.getSocketFactory());
    		conn.setHostnameVerifier(new TrustAnyHostnameVerifier());
    		conn.setRequestMethod("GET");
    		
    		conn.connect();
    		
    		result = GzipUtil.uncompress(conn.getInputStream());
    		
    		conn.disconnect();
    		
    		return JSONObject.toJSONString(HttpResult.successResult(result));
    		
        } catch (Exception ex) {
        	ex.printStackTrace();
            LOGGER.error("sendHttpsGetGzip:发送 Get 请求出现异常！{}", ex.getMessage().toString());            
            return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));
        } finally {
            try {
            	if (conn != null) {
            		conn.disconnect();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException ex) {
                LOGGER.error("sendHttpsGetGzip:io关闭异常!{}", ex.getMessage().toString());         
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }
        
    }

    /**
     * 向指定https URL 发送GET方法的请求
     *
     * @param url
     *            发送请求的 URL
     *            请求参数，请求参数应该是 name1=value1&name2=value2 的形式。
     * @param property
     *            请求header参数，Json 的形式。
     * @return 所代表远程资源的响应结果
     */
    public static String sendHttpsGet(String url, JSONObject property) {

        BufferedReader in = null;
        HttpsURLConnection conn = null;
        String result = "";
        String urlNameString =url;

        try {
            URL realUrl = new URL(urlNameString);

            SSLContext sc = SSLContext.getInstance("SSL");
            sc.init(null, new TrustManager[] { new TrustAnyTrustManager() }, new java.security.SecureRandom());
            conn = (HttpsURLConnection) realUrl.openConnection();
            // 设置连接超时
            conn.setConnectTimeout(CONNECT_TIMEOUT);
            // 设置读取超时
            conn.setReadTimeout(READ_TIMEOUT);
            if(null != property){
                for (Map.Entry<String, Object> entry : property.entrySet()) {
                    String key = entry.getKey();
                    String value = entry.getValue().toString();
                    conn.setRequestProperty(key, value);
                }

            }
            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept-Charset", "utf-8");
            conn.setSSLSocketFactory(sc.getSocketFactory());
            conn.setHostnameVerifier(new TrustAnyHostnameVerifier());
            conn.setRequestMethod("GET");

            conn.connect();

            in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
            String line;
            while ((line = in.readLine()) != null) {
                result += line;
            }
            conn.disconnect();

            return JSONObject.toJSONString(HttpResult.successResult(result));

        } catch (Exception ex) {
            ex.printStackTrace();
            LOGGER.error("sendHttpsGet:发送 Get 请求出现异常！{}", ex.getMessage().toString());
            return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0002));
        } finally {
            try {
                if (conn != null) {
                    conn.disconnect();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException ex) {
                LOGGER.error("sendHttpsGet:io关闭异常!{}", ex.getMessage().toString());
                return JSONObject.toJSONString(HttpResult.failResult(ex.getMessage().toString(),ResultCodeConstants.CODE_S0007));
            }
        }

    }


    public static void main(String[] args) {
        // 程序启动入口
        // 启动嵌入式的 Tomcat 并初始化 Spring 环境及其各 Spring 组件
        JSONObject property = new JSONObject();
        property.put("Authorization", "token 3ef74e699cf743809dbda3fef0535643");
        JSONObject param = new JSONObject();
        param.put("task_id", "1dd5b060-66ca-11e7-84a0-00163e0020f3");

    	String ssss= sendHttpsPost("https://api.51datakey.com/email/v2/data", param, property);
    	System.out.println(ssss);
    }
}

```


![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>
