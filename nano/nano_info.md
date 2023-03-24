# 相关使用说明

## 怎么用的问题

* 怎么连校园网: 需要在显示屏上操作, 手动连接

## 一些继承的资料和nano的基本信息

* Username@ip: dlinano@192.168.55.1:dlinano
  * 这里的ip是固定硬件的ip
  * 连接网络后的ip需要用`ifconfig`查询`wlan0`的内容
* 软件版本: 取决于刷机时使用的`jetpack`的版本
  * jetpack版本: `sudo apt-cache show nvidia-jetpack`, 现在用的是4.4* OpenCV: 4.1.1 `dpkg -l | grep opencv`
  * cuda版本: 现在使用的是11.6 `nvcc -V`
  * Night SyStem版本: 现在的是2021.5 `nsys -v`
  * TensorRT: 7.1.3 `dpkg -l | grep TensorRT`
    * 位置在`/usr/src/tensorrt`
    * 可执行文件在`/usr/src/tensorrt/bin/trtexec`
    * 使用需要添加环境变量
  * cuDNN: 8.0.0 `dpkg -l | grep cuDNN`
* 如何控制风扇: `sudo sh -c 'echo 0(speed) > /sys/devices/pwm-fan/target_pwm'`
  * 开机的风扇转速: 在`/etc/rc.local`文件中, 修改此文件中的转速即可(目前权限是755, 需要用`chmod xxx file`把文件权限修改成777)
* 查询此时处在的功耗模式
  * `nvpmodel -q`查询模式; `nvpmodel -m 0`正常模式; `nvpmodel -m 1`低功耗模式
* 视觉功能库
  * 范例在`/home/dlinano/VisionWorks-1.6-Samples/bin/aarch64/linux/release`
* 如何检查摄像机参数
  * `v4l2-ctl --list-devices` 有哪些摄像头
  * `v4l2-ctl --device=/dev/video0 --list-formats-ext` 摄像头具体参数
  * `gst-inspect-1.0 <keyword>`查询具体的设备的参数 usb摄像头的关键字是`nvv4l2camerasrc`, CSI摄像头的关键字是`nvarguscamerasrc`