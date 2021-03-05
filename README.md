<h1 align="center">Remote WOL 项目整体介绍</h1>

<p align="center"><img src="https://img.shields.io/badge/Licence-MIT-green.svg?style=for-the-badge" /></p>

### 项目介绍

通过软硬件结合的方式通过互联网远程唤醒局域网中的电脑

项目包括：

* 硬件部分：基于`ESP32`开发的 [Remote WOL MicroPython](https://gitee.com/walkline/remote-wol-micropython)

* 软件部分：

	1. 基于 [uni-app](https://uniapp.dcloud.io/) 开发的 [Remote WOL Uni-App](https://gitee.com/walkline/remote-wol-uni-app) (安卓)
	2. 基于`PHP`开发的 [在线更新管理器](https://gitee.com/walkline/online-update-manager)
	3. 基于`PHP`开发的 [数据转发接收接口](https://gitee.com/walkline/data_receiving_interface)

### 使用方法

远程控制部分基于`MQTT 服务`进行数据交换，所以提前需要注册一个用户账号并准备两个客户端 ID，一个用于硬件设备登录一个用于 App 登录

推荐使用 [扇贝物联](https://jniot.xyz/) 提供的`MQTT 服务`

#### 硬件部分

为了便于使用，现已将`Remote WOL MicroPython`项目文件集成到固件文件中

1. 克隆或下载本项目所有文件
2. 将**最新版本**的固件烧录到`ESP32 开发板`
3. 依次向`开发板`上传`config.py`和`main.py`文件

	* `config.py`：硬件配置文件，用户可以根据需求自行修改（**版本号相关字符串切勿修改**）
	* `main.py`：程序入口文件，调用固件中的主程序

4. `开发板`板载 Led 开始闪烁后检查是否存在类似`wol_xxxxxxxxxxxx`的热点

> `开发板`处于未设置状态的时候板载 Led 会一直闪烁，正确设置并正常运行以后 Led 处于常亮状态

#### 软件部分：手机 App

1. 从`Remote WOL Uni-App`项目 [releases](https://gitee.com/walkline/remote-wol-uni-app/releases) 中下载**最新版本**的`apk`文件并安装到手机上
2. 打开`设置`页面，填写相关参数，测试无误后点右上角`保存`按钮

	> 如果使用`扇贝物联`提供的 MQTT 服务，务必打开`扇贝物联`开关并填写注册时的用户名

3. 打开`硬件列表`页面，点击右上角加号，在`添加硬件`页面下拉搜索周围的硬件设备

4. 如果搜索到硬件设备，点击进入并填写相关参数，经`测试`无误后`保存`设备参数

	> 如果使用`扇贝物联`提供的 MQTT 服务，务必打开`扇贝物联`开关并填写注册时的用户名

5. 回到首页，点击右下角的加号，选择`手动输入`，添加需要远程唤醒的电脑
6. 输入`名称`和`MAC 地址`后`保存`即可

7. 设备运行正常，App 设置正确的情况下，直接点击刚刚添加的电脑即可实现远程唤醒

> App 设置正确的情况下，首页右上角的灯泡会处于发光状态

#### 软件部分：在线更新管理器

`在线更新管理器`用于管理手机 App 在线更新信息，包括上传 App 文件，查看管理 App 升级信息等功能，具体使用方法参考 [在线更新管理器](https://gitee.com/walkline/online-update-manager) 中的说明

#### 软件部分：数据转发接收接口

`数据转发接收接口`用于接收并保存`硬件版本 1`上报的温度数据，具体使用方法参考 [数据转发接收接口](https://gitee.com/walkline/data_receiving_interface) 中的说明

### 硬件复位

硬件正确设置以后是无法再次进行添加操作的，如果想要复位硬件设置

* 长按`板载 BOOT`按钮 5 秒以上，直到 Led 闪烁为止
* 从 App 中删除硬件设备

### 已知问题

#### App 部分

- [x] 硬件添加之后无法修改设置。原因是硬件进入正常工作模式后不提供`WebSocket`服务，App 无法与之沟通，~~暂时懒得解决~~，增加了一个硬件设备详情页，用来显示硬件相关的一些参数，同时可以修改备注，已添加的硬件设备将不支持修改配网信息等参数

- [ ] 硬件添加之后大概需要 3 分钟时间才会收到硬件的上线消息。App 上的`mqtt`客户端在添加硬件的时候是处于断开状态的，恢复速度也许取决于手机连接网络并且`mqtt`客户端正好在进行重连。。。

- [x] 安卓权限的问题导致 App 内无法修改其它 App 或者系统连接过的热点，~~所以如果添加硬件的时候点击`测试按钮`长时间无响应，应该检查之前是否手动连接过硬件发出的热点~~，所以增加了相关提示，并在连接测试之后主动删除了硬件设备的热点信息

#### 无法分辨责任的问题

- [x] 在排除权限问题的情况下，有的时候点击`测试按钮`还是长时间无响应也没有报错，经过观察发现，硬件上开启的热点和`WebSocket`服务都正常，反复重启硬件或者 App 重新尝试连接都可以解决问题，~~但是原因未知。。。~~ 这个问题是由于 App 打开 WebSocket 超时导致的，之前的超时时间为`10 秒`，此时如果手机自动重连无线网络的时间超过这个时间就会出现长时间无响应的问题

### 如何烧写固件

请参考 [如何刷写固件](https://gitee.com/walkline/esp32_firmware#%E9%99%84%E5%BD%951%E5%A6%82%E4%BD%95%E5%88%B7%E5%86%99%E5%9B%BA%E4%BB%B6) 相关内容

### 合作交流

* 联系邮箱：<walkline@163.com>
* QQ 交流群：
    * [走线物联](https://jq.qq.com/?_wv=1027&k=xtPoHgwL)：163271910
    * [扇贝物联](https://jq.qq.com/?_wv=1027&k=yp4FrpWh)：31324057

<p align="center"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_walkline.png" width="300px" alt="走线物联"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_bigiot.png" width="300px" alt="扇贝物联"></p>
