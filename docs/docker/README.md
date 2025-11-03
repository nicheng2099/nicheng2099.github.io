# Docker

## 安装

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
```

## 检查

```shell
sudo systemctl status docker
```


