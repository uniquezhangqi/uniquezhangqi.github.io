---
layout:     post             				# 使用的布局（不需要改）
title:      jenkins docker automatic build vue project OOM    # 标题 
subtitle:    					  				#副标题
date:       2022-12-19  					# 时间
author:     Ian                  			# 作者
header-img: img/beauty_1662430256382.JPG 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
istop:  false                             # 是否置顶
iscopyright: false                      # 是否版权，默认有
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - 自我总结
    - 编程之路
    - docker
---

&nbsp;
&nbsp;
<section id="nice" data-tool="mdnice编辑器" data-website="https://www.mdnice.com" style="font-size: 16px; color: black; padding: 0 10px; line-height: 1.6; word-spacing: 0px; letter-spacing: 0px; word-break: break-word; word-wrap: break-word; text-align: left; counter-reset: counterh1 counterh2 counterh3; font-family: Optima-Regular, Optima, PingFangSC-light, PingFangTC-light, 'PingFang SC', Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;"><h2 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 22px;"><span class="prefix" style="display: inline-block;"><span style="counter-increment: counterh2; color: rgb(159,205,208); border-bottom: 4px solid rgb(159,205,208); font-size: 18px; padding: 2px 4px;">1</span></span><span class="content" style="font-size: 18px; border-bottom: 4px solid rgb(37,132,181); padding: 2px 4px; color: rgb(37,132,181);">背景</span><span class="suffix"></span></h2>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">jenkins + docker 自动化部署vue 项目，然后各种坑然后就遇到了久违了 oom 了</p>
<figure data-tool="mdnice编辑器" style="margin: 0; margin-top: 10px; margin-bottom: 10px; display: flex; flex-direction: column; justify-content: center; align-items: center;"><img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99bssmxd0j30ti0i7tcz.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></figure>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">打印的日志到 building 就没下文了也不报错 jenkins 服务还死掉了，然后去代码看是不是配置有问题啥的，再检查一遍</p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">用的若依的开源项目，对应配置
<img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99btv2jevj31hg0r8afy.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">检查后没毛病，上服务器看日志
<img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99bvtrxpkj31300u0qce.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">重现问题，看看经过
<img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99bwoj7dvj31db0u0tbx.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">看着内存涨的，股票都没这样涨过</p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">解决：</p>
<pre class="custom" data-tool="mdnice编辑器" style="margin-top: 10px; margin-bottom: 10px; border-radius: 5px; box-shadow: rgba(0, 0, 0, 0.55) 0px 2px 10px;"><span style="display: block; background: url(https://files.mdnice.com/user/3441/876cad08-0422-409d-bb5a-08afec5da8ee.svg); height: 30px; width: 100%; background-size: 40px; background-repeat: no-repeat; background-color: #282c34; margin-bottom: -7px; border-radius: 5px; background-position: 10px 10px;"></span><code class="hljs" style="overflow-x: auto; padding: 16px; color: #abb2bf; display: -webkit-box; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; font-size: 12px; -webkit-overflow-scrolling: touch; padding-top: 15px; background: #282c34; border-radius: 5px;">//加上下面这一段<br>&nbsp;&nbsp;"scripts":&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"fix-memory-limit":&nbsp;"cross-env&nbsp;LIMIT=4096&nbsp;increase-memory-limit"<br>&nbsp;&nbsp;},<br>&nbsp;&nbsp;<br>&nbsp;&nbsp;<br>&nbsp;npm&nbsp;install&nbsp;-g&nbsp;cross-env<br>&nbsp;<br>&nbsp;npm&nbsp;install&nbsp;-g&nbsp;increase-memory-limit<br></code></pre>
<figure data-tool="mdnice编辑器" style="margin: 0; margin-top: 10px; margin-bottom: 10px; display: flex; flex-direction: column; justify-content: center; align-items: center;"><img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99bxpc0a8j311k0u0gqk.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></figure>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">就可以了
<img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99byjjo30j30zu0u0n0f.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">服务器上面对应的包也出来了
<img src="https://tva1.sinaimg.cn/large/008vxvgGly1h99byzm6t3j317m0hi42k.jpg" alt style="display: block; margin: 0 auto; max-width: 100%;"></p>
<h2 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 22px;"><span class="prefix" style="display: inline-block;"><span style="counter-increment: counterh2; color: rgb(159,205,208); border-bottom: 4px solid rgb(159,205,208); font-size: 18px; padding: 2px 4px;">2</span></span><span class="content" style="font-size: 18px; border-bottom: 4px solid rgb(37,132,181); padding: 2px 4px; color: rgb(37,132,181);">为什么npm bulid 内存会暴涨</span><span class="suffix"></span></h2>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">网上搜了一圈好像也没找到大白话的解释，只有这个 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">“CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory JavaScript堆内存不足”</code>，大概意思是 JavaScript 其实就是 Node， 它是基于V8引擎，在 Node 中通过 JavaScript 使用内存时只能使用部分内存（64位系统下约为1.4 GB，32位系统下约为0.7 GB），然后编译项目时才会出现内存泄露，如果前端项目非常的庞大，webpack 编译时就会占用很多的系统资源，如果超出了V8对 Node 默认的内存限制大小就会出现上面打引号的错误了。</p>
</section>