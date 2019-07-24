---
title: 安装Ubuntu18.04 caffe tensorflow mxnet等
date: 2019-07-16 22:00:00
categories: Ubuntu
tags: Ubuntu 实验室 caffe tensorflow mxnet
mathjax: false
---

最近由于一些原因，给实验室电脑重装了Ubuntu系统，安装了具有难以言表的难度的caffe，以及tensorflow、mxnet等工具，踩了许多坑，为了以后安装方便，因此将安装过程记录下来。

# 电脑硬件

**DELL-PRECSION TOWER 7910**  
CPU：Intel Xeon(R) CPU E5-2630 v4 @ 2.20GHz × 20  
GPU：GeForce GTX 1080 Ti × 2     
Mem：128 GB  
Disk：256 GB + 2TB

# 安装系统
**下载系统镜像**   
Ubuntu 18.04 LTS：[ubuntu-18.04.2-desktop-amd64.iso](http://releases.ubuntu.com/18.04.2/ubuntu-18.04.2-desktop-amd64.iso?_ga=2.29690233.792768982.1563462256-1758338699.1562838306)    
Ubuntu桌面版下载地址：[Download Ubuntu Desktop | Download | Ubuntu](https://ubuntu.com/download/desktop) （此网址为最新版的下载地址）

注：本文需要下载的所有文件，我都已经下载好，放在了~/WD/download文件件内。

**安装系统到电脑**   
此处网上教程很多，在此给出一种安装方法：    
 [用Ultraiso(软通牒)制作ubuntu U盘启动盘-百度经验](https://jingyan.baidu.com/article/77b8dc7fa7b7c56175eab611.html)     
USB HDD+ 便携启动 也行

U盘插入电脑，开机一直按F12进入启动项选择，选择U盘，进入Ubuntu安装界面    
选择中文，选择安装    
选择汉语    
选择正常安装    
选择其他选项，目的是自己配置分区    
其实只需要配置三个分区就好    
swap(交换空间) 设置成与内存大小相近 我设成了128GB（固态硬盘）    
/根目录 按需求最好200G以上，这里我设成了125GB左右（固态硬盘）    
/home 有多大设成多大，后续自己的数据程序基本都在里面，这里我设成了1.2TB（机械硬盘）    


# apt换源
开机后联网    
打开软件和更新    
ubuntu软件-下载自-进入修改为国内的地址，比如aliyun的    
关闭并更新    
升级软件（可选）    

	sudo apt-get update
	sudo apt-get upgrade


# pip
**安装pip**   
pip是python包的管理

	sudo apt-get install python-pip python-dev build-essential
	pip install --upgrade pip
	
**pip换源**   
默认的源服务器在国外，国内访问太慢，修改为国内的服务器访问起来较快。    
修改 ~/.pip/pip.conf (没有就创建一个)， 如下：

	mkdir ~/.pip/
	geidt ~/.pip/pip.conf
	
输入如下内容并保存

	[global]
	index-url = https://mirrors.aliyun.com/pypi/simple/
	
pip换源之后速度真的是非常舒爽...


# 安装显卡驱动430.34
**下载显卡驱动**   
下载地址：[Nvidia驱动程序下载](https://www.nvidia.cn/Download/index.aspx?lang=cn)

**安装依赖项**   
	sudo apt-get install dkms build-essential linux-headers-generic apt-show-versions
	
**禁用nouveau**   
	sudo gedit /etc/modprobe.d/blacklist.conf
	
在最后一行加入

	blacklist nouveau
	blacklist lbm-nouveau
	options nouveau modeset=0
	alias nouveau off
	alias lbm-nouveau off
	
保存，并执行如下命令

	echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
	sudo update-initramfs -u

**开始安装**   
重启，在显卡驱动位置打开终端，执行：

	sudo ./NVIDIA-Linux-*.run
	
安装过程中，选择默认选项即可     
安装完成后执行

	nvidia-smi
	
(查看nvidia信息）    
出现如下信息就说明安装成功了


# 安装相关依赖项

	sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler libboost-all-dev libopenblas-dev liblapack-dev libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev

# 安装CUDA10.0
**下载CUDA**   
下载地址：[CUDA Toolkit 10.0 Archive | NVIDIA Developer](https://developer.nvidia.com/cuda-downloads)    
如果需要下载历史版本，点击页面Legacy Releases按钮选择进行下载    
推荐下载格式为runfile(local)版本，安装起来较为方便    

**开始安装**   
下载好之后在安装包所在路径打开终端，执行以下命令进行安装：

	sudo sh cuda_10.1.168_418.67_linux.run
	
条款页面太长，可按Ctrl+C退出条款页面，输入accept同意条款。    
安装过程中的选项，是否安装显卡驱动的时候选择NO（因为之前已经安装过了），其他都是选择默认。

如果没有出错的话，会显示：


**设置**   
安装完成后需要设置环境变量和动态链接库    
执行

	sudo gedit ~/.bashrc
	
写入

	export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
	export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
	export CUDA_HOME=/usr/local/cuda
	
执行

	sudo gedit /etc/profile

写入

	export PATH=/usr/local/cuda/bin:$PATH
	
执行

	sudo gedit /etc/ld.so.conf.d/cuda.conf
	
写入

	/usr/local/cuda/lib64

执行

	sudo ldconfig
	
然后重新启动

	reboot

**检查是否安装成功**   

	nvcc --version

正常的话会在最后一行显示CUDA 10.0的字样

测试一下看看cuda是否安装成功

	cd ~/NVIDIA_CUDA-10.0_Samples/1_Utilities/deviceQuery 
	sudo make -j4
	./deviceQuery
	
最后一行PASS即可


# 安装cudnn7.4
**下载cudnn**   
下载地址：[cuDNN Archive | NVIDIA Developer](https://developer.nvidia.com/rdp/cudnn-archive)    
选择需要的版本，选择cuDNN Library for Linux进行下载    
这里我选择这个版本：[cudnn-10.0-linux-x64-v7.4.2.24.tgz](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.4.2/prod/10.0_20181213/cudnn-10.0-linux-x64-v7.4.2.24.tgz)

>下载这个版本是因为tensorflow需要这个版本的cudnn

**拷贝安装**   
下载好解压后解压后进入cuda文件夹，执行：

	sudo cp include/cudnn.h /usr/local/cuda/include
	sudo cp lib64/libcudnn* /usr/local/cuda/lib64
	sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
	
对，直接拷贝过去就可以了


# 安装相关依赖项

	sudo apt-get install build-essential cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libtiff5-dev libdc1394-22-dev libatlas-base-dev gfortran



# 安装openblas
这是一个科学计算库   
**下载**   
可以到 [https://github.com/xianyi/OpenBLAS/releases](https://github.com/xianyi/OpenBLAS/releases) 下载你喜欢的版本解压到指定目录，也可以直接git clone   
**安装依赖项**     

	sudo apt-get install libopenblas-dev
	sudo apt-get install libopenblas-base

**安装**   
下载后进入OpenBLAS文件夹，执行以下命令进行安装：

	make -j10
	sudo make --PREFIX=/usr/local/OpenBLAS/ install

**测试**   
测试OpenBLAS

	gedit ./test.c

输入：

==**等待编辑**==

执行：

	gcc ./test.c  -I /usr/local/OpenBLAS/include/ -L /usr/local/OpenBLAS/lib -lopenblas
	
会在当前文件夹下生成可执行文件　a.out
执行：

	a.out
	
结果如下

> 0.000000 1.000000 2.000000 3.000000 4.000000 5.000000 6.000000 7.000000 8.000000 9.000000 
90.000000 81.000000 72.000000 63.000000 54.000000 45.000000 36.000000 27.000000 18.000000 9.000000


# 安装caffe-ssd
以上全部安装完成后，我们开始安装caffe-ssd   
**下载**   
下载caffe-ssd源码  [github caffe-ssd地址](https://github.com/weiliu89/caffe/tree/ssd)   
下载caffe源码  [github caffe地址](https://github.com/BVLC/caffe)     

**修改Makefile.config**   
下载解压后进入caffe-ssd文件夹    
官方的说明是 cp Makefile.config.example Makefile.config 然后修改内容。    
这里我已经修改好了，修改好的Makefile.config，直接拷贝到caffe-ssd中即可。    

关于opencv版本：    
Makefile.config中第23行
> OPENCV_VERSION := 3

需要根据自己opencv的版本情况设定，首先查询本机的opencv版本：

	pkg-config opencv --modversion

如果是3.x的话，需要将Makefile.config中第23前面的注释#去掉。

**拷贝文件**   
将caffe中关于cudnn的文件全部拷贝到caffe-ssd的相应文件夹中中，因为caffe-ssd中的caffe版本太老，直接安装会出错。

**开始编译**

    sudo make all -j20
    sudo make test -j20
    sudo make runtest -j20
    sudo make pycaffe -j20

如果不报错，应该说明安装好了

**测试pycaffe**   
终端输入

    python
    import sys
    sys.path.append("{your_caffe_path}/python")
    import caffe

不报错就说明安装好了

**测试mnist分类**   
这个教程写的挺好的：[运行caffe自带的mnist实例教程](https://www.cnblogs.com/wmlj/p/8681216.html)


++**以下是可能出现的错误及解决方法**++

**错误１**   

    collect2: error: ld returned 1 exit status
    .build_release/lib/libcaffe.so：对‘cv::VideoWriter::isOpened() const’未定义的引用
    collect2: error: ld returned 1 exit status
    Makefile:619: recipe for target '.build_release/tools/convert_annoset.bin' failed
    make: *** [.build_release/tools/convert_annoset.bin] Error 1
    Makefile:624: recipe for target '.build_release/examples/siamese/convert_mnist_siamese_data.bin' failed
    make: *** [.build_release/examples/siamese/convert_mnist_siamese_data.bin] Error 1

解决：
首先这个问题确实是opencv的问题，只需要把  Makefile.config里的#USE_PKG_CONFIG := 这一行前面的#给去掉，然后在他下一行添加
 
    LIBRARIES += glog gflags protobuf leveldb snappy \
        lmdb boost_system hdf5_hl hdf5 m \
        opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs

参考：[https://blog.csdn.net/yuweiyang123/article/details/53106638](https://blog.csdn.net/yuweiyang123/article/details/53106638) 

**警告２**

     In file included from src/caffe/util/math_functions.cu:1:0:
    /usr/local/cuda/include/math_functions.h:54:2: warning: #warning "math_functions.h is an internal header file and must not be used directly.  This file will be removed in a future CUDA release.  Please use cuda_runtime_api.h or cuda_runtime.h instead." [-Wcpp]
     #warning "math_functions.h is an internal header file and must not be used directly.  This file will be removed in a future CUDA release.  Please use cuda_runtime_api.h or cuda_runtime.h instead."
 
解决２:
修改相关程序的头文件即可
将src/caffe/util/math_functions.cu第一行的math_functions.h修改为cuda_runtime_api.h即可


**错误３**

	1 Check failed: a <= b <0 vs -1.19209e-007>

解决：
解决办法修改src/caffe/util/sampler.cpp，如下面修改代码所示//renew注释下，加入两个判断，使得bbox长宽不要越界。

      // Figure out bbox dimension.
      float bbox_width = scale * sqrt(aspect_ratio);
      float bbox_height = scale / sqrt(aspect_ratio);
    
      //renew
      if(bbox_width>=1.0)
      {
        bbox_width=1.0;
      }
      if(bbox_height>=1.0)
      {
        bbox_height=1.0;
      }
  
参考：[https://blog.csdn.net/LuohenYJ/article/details/88416180](https://blog.csdn.net/LuohenYJ/article/details/88416180)



**错误４**

    ./include/caffe/util/cudnn.hpp:21:10: warning: enumeration value ‘CUDNN_STATUS_RUNTIME_FP_OVERFLOW’ not handled in switch [-Wswitch]
    CXX src/caffe/layers/cudnn_sigmoid_layer.cpp
    In file included from ./include/caffe/util/device_alternate.hpp:40:0,
                 from ./include/caffe/common.hpp:19,
                 from ./include/caffe/blob.hpp:8,
                 from ./include/caffe/layers/silence_layer.hpp:6,
                 from src/caffe/layers/silence_layer.cpp:3:

解决：
 作者发布的源码中caffe的版本较低，下载最新的caffe，将cudnn开头的文件拷贝覆盖掉caffe-ssd中的对应文件夹即可


**错误５**

    CXX/LD -o .build_release/tools/convert_imageset.bin
    //usr/lib/x86_64-linux-gnu/libblas.so.3：对‘gotoblas’未定义的引用
    collect2: error: ld returned 1 exit status
    Makefile:619: recipe for target '.build_release/tools/upgrade_solver_proto_text.bin' failed
    make: *** [.build_release/tools/upgrade_solver_proto_text.bin] Error 1
    make: *** 正在等待未完成的任务....
    //usr/lib/x86_64-linux-gnu/libblas.so.3：对‘gotoblas’未定义的引用

解决：
这里应该是openblas未安装好，执行以下命令安装相关依赖项即可

    sudo apt-get install libopenblas-dev
    sudo apt-get install libopenblas-base

如果还是出现以上错误，在makefile.config相应位置中加入路径/usr/lib/x86_64-linux-gnu/即可，具体如下：
修改前：

    INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/lib/x86_64-linux-gnu/hdf5/serial/include
    LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial

修改后：

    INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/lib/x86_64-linux-gnu/hdf5/serial/include /usr/lib/x86_64-linux-gnu/
    LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial /usr/lib/x86_64-linux-gnu/



++**以下安装不分先后**++

# 安装 TensorFlow 1.13.1

    sudo pip install tensorflow-gpu==1.13.0

附：安装tensorflow需要的cuda以及cudnn版本可进入官网查询    
地址：[从源代码编译  |  TensorFlow](https://tensorflow.google.cn/install/source)    
页面最下方即可查看版本依赖关系   

| 版本      | Python 版本|  编译器| 编译工具| cuDNN | CUDA |
| --------- | -------- | -----: | --: | --: | --: |
| tensorflow_gpu-1.13.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.19.2 | 7.4 | 10.0 |
| tensorflow_gpu-1.12.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.15.0 | 7 | 9 |


# 安装 MXNet 1.4.0
由于我安装的是cuda10.0，因此使用如下命令安装

	pip install mxnet-cu100

# 安装搜狗拼音输入法
去官网下载deb格式的安装包，下载地址：[搜狗输入法 for linux](https://pinyin.sogou.com/linux/?r=pinyin)    
下载后直接双击deb安装包，进入软件管理器自动安装。    
安装后注销或者重启即可使用


# 安装WPS Office
去官网下载deb格式的安装包，下载地址：[WPS Office 2019 For Linux](https://www.wps.cn/product/wpslinux)    
下载后直接双击deb安装包，进入软件管理器自动安装。

安装后若报错，系统缺失字体。    
解决方法  [解决：WPS for Linux提示“系统缺失字体”](https://www.cnblogs.com/dinphy/p/5888546.html)

若一直出现警告：

>下载额外数据文件失败 ttf-mscorefonts-installer

后台发现/usr/share/package-data-downloads有一个文件ttf-mscorefonts-installer，用gedit打开，有一大串地址。   
其实解决办法是，手动把这些地址链接的文件下载下来，然后放到一个文件夹中。   
如果懒得下载，也可以使用已下载好的文件，百度网盘： https://pan.baidu.com/s/1jIcfEMa 密码: rbeh   
将下载的文件解压放到/usr/share/package-data-downloads中，然后手动在命令行执行sudo dpkg-reconfigure ttf-mscorefonts-installer这条语句手动指定文件夹的位置,重新配置下。   

参考: [解决Ubuntu“下载额外数据文件失败  ttf-mscorefonts-installer的问题_博客园](https://www.cnblogs.com/bfhxt/p/9967039.html)
# 安装 MATLAB R2017b

**安装**   
进入matlab文件夹，执行：

	sudo mkdir /media/matlab
	sudo mount -o loop ./R2017b_glnxa64_dvd1.iso /media/matlab
	sudo /media/matlab/install

Install choosing the option "Use a File Installation Key" and supply the following FIK

当提示需要挂载第二个盘时，先退出第一个盘，可在ubuntu18.04桌面找到matlab挂载的文件，点击右键取消挂载，然后挂载第二个盘：

    sudo mount -o loop ./R2017b_glnxa64_dvd2.iso /media/matlab


**激活**   
	cd /usr/local/MATLAB/R2016b/bin
	sudo ./matlab

进入crack下R2017b/bin/glnxa64拷贝文件

	sudo cp license_standalone.lic /usr/local/MATLAB/R2017b/licenses/
	sudo cp libmwservices.so /usr/local/MATLAB/R2017b/bin/glnxa64/

**任意位置终端打开matlab**   
在目录/usr/local/bin里面创建一个指向Matlab安装目录/usr/local/MATLAB/R2017b/bin的符号链接：（非默认安装需替换安装路径）

	sudo ln -s /usr/local/MATLAB/R2017b/bin/matlab /usr/local/bin/matlab

现在在任何位置都可以打开MATLAB

	matlab


**其他**   
如果出现权限问题，执行如下命令

	sudo chmod a+w -R ~/.matlab

