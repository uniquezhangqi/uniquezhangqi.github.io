---
layout:     post             				# 使用的布局（不需要改）
title:        jekyll 集成valine评论系统    # 标题 
subtitle:    					  				#副标题
date:       2020-07-25  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - 自我总结
---

## valine 评论系统

申请账号地址：<https://leancloud.app/>

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh38dene6rj31lz0u0gnq.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh3888r1ffj314h0u0adu.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh38dvtmicj315v0u0goy.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh38ehnxubj327g0qmjwb.jpg)

配置文件 `_config.yml` 如下：

```xml
# Valine评论系统开关
# Valine.
# You can get your appid and appkey from https://leancloud.app
# more info please open https://valine.js.org
valine:
  enable: true
  appid:  xxxxxxxxxxxxxxxxxxxx # your leancloud app id
  appkey: xxxxxxxxxxxxxxxxxxxx # your leancloud app key
  notify: true # mail notifier , https://github.com/xCss/Valine/wiki，v1.4.0+ 已废弃
  verify: true # Verification code，v1.4.0+ 已废弃
  placeholder: "不需要登录，不需要翻墙，可以直接留言" # comment box placeholder
  avatar:   # gravatar style
  guest_info: 昵称,邮件,网址 # custom comment header
  pageSize: 10 # pagination size
  recordIP: true # 是否记录评论者IP
  enableQQ: true # 是否启用昵称框自动获取QQ昵称和QQ头像, 默认关闭
```

以上的 appid和appkey为本文开始在Leancloud创建应用的 App ID 和 App Key。


`_includes`目录下创建valine_comments.html文件。文件内容如下:


**注意：** `[[     ]]`   需要改成 2 个花括号 ， 这 Macdown 如果用 花括号 就会自动显示引用里面的内容🌚，具体可以看留言板

``` html

<br>
<h4 align="left">留言区：</h4>
<div id="comments"></div>
<!--Leancloud 操作库:-->
<script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
<!--Valine 的核心代码库:-->
<script src='//unpkg.com/valine/dist/Valine.min.js'></script>
<script>
    new Valine([
        av: AV,
        el: '#comments',
        app_id: '[[ site.valine.appid ]]',
        app_key: '[[ site.valine.appkey ]]',
        placeholder: '[[ site.valine.placeholder ]]',
        notify: '[[ site.valine.notify ]]',
        verify: '[[ site.valine.verify ]]',
        recordIP: 'true',
        enableQQ: 'true',
    ])
</script>
```

在post.html文件末尾后面（或你认为合适的位置）添加代码引用valine_comments.html来显示评论框:

```xml
 <!-- Valine 评论框 start -->
  [% if site.valine.enable %]
    [% include valine_comments.html %]
    [% endif %]
<!-- Valine 评论框 end -->
```

就可以了，如本文最下面留言板。


###   intensedebate 留言统计

地址：<https://intensedebate.com/install-g>

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh39f27ehej311o0f8q4w.jpg)

绑定自己的域名，一步一步操作

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh38pveo7bj312k0jsaha.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh38q080i5j30bq0gun1u.jpg)

在 `_includes` 目录中新建intensedebate-comments.html , 上面截图的代码复制出来放到新建的这个 html 中，如下图：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh39en3pe1j31pk0nodht.jpg)

不要管文中那个 XXXXXXX，用你复制出来的就行


### 头像

Valine 的[Gravatar](https://cn.gravatar.com/) 设置评论[头像](https://valine.js.org/avatar.html)

用法： 自己邮箱账号设置好头像，以后到 valine 留言就用这个邮箱，然后就有头像了。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh387ko5mcj31eb0u00xs.jpg)


