# NFS网络文件

## 准备两个ubantu系统

> 存储服务器ip：192.168.100.105
> 
> 挂载服务器ip：192.168.100.102

## 在存储服务器上配置NFS（192.168.100.105）

> 1000是用户id，通过id -u 或者id -g查看

```shell
# 安装NFS服务
sudo apt update
sudo apt install nfs-kernel-server

# 创建共享目录
sudo mkdir -p /odoo_shared_data
sudo chown 1000:1000 /odoo_shared_data

# 配置导出
sudo nano /etc/exports
```

```ini
/odoo_shared_data 192.168.100.102(rw,sync,no_subtree_check)
```

```bash
# 重新加载NFS配置
sudo exportfs -ra
sudo systemctl restart nfs-server
# 检查NFS共享
sudo showmount -e localhost
```

## 在挂载服务器上挂载NFS（192.168.100.102）

```bash
# 安装NFS客户端
sudo apt install nfs-common

# 创建挂载点
sudo mkdir -p /mnt/odoo_shared

# 挂载NFS
sudo mount -t nfs 192.168.100.105:/odoo_shared_data /mnt/odoo_shared

# 设置开机自动挂载
echo "192.168.100.105:/odoo_shared_data /mnt/odoo_shared nfs defaults 0 0" | sudo tee -a /etc/fstab

```
