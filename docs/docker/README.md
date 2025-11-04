# Docker

## 安装docker

```shell
# 1. 卸载当前可能存在的 Docker 残留
sudo apt-get remove docker docker-engine docker.io containerd runc

# 2. 安装基础依赖
sudo apt-get update
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# 3. 使用阿里云镜像源
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 4. 添加阿里云 Docker 源
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. 更新并安装 Docker
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# 6. 启动 Docker 服务
sudo systemctl start docker
sudo systemctl enable docker

# 7. 验证安装
sudo docker --version
sudo systemctl status docker

# 8. 将当前用户加入docker组
sudo usermod -aG docker $USER
newgrp docker
```

## 安装docker-compose

```shell
# 更新包列表
sudo apt update

# 安装 docker-compose
sudo apt install -y docker-compose

# 验证安装
docker-compose --version
```

## 镜像加速

```shell
# 编辑daemon.json 没有就创建
sudo vim /etc/docker/daemon.json

# 添加镜像加速，当前可用的镜像：https://n3.ink/helper docker.1ms.run
# https://1ms.run/
sudo tee /etc/docker/daemon.json <<- EOF
{
  "insecure-registries": ["docker.1ms.run"],
  "registry-mirrors": ["https://docker.1ms.run"]
}
EOF

# 重启docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 检查镜像

```shell
sudo docker run hello-world
```
