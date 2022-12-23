# Study210开发板刷安卓系统

## 一、破坏BootLoader

> 1.用USB转串口线连接电脑与开发板，打开SecureCRT串口监视软件（此步骤注意：开发板上使用UART2）.

![image-20220415200533530](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415200533530.png)

> 2.长按开发板POWER按键开机，进入控制台。(让secureCRT读完全部信息)
>
> 3.输入root（password:123456）
>
> 4.然后输入`busybox dd if=/dev/zero of=/dev/mmcblk0 bs=512 seek=1 count=1 conv=sync`
> 
> 5.回车后显示
```
1+0 records in
1+O records out
```

> 6.然后再输入 sync 命令 ，此时第1扇区已经破坏。
>  此时重新启动开发板就无法启动了
>  
## 二、SD卡刷机(烧录uboot到SD卡中)

> 1.将SD卡插入到电脑的SD卡槽，使用SD卡烧录工具x210_Fusing_Tool 进行烧录。

![image-20220415202442316](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415202442316.png)
`注意：这里由于之前没有保存截图，插卡后，此软件会自动识别，然后在自己的电脑里选择一个uboot.bin文件。然后点击START.`

> 2.完成后将SD卡插入开发板的SD卡槽。然后开机就可以进入uboot界面了。在uboot开机自动启动倒数3秒之内迅速按下电脑回车键，打断自动启动。（否则会自动启动iNand中的android）
## 三、fastboot 下载安装镜像
> 1.用USB线的USB口 连接电脑，另一端连接开发板的OTG口，然后在SecureCRT 的uboot控制台输入fastboot命令，这时电脑会识别USB硬件，然后需要安装驱动。

> 2.然后将电脑内的fastboot压缩包解压到一个容易找到的文件目录下，如 D盘。打开windows控制台进入到相应目录下。

![image-20220415202849623](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415202849623.png)

> 3.下一步 在fastboot文件夹下，新建一个文件夹存放要烧录的文件，如Android
> Android中应该包含的文件（由于这里我烧写的是安卓系统）

![image-20220415203108592](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415203108592.png)



> 4.进行内核和系统的烧写 ，具体代码如下：

![image-20220415203639928](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415203639928.png)

> 同时在SecureCRT下可以看到下载结果



> 5.最后在windows控制台下输入` fastboot reboot`命令重启系统即可。

## 四、**dnw 刷机** (用fastboot刷Android )

- 准备事项：已安装好相应的驱动、串口线（连接的是UART2）和USB已经接好，dnw已打开。

![image-20220415204109660](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415204109660.png)

注意：

（1）安装`SecBulk.sys Njsmodi 2416 dnw drive`的驱动程序在`\X210V3S_A\tools\USB驱动\dnw_driver`下,安装驱动需要禁用数字签名(可参考[win10如何永久关闭数字签名](https://blog.csdn.net/m0_37182543/article/details/80541418?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165002148616780271549615%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165002148616780271549615&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-80541418.142^v9^pc_search_result_cache,157^v4^control&utm_term=Windows%E7%A6%81%E7%94%A8%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D&spm=1018.2226.3001.4187))

（2）在使用dnw过程中需要长按电源键，否则会断开连接。

刷机步骤：

> 1.将拨码开关拨到USB启动位置。

![image-20220415205448252](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415205448252.png)

> 2.按住开机键(长按不放)，DNW 配置下载地址为0xd0020010 ，然后transmit x210_usb.bin

![image-20220415205750621](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415205750621.png)

![image-20220415205902526](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415205902526.png)



![image-20220415210139477](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415210139477.png)

> 3.DNW 修改下载地址为 0x23e00000 ，下载uboot.bin,注意下载的同时要看SecureCRT界面，[串口](https://so.csdn.net/so/search?q=串口&spm=1001.2101.3001.7020)终端有信息打印出来，在3s倒计时内按下回车键，进入shell界面。

> 4.回到secureCRT

```
输入fdisk -c 0 （进行分区）
输入fastboot （查看分区）
```
> 5.cmd打开系统终端，切换到fastboot目录分别执行下列红框的命令：

![image-20220415203639928](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220415203639928.png)

全部执行完成后，将拨码开关切换回原来的状态，重新启动，此次刷机完成。

---

此文章参考于[S5PV210 Study210开发板刷系统](https://blog.csdn.net/madao1234/article/details/101104872)