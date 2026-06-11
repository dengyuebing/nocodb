# nocodb usage
nocodb usage

# Website

- 官网：<https://nocodb.com/>

- GitHub: https://github.com/nocodb/nocodb

# Install from Linux(binary)

## 一、确认环境
- Linux x64（主流服务器：Ubuntu 20.04/22.04、Debian、CentOS 7/8）
- 内存建议 ≥1G（至少 512M）
- 网络开放 **8080** 端口（或你自定义的端口）

---

## 二、从 GitHub 下载 Linux 版本
### 方式1：从 Release 页面手动下载
1. 打开：https://github.com/nocodb/nocodb/releases
2. 找到最新版，下载 **Linux x64** 二进制包  
   文件名类似：`nocodb-linux-x64`（或带版本号）
3. 上传到你的 Linux 服务器（用 `scp` 或 FTP）

### 方式2：服务器直接用 curl 下载（推荐）
```bash
# 直接拉官方最新 Linux x64 二进制
curl -fsSL https://get.nocodb.com/linux-x64 -o nocodb
```

---

## 三、安装（解压/授权）
### 1. 放到安装目录（规范一点）
```bash
sudo mkdir -p /opt/nocodb
sudo mv nocodb /opt/nocodb/
cd /opt/nocodb
```

### 2. 加执行权限
```bash
chmod +x nocodb
```

### 3. 测试运行（前台启动，看是否正常）
```bash
./nocodb
```
- 看到类似 `NocoDB started on 0.0.0.0:8080` 就成功
- 浏览器访问：`http://你的IP:8080`
- 第一次会让你创建管理员账号

> 按 `Ctrl + C` 停止前台运行。

---

## 四、配置（数据持久化 + 自定义端口/数据库）
默认用 SQLite，数据存在当前目录的 `.nocodb`，建议固定目录。

### 1. 创建数据目录
```bash
sudo mkdir -p /data/nocodb
sudo chmod 777 /data/nocodb  # 简单权限，生产可更严格
```

### 2. 用环境变量启动（常用配置）
```bash
# 自定义端口 + 指定数据目录
NC_PORT=8080 NC_DATA=/data/nocodb ./nocodb
```

### 3. 连接外部 MySQL/PostgreSQL（可选）
```bash
# MySQL 示例
NC_DB_TYPE=mysql2 \
NC_DB_HOST=127.0.0.1 \
NC_DB_PORT=3306 \
NC_DB_USER=root \
NC_DB_PASS=你的密码 \
NC_DB_NAME=nocodb \
NC_PORT=8080 \
NC_DATA=/data/nocodb \
./nocodb
```

---

## 五、后台运行 + 开机自启（systemd）
生产环境必须用 systemd 托管，否则退出终端就停了。

### 1. 新建服务文件
```bash
sudo nano /etc/systemd/system/nocodb.service
```

内容复制：
```ini
[Unit]
Description=NocoDB
After=network.target

[Service]
User=root
WorkingDirectory=/opt/nocodb
Environment="NC_PORT=8080"
Environment="NC_DATA=/data/nocodb"
ExecStart=/opt/nocodb/nocodb
Restart=always

[Install]
WantedBy=multi-user.target
```

### 2. 生效并启动
```bash
sudo systemctl daemon-reload
sudo systemctl start nocodb
sudo systemctl enable nocodb
```

### 3. 查看状态
```bash
systemctl status nocodb
```
显示 `active (running)` 就是正常。

---

## 六、防火墙开放端口（如果有）
### ufw（Ubuntu）
```bash
sudo ufw allow 8080
```

### firewalld（CentOS）
```bash
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

---

## 七、访问
浏览器打开：
```
http://你的服务器IP:8080
```
首次注册管理员账号即可使用。

---

## 八、常见问题
- **端口被占用**：改 `NC_PORT=xxxx`
- **数据丢了**：一定要配置 `NC_DATA` 指向固定目录
- **启动失败**：`journalctl -u nocodb -f` 看日志

---

