# lenovo-miix-520-hackintosh-10.14-CLOVER
macos 10.14 on lenovo miix 520 is almost perfect,The English version of README is coming.

miix 520 接近完美黑苹果，[点击查看远景贴](http://bbs.pcbeta.com/viewthread-1795824-1-1.html)

#### 目录

1. 配置信息
2. 系统运行截图
3. 正常工作
4. 不正常工作
5. bug与解决方式
	- bug1:触摸板与触摸屏不能同时使用
	- bug2:睡眠唤醒后键盘失效(重新拔插后正常)
6. 更新日志
7. 常见问题解答
	- 1:安装前的准备有哪些？
	- 2:为什么安装时进度条走一半就卡住了？
	- 3:为什么安装后触屏不能用？
	- 4:为什么睡眠唤醒后键盘与触摸板就不能用了？
	- 5:为什么多指触控不正常？
	- 6:我可以随意更新系统吗？

## 1.配置信息

- 品牌型号：联想miix 520
- cpu：i5 8250u 
- 显卡：uhd620
- 内存：16G
- 声卡：alc298
- 无线网卡：bcm94352z
- 屏幕大小：12.2寸
- 分辨率：1920x1200
- NVME硬盘：samsung pm961 1TB
- bios：6ncn13ww

## 2.系统运行截图

- CLOVER多系统选择界面：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/clover.png" width="95%" /></div>

- Mac OS运行图：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/about.png" width="95%" /></div>

## 3.正常工作

1. 声显网三卡：OK
2. usb：OK
3. 电量显示：OK
4. 亮度调节：OK
5. 变频：OK
6. 盒盖睡眠 开盖唤醒：OK
7. 触摸屏、手写笔：OK
8. 蓝牙：OK
9. usb键盘、鼠标唤醒：OK
10. SD读卡器：测试...

## 4.不正常工作

1. I2C的重感、摄像头（貌似无解）
2. ~~SD读卡器（我的是LTE版本的，没有sd模块，这个无法测试，可能有解~~
3. iMessage（没有三码，可能有解）
4. 指纹识别（未研究，可能无解）

## 5.bug与解决方式

### bug1:触摸板与触摸屏不能同时使用

这个bug应该来自于VoodooI2CHID.kext驱动，因为它会让触摸板加载这个驱动，而触摸板加载这个驱动后就没法使用了，所以解决这个bug的方法是修改VoodooI2CHID.kext驱动，让它不能识别触摸板，具体修改方法是删掉VoodooI2CHID.kext/Contents/Info.plist里的这一段：

    <key>VoodooI2CHIDDevice Multitouch HID Event Driver</key>
    <dict>
    <key>CFBundleIdentifier</key>
    <string>com.alexandred.VoodooI2CHID</string>
    <key>DeviceUsagePairs</key>
    <array>
    <dict>
    <key>DeviceUsage</key>
    <integer>4</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    <dict>
    <key>DeviceUsage</key>
    <integer>5</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    <dict>
    <key>DeviceUsage</key>
    <integer>2</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    </array>
    <key>IOClass</key>
    <string>VoodooI2CMultitouchHIDEventDriver</string>
    <key>IOProbeScore</key>
    <integer>200</integer>
    <key>IOProviderClass</key>
    <string>IOHIDInterface</string>
    </dict>

我上传的clover里集成的VoodooI2CHID.kext默认已经去掉了这一段代码了，这里介绍这个bug，是为了避免更新VoodooI2C系列驱动时忘记修改驱动而导致触摸板无法使用的问题。


### bug2:睡眠唤醒后键盘失效(重新拔插后正常)

这是个奇葩的bug，可能和键盘硬件有关，经过搜索，发现不少用户遇到了唤醒后鼠标失效、键盘失效的问题，重新拔插后又能正常使用了，针对这个bug，解决办法就是安装sleepwatcher来监控系统的睡眠唤醒，在电脑唤醒时执行一条重连usb设备的命令，该补丁包默认适合miix 520的键盘bug，如果想要用到别的电脑上解决重连usb的问题，需要修改/usr/local/acai/patch，里面的0x14500000是miix 520键盘的usb口的地址。

经过测试，按照如下步骤就能解决miix 520的键盘失效问题：

1. 安装brew，终端执行如下命令


    `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

2. 安装sleepwatcher，终端执行如下命令

	`brew install sleepwatcher`

3. 下载解压补丁包 : 解决唤醒后需要拔插键盘问题的方案.zip
4. 终端进入补丁包目录，执行如下命令

	`sudo sh ./patch.sh`

## 6.更新日志

### v1.3 -- 2019-01-26

- 更新clover版本。
- 添加开机声音驱动文件与声音资源文件，如何使用 请看 常见问题解答。
- 修改电池型号信息为miix 520，无关紧要的一条更新。
- 默认不集成sd卡驱动，如需要sd卡驱动，请看 常见问题解答。


### v1.2 -- 2018-12-30

- 启用VirtualSMC.kext，放弃fakesmc.kext。
- 启用SMCBatteryManager.kext，放弃ACPIBatteryManager.kext。
- 更新clover版本为4831。
- 更新kexts内各驱动版本。
- 显卡仿冒19168086，解决仪表盘添加小部件时水波纹花屏问题。
- CLOVER完全重新从我自用的提取，不是增量更新。
- 测试添加SD读卡器驱动。

### v1.1 -- 2018-10-14

- 亮度调节方式更改为AppleBacklightFixup，[详细信息点击查看](https://bitbucket.org/RehabMan/applebacklightfixup)。
- 设置默认启动上次启动的系统启动项，倒计时为10秒。

### v1.0 -- 2018-10-11

- 初始版本.

## 7.常见问题解答

### 1:安装前的准备有哪些？

### 2:为什么安装时进度条走一半就卡住了？

### 3:为什么安装后触屏不能用？

### 4:为什么睡眠唤醒后键盘与触摸板就不能用了？

### 5:为什么多指触控不正常？

### 6:我可以随意更新系统吗？


