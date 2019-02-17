---
layout:     post             				# 使用的布局（不需要改）
title:      HttpClientTest          		# 标题 
subtitle:   ✍🏻					  			#副标题
date:       2018-04-07  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-universe.jpg	 #这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。


```java
 public class Test {
	static Logger logger;

	public static void main(String[] args) throws ParseException, IOException {
		logger = Logger.getLogger( CreateOrderTest.class );
		String url = "需要的url";
		EssentialParaDto essentialParaDto = new EssentialParaDto();
		// 随机数
		essentialParaDto.setNonceStr( RandomNumber.getOrderIdByUUId() );
		// 时间戳
		essentialParaDto.setTimeStamp( Timestamp.getTimestamp() );
		// 测试秘钥
		String secret = InternetPortConstant.SECRET;
		Map<String, String> mapes = new TreeMap<String, String>();
		mapes.put( "nonceStr", essentialParaDto.getNonceStr() );
		
		mapes.put( "secret", secret );
		mapes.put( "timeStamp", String.valueOf( essentialParaDto.getTimeStamp() ) );	
		essentialParaDto.setSign( SignCommon.getSign( mapes ) );

		String nonceStr = essentialParaDto.getNonceStr();// 随机数
		String sign = essentialParaDto.getSign();// 加密签名
		System.out.println( "sign:" + sign );
		String timeStamp = String.valueOf( essentialParaDto.getTimeStamp() );// 时间戳

		// 传入的参数
		CancelOrderV2Dto dto = new CancelOrderV2Dto();
		dto.setUserId( "123" );
		dto.setOrderNo( "CCWP000010951" );
		Gson gson = new Gson();
		String orderJSON = gson.toJson( dto );
		logger.info( "orderJSON:" + orderJSON );

		Map<String, String> maps = new HashMap<String, String>();
		maps.put( "orderJSON", orderJSON );
		maps.put( "nonceStr", nonceStr );
		maps.put( "sign", sign );
		maps.put( "timeStamp", timeStamp );
		maps.put( "orderChannel", "22" );
		logger.info( "maps传入之前:" + maps );

		String orderJSONes = send( url, maps, "utf-8" );
		logger.info( "响应结果:" );
		logger.info( orderJSONes );
	}

	public static String send(String url, Map<String, String> maps, String encoding) throws ParseException, IOException {
		logger.info( "mapParameteres传入后的值:" + maps );
		String orderJSON = "";
		// 创建httpclient对象
		CloseableHttpClient client = HttpClients.createDefault();
		//url = url + "?nonceStr=" + maps.get( "nonceStr" ) + "&sign=" + maps.get( "sign" ) + "&timeStamp=" + maps.get( "timeStamp" );
		
		logger.info( "url = " + url);
		// 创建post方式请求对象
		HttpPost httpPost = new HttpPost( url );

		// 装填参数
		List<NameValuePair> nvps = new ArrayList<NameValuePair>();
		if (maps != null) {
			for (Entry<String, String> entry : maps.entrySet()) {
				nvps.add( new BasicNameValuePair( entry.getKey(), entry.getValue() ) );
			}
		}
		logger.info( "nvps:" + nvps );

		// 设置参数到请求对象中
		httpPost.setEntity( new UrlEncodedFormEntity( nvps, encoding ) );

		logger.info( "请求地址：" + url );
		logger.info( "请求参数：" + nvps.toString() );
		// 设置header信息
		// 指定报文头【Content-type】、【User-Agent】
		httpPost.setHeader( "X-Requested-With", "XMLHttpRequest" );
		httpPost.setHeader( "Content-type", "application/x-www-form-urlencoded;charset=UTF-8" );
		// 执行请求操作，并拿到结果（同步阻塞）
		CloseableHttpResponse response = client.execute( httpPost );
		HttpEntity entitys = response.getEntity();
		logger.info( "entity:" + entitys );

		// 获取结果实体
		if (entitys != null) {
			// 按指定编码转换结果实体为String类型
			orderJSON = EntityUtils.toString( entitys, encoding );
			logger.info( "orderJSON:" + orderJSON );
		}
		EntityUtils.consume( entitys );

		// 释放链接
		client.close();
		response.close();
		return orderJSON;
	}
}
```

**参考：**<https://blog.csdn.net/caesardadi/article/details/8621595>



![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>