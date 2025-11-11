# 在 Ubuntu 上部署 Grafana

## 方法一：使用官方APT仓库

### 步骤1：更新系统包

```shell
sudo apt update
sudo apt upgrade -y
```

### 步骤2：安装必要的依赖

```shell
sudo apt install -y software-properties-common wget curl gnupg
```

### 步骤3：添加Grafana官方GPG密钥

```shell
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```

### 步骤4：添加Grafana APT仓库

```shell
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

### 步骤5：更新包列表并安装Grafana

```shell
sudo apt update
sudo apt install grafana -y
```

### 步骤6：启动并启用Grafana服务

```shell
# 启动Grafana服务
sudo systemctl start grafana-server

# 设置开机自启
sudo systemctl enable grafana-server

# 检查服务状态
sudo systemctl status grafana-server
```

## 方法二：使用Docker部署

### 步骤1：安装Docker

```shell
# 安装Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 将当前用户添加到docker组
sudo usermod -aG docker $USER
newgrp docker
```

### 步骤2：使用Docker运行Grafana

```shell
# 创建数据目录
sudo mkdir -p /docker/grafana/data
sudo chmod 777 /docker/grafana/data

# 运行Grafana容器
docker run -d \
  --name=grafana \
  -p 3000:3000 \
  -v /docker/grafana/data:/var/lib/grafana \
  -e "GF_SECURITY_ADMIN_PASSWORD=admin123" \
  grafana/grafana-oss:latest
```

## 配置Grafana

### 步骤1：访问Grafana Web界面

- •
  
  打开浏览器访问：`http://你的服务器IP:3000`

- •
  
  默认用户名：`admin`

- •
  
  默认密码：`admin`（首次登录后会要求修改）

### 步骤2：基本配置

```shell
# 编辑Grafana配置文件
sudo nano /etc/grafana/grafana.ini
```

主要配置项：

```shell
[server]
# 监听地址（设置为0.0.0.0允许外部访问）
http_addr = 0.0.0.0
http_port = 3000

[database]
# 数据库配置（默认使用SQLite）
;type = sqlite3
;path = grafana.db

[security]
# 安全配置
admin_user = admin
admin_password = admin
```

### 步骤3：重启服务应用配置

```shell
sudo systemctl restart grafana-server
```

## 防火墙配置

### 如果使用UFW防火墙

```shell
# 允许3000端口
sudo ufw allow 3000/tcp
sudo ufw reload
```

### 如果使用iptables

```shell
sudo iptables -A INPUT -p tcp --dport 3000 -j ACCEPT
```

## 数据持久化目录

Grafana的重要数据位置：

- •
  
  配置文件：`/etc/grafana/grafana.ini`

- •
  
  数据文件：`/var/lib/grafana`

- •
  
  日志文件：`/var/log/grafana`

- •
  
  插件目录：`/var/lib/grafana/plugins`

## 常用管理命令

```shell
# 启动服务
sudo systemctl start grafana-server

# 停止服务
sudo systemctl stop grafana-server

# 重启服务
sudo systemctl restart grafana-server

# 查看服务状态
sudo systemctl status grafana-server

# 查看日志
sudo journalctl -u grafana-server -f
```

## 备份和恢复

### 备份Grafana数据

```shell
# 备份配置和数据
sudo tar -czf grafana-backup-$(date +%Y%m%d).tar.gz /etc/grafana /var/lib/grafana
```

### 恢复Grafana数据

```shell
# 停止Grafana服务
sudo systemctl stop grafana-server

# 恢复备份
sudo tar -xzf grafana-backup-20231201.tar.gz -C /

# 启动服务
sudo systemctl start grafana-server
```

## 故障排除

### 常见问题解决

1. 1.
   
   **端口被占用**

```shell
# 检查3000端口占用
sudo netstat -tulpn | grep 3000

# 如果被占用，修改Grafana端口或停止占用程序
```

1. 2.
   
   **无法访问Web界面**

```shell
# 检查防火墙设置
sudo ufw status

# 检查Grafana服务状态
sudo systemctl status grafana-server

# 查看日志
sudo journalctl -u grafana-server -n 50
```

1. 3.
   
   **权限问题**

```shell
# 修复数据目录权限
sudo chown -R grafana:grafana /var/lib/grafana
sudo chmod 755 /var/lib/grafana
```

## 后续步骤

1. 1.
   
   **添加数据源**：配置Prometheus、InfluxDB等数据源

2. 2.
   
   **安装插件**：根据需求安装相关插件

3. 3.
   
   **创建仪表板**：设计监控仪表板

4. 4.
   
   **设置警报**：配置监控警报规则

这样就完成了Grafana在Ubuntu上的部署！
