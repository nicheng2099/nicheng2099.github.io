# 配置Java环境



## 安装JDK

##### 在/opt下创建两个目录

```
cd /opt/
mkdir ./apps
mkdir ./soft
cd soft
```

​	![](image/7.png)

##### 将jdk放入 /opt/soft目录中![](image/image-20230607152234807.png)

##### 解压到apps

```
tar -zxvf jdk-8u181-linux-x64.tar.gz -C /opt/apps/
```

![](image/image-20230607152547352.png)

##### 进入opt下的jdk

```
cd /opt/apps/jdk1.8.0_181/
```

​	![image-20230607152841625](image/image-20230607152841625.png)

##### 进入bin

```
cd ./bin
```

​	![image-20230607153056062](image/image-20230607153056062.png)

> drwxrwxrwx	第一位表示文件类型 ，2~4表示管理员权限，5~7表示组员权限，8~10表示所属者权限	r:读	w:改	x:执行

##### 查找需要卸载的jdk

```
rpm -qa|grep jdk
```

​	![image-20230607153659113](image/image-20230607153659113.png)

##### 卸载

```
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.79-2.5.5.4.el6.x86_64
rpm -e --nodeps java-1.6.0-openjdk-1.6.0.35-1.13.7.1.el6_6.x86_64
```

​	![image-20230607153859607](image/image-20230607153859607.png)

## 配置环境变量

##### 编辑profile文件

```
vim /etc/profile
```

​	![image-20230607154537738](image/image-20230607154537738.png)

##### 在最下方添加以下内容

```
export JAVA_HOME=/opt/apps/jdk1.8.0_181
export PATH=$PATH:$JAVA_HOME/bin
```

​	![image-20230607155306076](image/image-20230607155306076.png)

##### 刷新profile文件

```
source /etc/profile
```

![image-20230607155733278](image/image-20230607155733278.png)

