---
layout:     post             				# 使用的布局（不需要改）
title:      DownloadBase64Png   # 标题 
subtitle:   😏 					  				#副标题
date:       2018-07-08  					# 时间
author:     Ian                  			# 作者
header-img: img/img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 前端
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。

# 二维码下载
## 思路一：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-07-08-122505.png)

## 思路二：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-07-08-%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%80%9D%E8%B7%AF%E4%BA%8C.png)

当然是思路二比较好，不管从哪个方面讲。Z 直接上思路二的代码：

### 环境
```xml
<!-- 
环境：easyui、springmvc、maven 
需要的jar
-->
        <!-- 生成二维码 -->
        <dependency>
            <groupId>com.google.zxing</groupId>
            <artifactId>core</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.8</version>
        </dependency>

        <dependency>
            <groupId>com.google.zxing</groupId>
            <artifactId>javase</artifactId>
            <version>3.3.0</version>
        </dependency>
```

### 前端代码
```js
    //下载图片
    function downloadPng() {
        let imgData,
        $.ajax({
            type: "POST",
            url: "后台接口",
            async: false,
            data: {id: id},
            success: function (downloadUrl) {
                imgData = 'data:image/png;base64,' + downloadUrl;
            }
        });
        this.downloadFile('二维码.png', imgData);
    }

    //下载
    function downloadFile(fileName, content) {
        let aLink = document.createElement('a');
        //new Blob([content]);
        let blob = this.base64ToBlob(content);

        let evt = document.createEvent("HTMLEvents");
        //initEvent 不加后两个参数在FF下会报错  事件类型，是否冒泡，是否阻止浏览器的默认行为
        evt.initEvent("click", true, true);
        aLink.download = fileName;
        aLink.href = URL.createObjectURL(blob);

        // aLink.dispatchEvent(evt);
        aLink.click()
    }

    //base64转blob
    function base64ToBlob(code) {
        let parts = code.split(';base64,');
        let contentType = parts[0].split(':')[1];
        let raw = window.atob(parts[1]);
        let rawLength = raw.length;

        let uInt8Array = new Uint8Array(rawLength);

        for (let i = 0; i < rawLength; ++i) {
            uInt8Array[i] = raw.charCodeAt(i);
        }
        return new Blob([uInt8Array], {type: contentType});
    }
```

### 后台代码
```java
// controller 只需要把base64字符串响应给前端就ok，Z 这里直接给工具类
public class ZXingCode {
	/**
	 * 二维码宽度(默认)
	 */
	private static final int                         WIDTH   = 300;
	/**
	 * 二维码高度(默认)
	 */
	private static final int                         HEIGHT  = 300;
	/**
	 * 二维码文件格式
	 */
	private static final String                      FORMAT  = "png";
	/**
	 * 二维码参数
	 */
	private static final Map<EncodeHintType, Object> hints   = new HashMap();
	/**
	 * 默认是黑色
	 */
	private static final int                         QRCOLOR = 0xFF000000;
	/**
	 * 背景颜色
	 */
	private static final int                         BGWHITE = 0xFFFFFFFF;

	static {
		//字符编码
		hints.put( EncodeHintType.CHARACTER_SET, "utf-8" );
		//容错等级 H为最高
		hints.put( EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.H );
		//边距
		hints.put( EncodeHintType.MARGIN, 2 );
	}

	public static void main(String[] args) {
		try {
			String a = getLogoQRCode( "https://www.baidu.com/", "跳转到百度的二维码" );
			log.info( "------------------:" + a );
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	/**
	 * 生成带logo的二维码图片
	 *
	 * @param qrUrl
	 * @param productName
	 * @return
	 */
	public static String getLogoQRCode(String qrUrl, String productName) {
		//      String filePath = (javax.servlet.http.HttpServletRequest)request.getSession().getServletContext().getRealPath("/") + "resources/static/code/logo.png";
		//      String filePath = "ClassPath:/resources/static/code/logo.png";  //这个路径有问题，到时候看直接弄绝对路径
		//filePath是二维码logo的路径，但是实际中我们是放在项目的某个路径下面的，所以路径用上面的，把下面的注释就好
		String filePath = "绝对路径";

		String content = qrUrl;
		try {
			ZXingCode zp = new ZXingCode();
			BufferedImage bim = zp.getQR_CODEBufferedImage( content, BarcodeFormat.QR_CODE, 400, 400, zp.getDecodeHintType() );
			return zp.addLogo_QRCode( bim, new File( filePath ), new LogoConfig(), productName );
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}

	/**
	 * 将二维码图片输出到一个流中
	 *
	 * @param content 二维码内容
	 * @param stream  输出流
	 * @param width   宽
	 * @param height  高
	 */
	public static void writeToStream(String content, OutputStream stream, int width, int height) throws WriterException, IOException {
		BitMatrix bitMatrix = new MultiFormatWriter().encode( content, BarcodeFormat.QR_CODE, width, height, hints );
		MatrixToImageWriter.writeToStream( bitMatrix, FORMAT, stream );
	}

	/**
	 * 给二维码图片添加Logo
	 *
	 * @param bim
	 * @param logoPic
	 * @param logoConfig
	 * @param productName
	 * @return
	 */
	public String addLogo_QRCode(BufferedImage bim, File logoPic, LogoConfig logoConfig, String productName) {
		try {
			/**
			 * 读取二维码图片，并构建绘图对象
			 */
			BufferedImage image = bim;
			Graphics2D g = image.createGraphics();

			/**
			 * 读取Logo图片
			 */
			BufferedImage logo = ImageIO.read( logoPic );
			/**
			 * 设置logo的大小,本人设置为二维码图片的20%,因为过大会盖掉二维码
			 */
			int widthLogo = logo.getWidth( null ) > image.getWidth() * 3 / 10 ? (image.getWidth() * 3 / 10) : logo.getWidth( null ), heightLogo =
					logo.getHeight( null ) > image.getHeight() * 3 / 10 ? (image.getHeight() * 3 / 10) : logo.getWidth( null );

			/**
			 * logo放在中心
			 */
			int x = (image.getWidth() - widthLogo) / 2;
			int y = (image.getHeight() - heightLogo) / 2;
			/**
			 * logo放在右下角
			 *  int x = (image.getWidth() - widthLogo);
			 *  int y = (image.getHeight() - heightLogo);
			 */

			//开始绘制图片
			g.drawImage( logo, x, y, widthLogo, heightLogo, null );
			//            g.drawRoundRect(x, y, widthLogo, heightLogo, 15, 15);
			//            g.setStroke(new BasicStroke(logoConfig.getBorder()));
			//            g.setColor(logoConfig.getBorderColor());
			//            g.drawRect(x, y, widthLogo, heightLogo);
			g.dispose();

			//把商品名称添加上去，商品名称不要太长哦，这里最多支持两行。太长就会自动截取啦
			if (productName != null && !productName.equals( "" )) {
				//新的图片，把带logo的二维码下面加上文字
				BufferedImage outImage = new BufferedImage( 400, 445, BufferedImage.TYPE_4BYTE_ABGR );
				Graphics2D outg = outImage.createGraphics();
				//画二维码到新的面板
				outg.drawImage( image, 0, 0, image.getWidth(), image.getHeight(), null );
				//画文字到新的面板
				outg.setColor( Color.BLACK );
				//字体、字型、字号
				outg.setFont( new Font( "宋体", Font.BOLD, 30 ) );
				int strWidth = outg.getFontMetrics().stringWidth( productName );
				if (strWidth > 399) {
					//                  //长度过长就截取前面部分
					//                  outg.drawString(productName, 0, image.getHeight() + (outImage.getHeight() - image.getHeight())/2 + 5 ); //画文字
					//长度过长就换行
					String productName1 = productName.substring( 0, productName.length() / 2 );
					String productName2 = productName.substring( productName.length() / 2, productName.length() );
					int strWidth1 = outg.getFontMetrics().stringWidth( productName1 );
					int strWidth2 = outg.getFontMetrics().stringWidth( productName2 );
					outg.drawString( productName1, 200 - strWidth1 / 2, image.getHeight() + (outImage.getHeight() - image.getHeight()) / 2 + 12 );
					BufferedImage outImage2 = new BufferedImage( 400, 485, BufferedImage.TYPE_4BYTE_ABGR );
					Graphics2D outg2 = outImage2.createGraphics();
					outg2.drawImage( outImage, 0, 0, outImage.getWidth(), outImage.getHeight(), null );
					outg2.setColor( Color.BLACK );
					//字体、字型、字号
					outg2.setFont( new Font( "宋体", Font.BOLD, 30 ) );
					outg2.drawString( productName2, 200 - strWidth2 / 2,
							outImage.getHeight() + (outImage2.getHeight() - outImage.getHeight()) / 2 + 5 );
					outg2.dispose();
					outImage2.flush();
					outImage = outImage2;
				} else {
					//画文字
					outg.drawString( productName, 200 - strWidth / 2, image.getHeight() + (outImage.getHeight() - image.getHeight()) / 2 + 12 );
				}
				outg.dispose();
				outImage.flush();
				image = outImage;
			}
			logo.flush();
			image.flush();
			ByteArrayOutputStream baos = new ByteArrayOutputStream();
			baos.flush();
			ImageIO.write( image, "png", baos );

            /*
            二维码生成的路径
             */
			ImageIO.write( image, "png", new File(
					"绝对路径" + System.currentTimeMillis() + ".png" ) );

			BASE64Encoder base64Encoder = new BASE64Encoder();
			String imageBase64QRCode = base64Encoder.encode( baos.toByteArray() );

			baos.close();
			return imageBase64QRCode;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return "";
	}

	/**
	 * 构建初始化二维码
	 *
	 * @param bm
	 * @return
	 */
	public BufferedImage fileToBufferedImage(BitMatrix bm) {
		BufferedImage image = null;
		try {
			int w = bm.getWidth(), h = bm.getHeight();
			image = new BufferedImage( w, h, BufferedImage.TYPE_INT_RGB );

			for (int x = 0; x < w; x++) {
				for (int y = 0; y < h; y++) {
					image.setRGB( x, y, bm.get( x, y ) ? 0xFF000000 : 0xFFCCDDEE );
				}
			}

		} catch (Exception e) {
			e.printStackTrace();
		}
		return image;
	}

	/**
	 * 生成二维码bufferedImage图片
	 *
	 * @param content       编码内容
	 * @param barcodeFormat 编码类型
	 * @param width         图片宽度
	 * @param height        图片高度
	 * @param hints         设置参数
	 * @return
	 */
	public BufferedImage getQR_CODEBufferedImage(String content, BarcodeFormat barcodeFormat, int width, int height, Map<EncodeHintType, ?> hints) {
		MultiFormatWriter multiFormatWriter = null;
		BitMatrix bm = null;
		BufferedImage image = null;
		try {
			multiFormatWriter = new MultiFormatWriter();
			// 参数顺序分别为：编码内容，编码类型，生成图片宽度，生成图片高度，设置参数
			bm = multiFormatWriter.encode( content, barcodeFormat, width, height, hints );
			int w = bm.getWidth();
			int h = bm.getHeight();
			image = new BufferedImage( w, h, BufferedImage.TYPE_INT_RGB );

			// 开始利用二维码数据创建Bitmap图片，分别设为黑（0xFFFFFFFF）白（0xFF000000）两色
			for (int x = 0; x < w; x++) {
				for (int y = 0; y < h; y++) {
					image.setRGB( x, y, bm.get( x, y ) ? QRCOLOR : BGWHITE );
				}
			}
		} catch (WriterException e) {
			e.printStackTrace();
		}
		return image;
	}

	/**
	 * 设置二维码的格式参数
	 *
	 * @return
	 */
	public Map<EncodeHintType, Object> getDecodeHintType() {
		// 用于设置QR二维码参数
		Map<EncodeHintType, Object> hints = new HashMap<EncodeHintType, Object>( 16 );
		// 设置QR二维码的纠错级别（H为最高级别）具体级别信息
		hints.put( EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.H );
		// 设置编码方式
		hints.put( EncodeHintType.CHARACTER_SET, "utf-8" );
		hints.put( EncodeHintType.MARGIN, 0 );
		hints.put( EncodeHintType.MAX_SIZE, 350 );
		hints.put( EncodeHintType.MIN_SIZE, 100 );

		return hints;
	}
}

class LogoConfig {
	// logo默认边框颜色
	public static final Color DEFAULT_BORDERCOLOR = Color.WHITE;
	// logo默认边框宽度
	public static final int   DEFAULT_BORDER      = 2;
	// logo大小默认为照片的1/5
	public static final int   DEFAULT_LOGOPART    = 5;

	private final int   border = DEFAULT_BORDER;
	private final Color borderColor;
	private final int   logoPart;

	/**
	 * Creates a default config with on color  and off color
	 * , generating normal black-on-white barcodes.
	 */
	public LogoConfig() {
		this( DEFAULT_BORDERCOLOR, DEFAULT_LOGOPART );
	}

	public LogoConfig(Color borderColor, int logoPart) {
		this.borderColor = borderColor;
		this.logoPart = logoPart;
	}

	public Color getBorderColor() {
		return borderColor;
	}

	public int getBorder() {
		return border;
	}

	public int getLogoPart() {
		return logoPart;
	}
}
```
###  结果：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-07-08-%E4%BA%8C%E7%BB%B4%E7%A0%81.png)

# 总结：
自己用到的时候方便查找，也希望对你有所帮助。



![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>
