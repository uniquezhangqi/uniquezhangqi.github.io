---
layout:     post             				# 使用的布局（不需要改）
title:      HttpClientTest          		# 标题 
subtitle:   ✍🏻					  			#副标题
date:       2018-04-07  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-universe.jpg	 #这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
    - 自我总结
---




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

```java
public class CommonMethod {

    private static Logger logger = LoggerFactory.getLogger(CommonMethod.class);

    /**
     * 拼接参数
     *
     * @param str
     * @return
     * @throws Exception
     */
    public static String splicingParameters(String str, String appKey) throws Exception {

        String strNew = str + appKey;
        String strMd5 = UuidUntil.MD5(strNew).toLowerCase();
        return str + "&SignMsg=" + strMd5;
    }

    /**
     * 拼接参数
     *
     * @param str
     * @throws Exception
     * @return不参与拼接
     */
    public static String splicingParameter(String str, String appKey) throws Exception {
        String strNew = str + appKey;
        String strMd5 = UuidUntil.MD5(strNew).toLowerCase();
        return strMd5;
    }


    /**
     * * 通用接口
     *
     * @param jsonObject       请求参数字符串，不加appkey，例如：a=1&b=2&c=3
     * @param interfaceAddress 请求地址
     * @param appKey
     * @return 服务器响应的json对象
     */
    public static JSONObject sendReqRes(String jsonObject, String interfaceAddress, String appKey) {

        String str = null;
        try {

            str = CommonMethod.splicingParameters(jsonObject, appKey);
        } catch (Exception e) {
            e.printStackTrace();
            logger.error(e.getMessage());
            return null;
        }
        JSONObject json = null;
        try {
            json = HttpRequestUtil.sendPost(interfaceAddress, str);
        } catch (Exception e) {
            e.printStackTrace();
            logger.error(e.getMessage());
        }
        return json;
    }


    /**
     * @param param
     * @return
     * @Title: MapToStr
     * @Description: 将MAP参数转成STR字符串(返回字符串示例 : a = 1 & b = 2)
     */
    public static String MapToStr(Map<String, String> param) {
        Set<String> keys = param.keySet();
        List<String> ks = CommonMethod.sortKeys(keys);
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < ks.size(); i++) {
            sb.append(ks.get(i));
            sb.append("=");
            sb.append(param.get(ks.get(i)));
            if (i == ks.size() - 1) {
                continue;
            }
            sb.append("&");
        }
        return sb.toString();
    }


    /**
     * @param keys
     * @return
     * @Title: sortKeys
     * @Description: 将SET集合转成LIST集合，并按升序排列
     */
    public static List<String> sortKeys(Set<String> keys) {
        List<String> sortKeys = new ArrayList<String>();
        for (String key : keys) {
            sortKeys.add(key);
        }
        sort(sortKeys, 0, sortKeys.size() - 1);
        return sortKeys;
    }

    /**
     * @param keys
     * @param start
     * @param end
     * @Title: sort
     * @Description: 快速排序
     */
    private static void sort(List<String> keys, int start, int end) {
        if (start >= end) {
            return;
        }
        int l = start;
        int r = end;
        String k = keys.get(start);
        while (l < r) {
            while (k.compareTo(keys.get(r)) < 0 && l < r) {
                r--;
            }
            keys.set(l, keys.get(r));
            while (k.compareTo(keys.get(l)) > 0 && l < r) {
                l++;
            }
            keys.set(r, keys.get(l));
        }
        keys.set(l, k);
        sort(keys, start, l);
        sort(keys, l + 1, end);
    }


    public static String msg(Map<String, String> map) {
        String str1 = CommonMethod.MapToStr(map);
        String appKey = SysInitConfigParam.INTERFACE_APPKEY;
        String SignMsg = null;
        try {
            SignMsg = CommonMethod.splicingParameter(str1, appKey);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return SignMsg;
    }


}
```

```java
public class HttpRequestUtil {

	private static final Logger LOGGER = LoggerFactory.getLogger(HttpRequestUtil.class);

	public static JSONObject sendPost(String url, String param) throws Exception 
	{
        
		LOGGER.info("http请求参数：{}", param);
		PrintWriter out = null;
		BufferedReader in = null;
		String result = "";
		JSONObject jsonObject = new JSONObject();
		try {

			URL realUrl = new URL(url);
			// 打开和URL之间的连接
			URLConnection conn = realUrl.openConnection();
			// 设置通用的请求属性
			conn.setRequestProperty("accept", "application/json");
			conn.setRequestProperty("connection", "Keep-Alive");
			conn.setRequestProperty("Accept-Encoding", "UTF-8");
			conn.setRequestProperty("user-agent", "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1;SV1)");
			// 发送POST请求必须设置如下两行
			conn.setDoOutput(true);
			conn.setDoInput(true);
			conn.setConnectTimeout(20000);//连接超时
			// 获取URLConnection对象对应的输出流
			out = new PrintWriter(conn.getOutputStream());
			// 发送请求参数
			out.print(param);
			// flush输出流的缓冲
			out.flush();
			// 定义BufferedReader输入流来读取URL的响应
			in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
			String line;
			while ((line = in.readLine()) != null) 
			{
				
				result += line;
			}
			LOGGER.info("http响应数据：{}", result);
		}catch (Exception e) {
			LOGGER.error("请求异常！", e);
		}finally {
		
			if (out != null) {
				out.close();
			}
			if (in != null) {
				in.close();
			}
		}
		jsonObject = JSONObject.parseObject(result);
		return jsonObject;
	}
	
	
  /**
	* 
	* @Title: sendPostJson
	* @Description: 通用POST,JSON请求
	* @param url
	* @param json
	* @return
	*/
	public static JSONObject sendPostJson(String url, String json) throws Exception 
	{
	
		LOGGER.info("请求url:"+url);
		LOGGER.info("请求参数:"+json);
		CloseableHttpClient httpClient = HttpClients.createDefault();
		HttpPost httpPost = new HttpPost(url);
		httpPost.addHeader("Content-Type", "application/json");
		httpPost.setEntity(new StringEntity(json));
		CloseableHttpResponse response = httpClient.execute(httpPost);
		HttpEntity entity = response.getEntity();
		String responseContent = EntityUtils.toString(entity, "UTF-8"); 
		LOGGER.info("返回参数:"+responseContent);
		JSONObject jsonObject = JSONObject.parseObject(responseContent);
		response.close();
		httpClient.close();
		return jsonObject;
	}
	
```

**参考：**<https://blog.csdn.net/caesardadi/article/details/8621595>



