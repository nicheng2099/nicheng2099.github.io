# entOS 7 yum安装 MySQL 5.7

1、下载命令

```
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

2、yun源安装命令

```
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
```

3、使用yum命令安装mysql

```
yum -y install mysql-server
```

4、启动mysql

```
systemctl start mysqld
```

5、检查是否安装启动成功

```
systemctl status mysqld
```



6、查看默认密码

```
grep 'temporary password' /var/log/mysqld.log
```

7、登录修改密码

```
mysql -u root -p
```

8、修改密码

因为5.7及以上版本的数据库对密码做了强度要求，默认密码的要求必须是大小写字母数字特殊字母的组合且至少要8位长度

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '新的密码Lichao.123';
```

9、允许远程登录

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '密码Lichao.123' WITH GRANT OPTION;
```

10、如仍然无法访问则需关闭防火墙或者开放3306端口访问权限

附：CentOS 7 关闭防火墙命令

```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
systemctl restart firewalld.service
```