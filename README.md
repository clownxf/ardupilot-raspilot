#树莓派飞控RasPilot#

###硬件安装和接线###

###写入系统镜像到树莓派SD卡###

###配置树莓派WiFi连接###
编辑/etc/wpa_supplicant/wpa_supplicant.conf文件<br>
　`sudo nano /etc/wpa_supplicant/wpa_supplicant.conf`<br>
将文件中的<br>
　`ssid = “emlidltd”`<br>
　`psk = “emlidltd”`<br>
　`key_mgmt = wpa-psk`<br>
改为自己WiFi的SSID和密码。<br>
<br>

###安装和设置飞控程序###
1 下载飞控程序<br>
　ArduCopter.elf 百度网盘下载地址：http://pan.baidu.com/s/1hqJ9ZbY （1、2代树莓派均可使用）<br>
　下载后放到树莓派用户根目录，即/home/pi目录中<br>
<br>
2 运行飞控程序<br>
　`sudo ./ArduCopter.elf -B /dev/ttyAMA0`<br>
　ArduCopter.elf可以带-A -B -C -E四个参数，其中：<br>
　* -A primary数传接口<br>
　* -B primary GPS接口<br>
　* -C secondary数传接口<br>
　* -E sencondary GPS接口<br>
<br>
　raspilot中-B使用树莓派自带的串口`/dev/ttyAMA0` -C默认设置为从STM32转发的串口<br>
　如果要使用WiFi的udp作为数传的话，可以输入：<br>
　`sudo ./ArduCopter.elf -A udp:192.168.1.100:14550 -B /dev/ttyAMA0`<br>
　其中`192.168.1.100`改为PC端的IP地址<br>
<br>
3 设置开机自动运行<br>
　修改/etc/rc.local文件，在`exit 0`前加入：<br>
　`sudo ./home/pi/ArduCopter.elf -B /dev/ttyAMA0 > /home/pi/startup_log &`<br>
　重新启动后飞控程序就会自动运行<br>
<br>

###编译飞控代码###
从Copter-3.3开始，ardupilot的编译需要使用4.7版本以上的gcc。而树莓派系统上还是4.6版本的gcc，所以建议在Ubuntu下面交叉编译代码。
这里使用树莓派官方提供的交叉编译工具<br>
<br>
1 下载树莓派编译工具，例如这里放到/opt目录下<br>
　`sudo git clone --depth 1 https://github.com/raspberrypi/tools.git /opt/tools`<br>
<br>
2 设置环境变量<br>
　32位系统输入：`export PATH=/opt/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin:$PATH`<br>
　64位系统输入：`export PATH=/opt/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin:$PATH`<br>
<br>
3 编译ardupilot<br>
* 从GitHub上获取源代码：<br>
　`git clone https://github.com/raspilot/ardupilot-raspilot.git`<br>
* 进入ArduCopter目录后编译：<br>
　`cd ardupilot-raspilot/ArduCopter`<br>
　`make raspilot`<br>
* 通过WiFi同步到树莓派：<br>
　`rsync -avz /tmp/ArduCopter.build/ArduCopter.elf pi@192.168.1.100:/home/pi/`<br>
　其中`192.168.1.100`改为树莓派的实际IP地址<br>
