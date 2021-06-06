---
layout:     post             				# 使用的布局（不需要改）
title:      Error: Cannot install in Homebrew on ARM 	 # 标题 
subtitle:    					  				#副标题
date:       2021-03-17  					# 时间
author:     阿琦                  			# 作者
header-img: img/wallhaven-cycling.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
istop:  false                             # 是否置顶
iscopyright: true                      # 是否版权，默认有
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - 编程之路
    - 自我总结
    - 问题定位
---

&nbsp;
&nbsp;


##  M1 芯片的兼容问题

Updating Homebrew...
Error: Cannot install in Homebrew on ARM processor in Intel default prefix (/usr/local)!
Please create a new installation in /opt/homebrew using one of the

![](https://static01.imgkr.com/temp/72ce0ab9574942e0bf7e870b80e8d88e.png)

1. 将/usr/local 目录下的所有文件移动到 /opt/homebrew 目录下
``` linux
sudo mkdir /opt/homebrew
sudo mv /usr/local/* /opt/homebrew 
```

2. 修正/bin目录下的软连接
``` linux
ln -s /opt/homebrew/Homebrew/bin/brew /opt/homebrew/bin/brew
```

3. 修改环境变量
``` linux
open ~/.zshrc -e
```
![](https://static01.imgkr.com/temp/3df7300df8ec48088519f046fd28cb07.png)

编辑此文件，将内容修改如下
``` linux
# HomeBrew
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
export PATH="/opt/homebrew/bin:$PATH"
export PATH="/opt/homebrew/sbin:$PATH"
# HomeBrew END

```

主要是修改path路径。

上面3步，所有工作已经完工。
此时执行 brew -v 会提示找不到brew命令。这是因为在当前的shell中brew还是原来的环境变量，不知道改变后的brew在哪里。所以关闭shell重新打开，或者执行source ~/.zshrc重新载入即可。

删除软连接：
rm -rf  ./要删除的目标

&nbsp;

> Mac安装oh my zsh插件autojump、zsh-syntax-highlighting、zsh-autosuggestion: https://www.jianshu.com/p/bd9ea233f933
> Ubuntu 安装zsh配置 oh-my-zsh autojump 和美化: https://www.jianshu.com/p/fc63d64c06d5
> 7449.github.io: https://7449.github.io/2019/11/20/mac-reloading.html
> mac下高效安装 homebrew 及完美避坑姿势 （亲测有效）: https://www.cnblogs.com/joyce33/p/13376752.html
