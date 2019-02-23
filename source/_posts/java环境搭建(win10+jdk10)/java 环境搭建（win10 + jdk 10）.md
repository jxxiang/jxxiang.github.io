---
title: java 环境搭建（win10 + jdk 10）
---

<p align="right"> 作者：不完美</p>

## 一、下载、安装jdk（jdk中包含了jre)

### 1、下载jdk（[下载地址](https://www.oracle.com/technetwork/java/javase/downloads/index.html) ）

点击下图中DOWNLOAD调转到下载页面

![Markdown](http://i1.bvimg.com/662648/269f8462013c3b59.png)



点击下图中的协议选择相应版本进行下载（这里下载的是jdk10.0.2 for windows 64)

![Markdown](http://i1.bvimg.com/662648/a46e4051f8489359.png)



### 2、安装jdk

jdk下载下来是一个.exe文件，双击这个文件，按照提示进行安装（这里按照默认路径装在了C盘）

![Markdown](http://i1.bvimg.com/662648/ea339ee25c39f746.png)



## 二、配置环境变量

### 1、安装完成后，找到环境变量

右击”我的电脑“，点击”属性“，选择”高级系统设置“，如下图所示

![Markdown](http://i1.bvimg.com/662648/1a4cb8c35c421a83.png)





点开”高级“选项卡，选择下方的”环境变量“

![Markdown](http://i1.bvimg.com/662648/27d1b911d69d2835.png)



### 2、修改系统变量（*JAVA_HOME、JRE_HOME、CLASSPATH、PATH*）

这里jdk安装路径：C:\Program Files\Java\jdk-10.0.2

       jre安装路径：C:\Program Files\Java\jre-10.0.2

**JAVA_HOME：** 在系统变量中如果没有JAVA_HOME则新建系统变量JAVA_HOME，变量值是jdk的安装路径，如下图所示。

![Markdown](http://i1.bvimg.com/662648/b93c499951f52ad9.png)



**JRE_HOME：**在系统变量中如果没有JRE_HOME则新建系统变量JRE_HOME，变量值是jre的安装路径，如下图所示。

![Markdown](http://i1.bvimg.com/662648/e265663f3d4fd840.png)



**CLASS_PATH：**在系统变量中如果没有CLASS_PATH则新建系统变量CLASS_PATH，变量值为：```.;%JAVA_HOME%\lib;%JRE_HOME%\lib``` ，如下图所示。

![Markdown](http://i1.bvimg.com/662648/133113c2b4faa751.png)



**PATH：**编辑系统变量中的PATH，新增路径```C:\Program Files\Java\jdk-10.0.2\bin```和```C:\Program Files\Java\jre-10.0.2\bin``` 。如下图所示（*注意：*win10需要用绝对路径，否则javac会报不是内部命令也不是外部命令）

![p9](pic\9.png)



## 三、输入java、java  -version、javac 测试配置是否成功

如下图所示则表明配置成功。

![Markdown](http://i1.bvimg.com/662648/8ba197385f563498.png)



![Markdown](http://i1.bvimg.com/662648/9acd91a9d39f85ae.png)



![Markdown](http://i1.bvimg.com/662648/f6b99292a0a0f687.png)





**PS：** 这里将jdk和jre按照默认路径装在了C盘，试了一下装在D盘，同样的配置，Javac还是不能运行。emmm，不知道为什么....











