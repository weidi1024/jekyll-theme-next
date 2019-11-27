---
title: 安装python3.5
date: 2019-11-27 22:00:00
categories: Ubuntu
tags: Ubuntu
mathjax: true
---

	sudo add-apt-repository ppa:jonathonf/python-3.6
	sudo apt-get update
	sudo apt-get install python3.6

调整优先级

	sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
	sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2



参考   
[1][ubuntu16.04 安装配置python3.6 - young_better的博客 - CSDN博客](https://blog.csdn.net/baidu_32392485/article/details/78588325)