# 配置主机名并添加ip映射



## 修改主机名

```
vim /etc/sysconfig/network
```

​	![](image/1.png)



## 修改网卡设置

> ​	BOOTPROTO的值设为static
>

```
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

​	![image-20230607171429267](image/image-20230607171429267.png)

##### 		修改完后重启网卡 

```
service network restart
```

​	<img src="image/2.png"  />



## 主机映射路径

##### 		windows下增加主机映射路径

​	C:\Windows\System32\drivers\etc\hosts文件

![](image/5.png)

​	列如 192.168.56.47 LG04

​	![](image/3.png)

##### 		linux下增加主机映射路径

```
vim /etc/hosts
```

​	![](image/4.png)

##### 	在xshell中通过主机名链接主机

​	![image-20230607150923606](image/6.png)

