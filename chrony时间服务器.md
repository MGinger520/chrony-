# **云计算-项目三：chrony时间服务器**

1、**准备工作：**

|     名称：      | 内核： | 内存： | 硬盘： |   备注：   |
| :-------------: | :----: | :----: | :----: | :--------: |
| 【主】CenOS-7-1 |   2    |   2    |   60   | A01-server |
| 【子】CenOS-7-2 |   2    |   2    |   20   | 02-server  |

#### 2、**最终实现目标：**

实现各服务器的时间同步。

#### 3、**项目要求：**

1台Centos7作为chrony对时客户端；

1台Centos7作为chrony对时服务端，同时与网络授时中心时间同步；

#### 4、项目要点：

查看各服务器的时区是否为Asia/Shanghai，否则先调整时区再对时；

​       国内常用授时中心：

阿里云：ntp.aliyun.com

中科院国家授时中心：ntp.ntsc.ac.cn

chrony官网：[https://chrony.tuxfamily.org](https://chrony.tuxfamily.org/)
chrony官方文档：https://chrony.tuxfamily.org/documentation.html

#                             实训开始：

#####       姓名：马江涛                                              学号：2150204154



#### 一、初次检查服务器

1-1、创建2台服务器，并改好名字，之后检查网络、端口、防火墙、时间、地区 是否正常以及开启关闭。

检查地区时间：查看时间命令：date -R

![1](https://tva4.sinaimg.cn/large/0072FrUOgy1h1cs7oudo5j30m80gowmc.jpg)

![111](https://tva1.sinaimg.cn/large/0072FrUOgy1h1csel3c8bj30dm02o405.jpg)

1-2、用Xshell连接，安装vim、rpm、yum

1-3、修改主机名字：`vim /etc/hostname`  之后`reboot`

![01](https://tva3.sinaimg.cn/large/0072FrUOly1h1csps0w1qj30r1032djr.jpg)

1-4、IP修改和ping通

进入cd /etc/sysconfig/network-scripts/ 

vim ifcfg-ens32 【ens32是自己的网卡名字，你也可以进行修改，但自行百度】

 查找你的ens网卡并进行修改：

![02](https://tvax3.sinaimg.cn/large/0072FrUOgy1h1ct16ez1kj30r50fse0e.jpg)

配置好并测试成功 ping截图：

![03](https://tvax1.sinaimg.cn/large/0072FrUOgy1h1ct309g22j30cn05d77v.jpg)

#### 二、开始安装chrony

2-1、`yum install cheony -y`  

![04](https://tvax3.sinaimg.cn/large/0072FrUOgy1h1ct70s0nlj30qi05hn1e.jpg)

2-2、开启chrony服务

```
systemctl start  chronyd
systemctl enable chronyd
systemctl status chronyd

```

```
#安装
yum install chrony
#启动以及启用开机自启
systemctl start chronyd
systemctl enable chronyd
#查看状态
systemctl status chronyd
#停止以及禁用开机自启
systemctl stop chronyd
systemctl disable chronyd


```

2-3、配置chrony服务 server配置

```
vim /etc/chrony.conf  之后wq保存

# server配置，vim /etc/chrony.conf 添加以下

##添加两个时间源，分别是阿里云和东北大学的时间同步服务器，
iburst表示加急
server time1.aliyun.com iburst
server time.neu.edu.cn iburst


# Allow NTP client access from local network.
allow all # 允许所有主机从server端同步时间
 
# Serve time even if not synchronized to a time source.
local stratum 10
# 即使server端无法从互联网同步时间，也同步本机时间至client

```

2.4、client配置【客户端配置】

```
vim /etc/chrony.conf 注释原server配置，改为prod1机器IP地址
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst

server 192.168.100.100 iburst
```

![06](https://tva3.sinaimg.cn/large/0072FrUOly1h1cuv5ts62j31hb0p34qp.jpg)



三、重启 测试服务

```
# 都重启服务
systemctl restart chronyd
# 服务端查看server端在线情况和时间同步情况
chronyc activity
```

![07](https://tva3.sinaimg.cn/large/0072FrUOly1h1cv164eswj316n06s7bz.jpg)



最后  为了 检查客户端是否成功的连接服务端，我们可以使用指定命令来检查是否连接成功！！  检查是否对特定主机可访问当前服务器

```
chronyc accheck 192.168.100.100【记得要用server检查客户端】
208 Access allowed【显示这个结果未成功，其他为失败】
```

![08](https://tva1.sinaimg.cn/large/0072FrUOly1h1cv1jj0v9j30lk07y0ye.jpg)

显示当前时间源的同步信息

```
chronyc sources 
```

![09](https://tvax4.sinaimg.cn/large/0072FrUOly1h1cv2zf2ivj30ve07xn4t.jpg)





参考文档：

```
https://blog.csdn.net/xiaochenwj1995/article/details/119781250

https://blog.csdn.net/wangjie72270/article/details/122196213
```