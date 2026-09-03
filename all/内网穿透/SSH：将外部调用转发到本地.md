## SSH 反向隧道：将外部 Webhook 转发到本地服务

### 背景

当你的本地开发机器（如办公电脑）没有公网 IP，但需要接收外部 Webhook 回调（如飞书事件订阅）时，可以通过一台有公网 IP 的跳板机建立 SSH 反向隧道，将外部流量转发到本地。

### 架构

```
外部请求（飞书等）
    │
    ▼
跳板机（wtp.wang:443）
    │  Nginx 反向代理（Docker或者跳板机部署nginx）
    ▼
127.0.0.1:9132（跳板机上的监听端口）
    │  SSH 反向隧道
    ▼
本地电脑（localhost:9132）
    │  Spring Boot 应用
    ▼
实际接口（如 /meego/plugin/listener/on-listen）
```

### 前置条件

- 一台有公网 IP 或域名的跳板机，且已配置 Nginx + SSL 证书
- 本地电脑可以通过 SSH 登录跳板机（密码或密钥均可）
- 本地有服务监听在指定端口（如 9132）

### 第一步：配置 SSH 服务端（跳板机上执行）

如果跳板机的 SSH 禁止了 root 密码登录，需要先开启：`
*`一般已经开启直接跳过这步即可
```bash
# 允许 root 密码登录（如果之前是 prohibit-password）
sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# 允许反向隧道绑定到所有接口（Docker 场景必须）
sed -i 's/#GatewayPorts no/GatewayPorts yes/' /etc/ssh/sshd_config

# 重启 SSH 服务使配置生效
systemctl restart sshd
```

> 注意：重启 sshd 会断开当前 SSH 隧道连接，需要在本地重新建立。

### 第二步：建立 SSH 反向隧道（本地电脑执行）

```bash
ssh -R 9132:localhost:9132 -N -o ServerAliveInterval=30 -o ServerAliveCountMax=3 root@跳板机IP
```

参数说明：

| 参数 | 含义 |
|------|------|
| `-R 9132:localhost:9132` | 将跳板机的 9132 端口转发到本地的 9132 端口 |
| `-N` | 不执行远程命令，仅建立隧道（终端会停住不动，这是正常的） |
| `-o ServerAliveInterval=30` | 每 30 秒发送心跳，防止连接超时断开 |
| `-o ServerAliveCountMax=3` | 连续 3 次心跳无响应则断开 |

如果需要后台运行，加 `-f`：

```bash
ssh -R 9132:localhost:9132 -f -N -o ServerAliveInterval=30 -o ServerAliveCountMax=3 root@跳板机IP
```

### 第三步：配置 Nginx 反向代理（跳板机上执行）

在 Nginx 的 server 块中添加 location：

**如果 Nginx 直接安装在宿主机上：**

```nginx
location /test/webhook {
    proxy_pass http://127.0.0.1:9132/meego/meego/plugin/listener/on-listen;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_read_timeout 60s;
    proxy_send_timeout 60s;
}
```

**如果 Nginx 运行在 Docker 容器中：**

容器内的 `127.0.0.1` 指向容器自身，无法访问宿主机端口。需要使用 Docker 网桥 IP（通常是 `172.17.0.1`）：

```nginx
location /test/webhook {
    proxy_pass http://172.17.0.1:9132/meego/meego/plugin/listener/on-listen;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_read_timeout 60s;
    proxy_send_timeout 60s;
}
```

确认 Docker 网桥 IP 的方法：

```bash
docker exec <nginx容器名> ip route | grep default
```

输出中 `default via` 后面的 IP 就是宿主机在容器视角的地址。

配置完成后重载 Nginx：

```bash
docker exec <nginx容器名> nginx -s reload
```

### 第四步：验证

**1. 确认隧道已建立（跳板机上执行）：**

```bash
ss -tlnp | grep 9132
```

如果看到 `127.0.0.1:9132` 或 `0.0.0.0:9132` 的 LISTEN 记录，说明隧道正常。

**2. 测试完整链路（跳板机上执行）：**

```bash
curl -X POST https://你的域名/test/webhook \
  -H "Content-Type: application/json" \
  -d '{"test": true}'
```

如果本地服务能收到请求，说明链路通了。

**3. 配置飞书回调地址：**

在飞书开发者后台的事件订阅中，将回调 URL 设为：

```
https://你的域名/test/webhook
```

### 取消隧道

SSH 隧道是临时的，断开即失效。

- **前台运行**（没加 `-f`）：在本地终端按 `Ctrl+C`
- **后台运行**（加了 `-f`）：找到进程并终止

```powershell
# Windows PowerShell
Get-Process ssh | Where-Object { $_.CommandLine -like "*9132*" } | Stop-Process
```

隧道取消后，Nginx 配置可以保留，下次重新建隧道即可恢复。

### 持久化 SSH 配置（可选）

在本地 `~/.ssh/config` 中添加：

```
Host wtp-tunnel
    HostName 跳板机IP
    User root
    RemoteForward 9132 localhost:9132
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

以后只需执行 `ssh -fN wtp-tunnel` 即可一键建立隧道。

### 常见问题

**Q: SSH 连接时输入密码后没有反应？**
这是正常的。`-N` 参数表示不打开远程终端，连接成功后终端会停住不动、没有任何输出。

**Q: Nginx 返回 502 Bad Gateway？**
检查本地 9132 端口是否有服务在运行。SSH 隧道只转发流量，如果本地没有服务监听，就会返回 502。

**Q: Nginx 返回 Connection refused？**
SSH 隧道可能已断开。检查本地跑隧道的终端是否还在运行，必要时重新执行 SSH 命令。

**Q: Docker 内 Nginx 返回 Connection refused，但宿主机上 ss 显示端口在监听？**
这是 Docker 网络隔离导致的。确保 Nginx 配置中使用的是 Docker 网桥 IP（如 `172.17.0.1`）而非 `127.0.0.1`，并且在 sshd_config 中开启了 `GatewayPorts yes`。
