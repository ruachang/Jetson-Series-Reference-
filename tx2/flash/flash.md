# Jetson tx2 刷机 + 深度学习配置

## 基本连接

* 按键
  * PWR: 最右侧按键, 长按开机
  * REC: 恢复模式设置按键(Recovery Mode)
  * VOL: 似乎是自定义的
  * RES: 可以辅助进入恢复模式
* 接口
  * 两根天线, 一个网线口
  * 一个USBA-B: 用来和host连接, 有专用线
  * 一个USB3.0: 可以外接一个扩展坞来加上其它外设
  * 一个SD卡插口: 可以设置TX2为从SD卡载入

## 刷机

大概刷好了? 没有截图捏, 完了再说捏

Jetson tx2开机自带Ubuntu16.03系统, 但是没有装cuda, TensorRT之类的, 需要自己安装, 对于Jetson系列器件, 比较快的办法是直接去装`jetpack`, 会把这个机子重刷一遍, tx2能下载的最高版本是`jetpack 4.6`, 相关信息在[Jetson pack archive](https://developer.nvidia.com/embedded/jetpack-archive)

### 配置需要
* 主机(host):
  * Ubuntu系统 or Ubuntu虚拟机, 内存>50GB
* Jetson tx2:
  * 要求连接好网络
  * 理论上可以没有显示屏, 键鼠, 只要可以ssh连接用远程操作就可以. 但是实际操作起来可能有困难

### 步骤

1. 在虚拟机上下载SDK Manager [官网链接](https://developer.nvidia.com/drive/sdk-manager)
2. 打开SDK Manager, 确保此时Jetson TX2和虚拟机是连接的, 
设置好STEP01中的器件和下载的`jetpack`版本进入STEP 02开始下载
[![ppBKvcD.png](https://s1.ax1x.com/2023/03/24/ppBKvcD.png)](https://imgse.com/i/ppBKvcD)
(要求Target Hardware那里是可以检测到的)
3. STEP02下载, 理论上下面两个可以都选, 但是据说第二个选项如果网不好可能会崩
[![ppBQpxU.png](https://s1.ax1x.com/2023/03/24/ppBQpxU.png)](https://imgse.com/i/ppBQpxU)
4. 下载完成后会弹出页面设置器件进入恢复模式(选择是自动或者手动)以及设置Jetson TX2的用户名和密码, 建议在设置之前直接手动让TX2进入恢复模式, 点击FLASH刷机
   * 设置恢复模式: 按PWR开机 => 长按REC, 不松手继续按一下RES, 绿灯闪一下 => 继续按REC 
   * 进入恢复模式后会连不上ssh
5. 进入漫长的安装模式, 在host上装好后会转移到Jetson TX2上, 此时会弹出页面填写刚才设置的用户名, 密码, 并且检查Jetson TX2是否联网, 满足条件后开始安装, 结束后基本环境配置完成

### 中间遇到的奇奇怪怪的问题

1. 虚拟机检测不到器件: 不管怎么插入USB都没反应

可以通过修改虚拟机配置文件解决: [参考链接](https://zhuanlan.zhihu.com/p/451966343) 里面评论区大佬的方法

用记事本打开虚拟机的.vmx文件, 加上`usb.restrictions.defaultAllow = TRUE`就可以检测到了

2. sdkmanager有些按键看不到
SDK manager的界面对显示屏大小有要求, 如果显示屏太小的话会显示不全, 可以手动把显示屏的大小调大, 保证所有的东西都会显示
## 配置

刷好jetpack 4.6之后现有的:

`cuda10.2 + TensorRT8.2 + python3.6.9`

1. 使用TensorRT + trtexec: 加入环境变量
`export PATH=/usr/src/tensorrt/bin:$PATH`, 保险起见可以加上`export OPENBLAS_CORETYPE=ARMV8`
2. 下载pip和pip3: `sudo apt-get install python-pip python3-pip`
3. 下载jtop: 用来观察运行状况
    * `sudo -H pip3 install -U jetson-stats`之后重启
    * terminal中`jtop`启动
4. 下载cmake: `sudo apt-get install git cmake`
5. Ubuntu换源: 和nano一样
6. 下载torch
    * 注意使用的torch的版本, 需要从官网下载对应版本的whl文件
    * 首先安装依赖库`sudo apt-get install python3-pip libopenblas-base libopenmpi-dev`
    * 图片读取的依赖库`sudo apt-get install libjpeg-dev zlib1g-dev`
    * 安装Pillow库 `pip3 install pillow==8.4.0`
    * 安装Cython  `pip3 install Cython`
    * 从whl安装`pip3 install torch-1.6.0a0+b31f58d-cp36-cp36m-linux_aarch64.whl`
7. 安装torchvision
    * 从官网copy torchvision
    * 如果没有配置ffpmeg会报错, 可以直接从代码里面把`if has_ffpmeg`注释掉
    * 安装 ` sudo python3 setup.py install`
8. 安装pycuda
    * 下载官网的安装包
    * 配置路径`python3 configure.py --cuda-root=/usr/local/cuda-10.2`
    * 安装依赖库`sudo apt-get install libboost-all-dev`
    * 安装`sudo python3 setup.py install`