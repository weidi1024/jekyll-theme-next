---
title: 调整显卡风扇转速
date: 2019-11-26 22:00:00
categories: Ubuntu
tags: Ubuntu
mathjax: true
---


近期朋友遇到显卡风扇转速过大，噪声非常大的问题。   
网络上查询相关资料，可以调整显卡的风扇转速来解决。    
折腾了一次，发现只能修改显卡0的转速，这可能是教程的作者的电脑只有一个显卡，所以教程具有局限性。   
后来又查到多GPU下调整风扇转速的博客文章，成功解决了调整显卡风扇转速的问题。遂记录下。   
代码为：

	sudo nvidia-xconfig
	sudo nvidia-xconfig --enable-all-gpus
	sudo nvidia-xconfig --cool-bits=4

执行完之后注销或者重启
	
	nvidia-settings
	
进入Thermal Settings里面的Enable GPU Fan Settings    
	可以对显卡风扇转速进行设置

但是其实这里只是一个缓兵之策，问题的本源应该显卡允许的最高温度较低，当温度不太高的时候，风扇就开始狂转了，但现在还不知道该怎么去调整这个温度最大值。

参考   
[1][https://devtalk.nvidia.com/default/topic/1003810/linux/adjust-nvidia-gpu-fan-speed-multiple-gpus-one-monitor-/](https://devtalk.nvidia.com/default/topic/1003810/linux/adjust-nvidia-gpu-fan-speed-multiple-gpus-one-monitor-/)