# Jetson Nano 刷机

## Nano基本配置

* 一个显示屏HDMI口, 一个网线口, 没有天线需要外接网卡
* 两个USB口
* 一个USBA-B用来连接host
* 一个type-C电源口
* 一个micro SD卡(TF卡)卡槽

## 需要的东西

* 显示屏, 键鼠: 用来配置系统
* micro sd卡和读卡器: 用来保存系统
* 主机: 用来下载镜像

## 系统安装流程
(官方参考文档)[https://developer.nvidia.cn/embedded/learn/get-started-jetson-nano-devkit#setup]
1. 下载jetpack镜像
因为Jetson系列的深度学习和推理引擎的环境都是内部封装好的包, 因此包括操作系统在内所有的所有需要用到的插件等等都需要下载jetpack, 下载后不建议单独对升级某个下载好的部件进行升级, 十分有可能遇到插件不兼容的情况. [下载链接](https://developer.nvidia.com/embedded/jetpack-archive)
Jetson Nano最高可下载版本为4.6, jetpack中包含的内容包括![内容](../../官方手册/Jetson%20nano开发示例.jpeg)

2. 解压jetpack包, 解压格式为镜像文件.img
3. 准备好micro SD卡
    * 对SD卡格式化, 使用`SD card Formatter`
    * 将下载好的镜像刷到SD卡里面, 使用`balenaEtcher`或者`WinImage`
4. 将micro SD卡插到卡槽里面即可载入系统, 首次登录需要设置Ubuntu系统, 包括设置语言, 地区, 下载相关软件等等, 会比较漫长, 下载完成后就算是装载好系统了

## 深度学习环境配置

1. 检查已经预装载好的TensorRT, OpenCV等是否可以使用, 检查的指令详见[相关指令](../nano_info.md)
2. 添加相关环境变量, 包括
    * TensorRT的trtexec执行路径
    * cuda的路径
    * 库的路径
3. 为Ubuntu系统换源
    * 注: 不是一定要换, 理论上国内源换掉之后国内源下载速度会变快, 但是有些需要的文件国内源有缺失, 下载的时候会很难受; 以及很多给Ubuntu换源的源都是`x84-64`的, 但是Jetson 系列的架构是`arm64`, 必须支持`arm64`的库才行, 所以换的时候小心
    * 首先要对原始文件做备份, 源文件地址`\etc\apt\source.list`(一定要备份!! 血的教训!!)
    * 注释掉源文件, 或者在源文件前面加上换源后的国内源
    * 更新文件列表`sudo apt-get update`
    * 更新软件`sudo apt-get upgrade`
```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic main restricted universe multiverse
# deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-updates main restricted universe multiverse
# deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-updates main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-backports main restricted universe multiverse
# deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-backports main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-security main restricted universe multiverse
# deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-security main restricted universe multiverse
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.aliyun.com/ubuntu-ports/ bionic main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic-updates main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu-ports/s bionic-backports main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic-security main restricted universe multiverse
```

4. 下载conda
    * 注: 不一定要下载, Jetson内存又小, 很多下载的包都是jetpack里面的, 反正我用conda下载体验不太好, 最后直接卸载了, 但是如果有需要多环境没办法的可以试一下
    * 由于架构不是x84, 所以不能使用anaconda, 而是使用archiconda
    * 需要先下载archiconda发布包`wget --quiet -O archiconda.sh https://github.com/Archiconda/build-tools/releases/download/0.2.3/Archiconda3-0.2.3-Linux-aarch64.sh`
    * 安装archiconda`sh archiconda.sh -b -p ~/archiconda3`
    * 加入环境变量`export PATH=../anaconda3/bin:$PATH`
    * 创建对应版本的环境, 如果是jetpack4.6, 对应安装python的版本是3.6, 那么创建的环境的python版本只能是3.6
5. 深度学习环境见[Jetson TX2环境配置](../../)