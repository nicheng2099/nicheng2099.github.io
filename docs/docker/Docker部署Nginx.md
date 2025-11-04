# Docker部署Nginx

## 不添加映射

### 拉取Nginx

```shell
sudo docker pull nginx:latest
```

### 查看是否拉取成功

```shell
sudo docker images
```

### 运行容器

```shell
sudo docker run --name nginx-test -p 8080:80 -d nginx
```

## 添加本地映射

> 文件结构
> 
> /home/lc_link/nginx/
> ├── html/
> 
> │   └── index.html          # 你的自定义页面
> ├── conf.d/
> │   └── app.conf            # Nginx配置
> ├── logs/                   # 日志目录（空，容器启动后会有日志）
> └── certs/                  # SSL证书目录（可选）

### 在任意地方创建映射文件目录

```shell
# 切换到根目录或你习惯的工作目录，这里以 /docker 为例
sudo mkdir -p ~/nginx
cd ~/nginx

# 创建子目录，分别用于存放配置、静态网页、日志
sudo mkdir conf.d html logs certs
```

### 配置静态资源

```shell
# 创建index静态资源
cat > /home/lc_link/nginx/html/index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to My Nginx LC_LINK!</title>
</head>
<body>
    <h1>Success! Nginx is running in a Docker container.</h1>
    <p>This is a test page from the host machine.</p>
</body>
</html>
EOF
```

### 配置conf文件

```shell
# 创建Nginx配置文件
cat > /home/lc_link/nginx/conf.d/app.conf << EOF
server {
    listen 80;
    server_name _;

    # 重要：指向容器内的标准HTML目录
    root /usr/share/nginx/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    # 访问日志和错误日志
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
}
EOF
```

### 使用挂载路径运行容器

```shell
sudo docker run -d \
  --name my-nginx \
  -p 80:80 \
  -v /home/lc_link/nginx/html:/usr/share/nginx/html:ro \
  -v /home/lc_link/nginx/conf.d:/etc/nginx/conf.d:ro \
  -v /home/lc_link/nginx/logs:/var/log/nginx \
  --restart unless-stopped \
  nginx:1.24
```

### 验证部署

```shell
# 1. 检查容器状态
sudo docker ps

# 2. 测试访问 - 这次应该看到你的自定义页面
curl http://localhost

# 3. 验证挂载是否正确
sudo docker exec my-nginx ls -la /usr/share/nginx/html/

# 4. 检查配置文件是否加载
sudo docker exec my-nginx cat /etc/nginx/conf.d/app.conf

# 5. 查看容器日志
sudo docker logs my-nginx
```

## 容器常用命令

```bash
# 停止容器
sudo docker stop my-nginx

# 启动容器
sudo docker start my-nginx

# 重启容器（例如修改配置后）
sudo docker restart my-nginx

# 进入容器内部（用于调试，一般情况下应避免）
sudo docker exec -it my-nginx /bin/bash

# 删除容器（如果需要重新配置）
sudo docker stop my-nginx
sudo docker rm my-nginxx
```
## Nginx
<a class="btn" href="/doc3/Nginx/index.html">学习Nginx</a>