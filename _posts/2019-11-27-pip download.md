---
title: pip download/install 下载/离线安装第三方包
date: 2019-11-27 22:00:00
categories: Ubuntu
tags: Ubuntu
mathjax: true
---



之前的做法是直接在pypi网站上下载whl包，但是这样下载太慢。     
于是我们对pip换国内源，在机器上直接pip install 也能有不错的速度。   
但是现在有可能会出现一些原因需要下载安装包离线安装   
比如我想离线下载这个包    
tensorflow-1.15.0-cp36-cp36m-manylinux2010_x86_64.whl    
使用换过国内源的电脑下载    
pip下载第三方包：    

	pip download <包名> -d "下载的路径(windows下双引号来表示文件夹)"

pip安装第三方包：

	pip install <包名>



参考   
[1][pip download/install 下载/离线安装第三方包 - 菜猿pro - 博客园](https://www.cnblogs.com/jasonzhang-blog/p/11262738.html)