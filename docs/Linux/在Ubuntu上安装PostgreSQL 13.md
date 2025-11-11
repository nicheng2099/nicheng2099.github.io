# 在Ubuntu上安装PostgreSQL 13

## 方法一：使用官方APT仓库（推荐）

### 1. 添加PostgreSQL官方仓库

```shell
# 更新系统包列表
sudo apt update

# 安装必要的依赖
sudo apt install wget curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release ubuntu-keyring

# 导入PostgreSQL签名密钥
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /usr/share/keyrings/postgresql-key.gpg

# 添加PostgreSQL仓库
echo "deb [signed-by=/usr/share/keyrings/postgresql-key.gpg] http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
```

### 2. 安装PostgreSQL 13

```shell
# 更新包列表
sudo apt update

# 安装PostgreSQL 13
sudo apt install postgresql-13 postgresql-client-13
```

## 方法二：使用Ubuntu默认仓库

```shell
# 更新包列表
sudo apt update

# 安装PostgreSQL（这会安装Ubuntu仓库中的最新版本）
sudo apt install postgresql postgresql-contrib
```

## 安装后的配置

### 1. 检查PostgreSQL服务状态

```shell
sudo systemctl status postgresql@13-main
# 或者
sudo systemctl status postgresql
```

### 2. 启动和启用服务

```shell
# 启动服务
sudo systemctl start postgresql@13-main

# 设置开机自启
sudo systemctl enable postgresql@13-main

# 重启服务
sudo systemctl restart postgresql@13-main
```

### 3. 连接到PostgreSQL

```shell
# 切换到postgres用户
sudo -u postgres psql

# 或者直接连接
sudo -u postgres psql -d postgres
```

### 4. 基本配置

```shell
# 修改postgres用户密码
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'your_password';"

# 创建新数据库
sudo -u postgres createdb mydatabase

# 创建新用户
sudo -u postgres createuser --interactive
```

## 配置文件位置

- 主配置文件：`/etc/postgresql/13/main/postgresql.conf`

- 访问控制文件：`/etc/postgresql/13/main/pg_hba.conf`

- 数据目录：`/var/lib/postgresql/13/main/`

## 常用管理命令

```shell
# 启动服务
sudo systemctl start postgresql@13-main

# 停止服务
sudo systemctl stop postgresql@13-main

# 重启服务
sudo systemctl restart postgresql@13-main

# 查看服务状态
sudo systemctl status postgresql@13-main

# 查看日志
sudo tail -f /var/log/postgresql/postgresql-13-main.log
```

## 验证安装

```shell
# 检查版本
psql --version

# 连接到数据库测试
sudo -u postgres psql -c "SELECT version();"
```

安装完成后，你就可以开始使用PostgreSQL 13了。记得根据你的需求适当调整配置文件。
