<h1 align="center">
  <img src="https://github.com/Dreamacro/clash/raw/master/docs/logo.png" alt="Clash" width="200">
  <br>Linux Clash 教程<br>
</h1>
<h5 align="center",href="https://github.com/Dreamacro/clash">clash 官方地址：https://github.com/Dreamacro/clash</h5>


## 安装 Clash

1. 下载对应的 `clash` 版本 

    下载地址：[https://github.com/Dreamacro/clash/releases](https://github.com/Dreamacro/clash/releases)

2. 解压 `clash`

3. 将解压出的文件重命名成 `clash`

4. 将 `clash` 移动到 `/usr/bin/` 目录下

5. 赋予 `clash` 可执行权限
    
    ```bash
    sudo chmod +x /usr/bin/clash
    ```

6. 检查 `clash` 是否安装成功

    ```bash
    clash -v
    ```
    
    > 返回以下内容说明安装成功：
    
    ```text
    Clash v0.13.0-0-g744728c linux 386 Sat Mar 30 06:23:03 UTC 2019
    ```
    
## 修改配置


默认配置目录是 `$HOME/.config/clash`

配置文件的名称是 `config.yml`

如果要使用其他目录，可以使用 `-d` 控制配置目录

> 例如，您可以使用当前目录作为配置目录

```bash
clash -d .
```

> 默认配置目录启动

```
clash
```

#### `config.yml` 配置示例

```yaml
# port of HTTP
port: 7890

# port of SOCKS5
socks-port: 7891

# redir port for Linux and macOS
# redir-port: 7892

allow-lan: false

# Rule / Global/ Direct (default is Rule)
mode: Rule

# set log level to stdout (default is info)
# info / warning / error / debug / silent
log-level: info

# A RESTful API for clash
external-controller: 127.0.0.1:9090

# you can put the static web resource (such as clash-dashboard) to a directory, and clash would serve in `${API}/ui`
# input is a relative path to the configuration directory or an absolute path
# external-ui: folder

# Secret for RESTful API (Optional)
# secret: ""

# dns:
  # enable: true # set true to enable dns (default is false)
  # ipv6: false # default is false
  # listen: 0.0.0.0:53
  # enhanced-mode: redir-host
  # nameserver:
  #   - 114.114.114.114
  #   - tls://dns.rubyfish.cn:853 # dns over tls
  # fallback: # concurrent request with nameserver, fallback used when GEOIP country isn't CN
  #   - tcp://1.1.1.1

Proxy:

# shadowsocks
# The types of cipher are consistent with go-shadowsocks2
# support AEAD_AES_128_GCM AEAD_AES_192_GCM AEAD_AES_256_GCM AEAD_CHACHA20_POLY1305 AES-128-CTR AES-192-CTR AES-256-CTR AES-128-CFB AES-192-CFB AES-256-CFB CHACHA20-IETF XCHACHA20
# In addition to what go-shadowsocks2 supports, it also supports chacha20 rc4-md5 xchacha20-ietf-poly1305
- { name: "ss1", type: ss, server: server, port: 443, cipher: AEAD_CHACHA20_POLY1305, password: "password" }

# old obfs configuration remove after prerelease
- name: "ss2"
  type: ss
  server: server
  port: 443
  cipher: AEAD_CHACHA20_POLY1305
  password: "password"
  plugin: obfs
  plugin-opts:
    mode: tls # or http
    # host: bing.com

- name: "ss3"
  type: ss
  server: server
  port: 443
  cipher: AEAD_CHACHA20_POLY1305
  password: "password"
  plugin: v2ray-plugin
  plugin-opts:
    mode: websocket # no QUIC now
    # tls: true # wss
    # skip-cert-verify: true
    # host: bing.com
    # path: "/"
    # headers:
    #   custom: value

# vmess
# cipher support auto/aes-128-gcm/chacha20-poly1305/none
- { name: "vmess", type: vmess, server: server, port: 443, uuid: uuid, alterId: 32, cipher: auto }
# with tls
- { name: "vmess", type: vmess, server: server, port: 443, uuid: uuid, alterId: 32, cipher: auto, tls: true }
# with tls and skip-cert-verify
- { name: "vmess", type: vmess, server: server, port: 443, uuid: uuid, alterId: 32, cipher: auto, tls: true, skip-cert-verify: true }
# with ws-path and ws-headers
- { name: "vmess", type: vmess, server: server, port: 443, uuid: uuid, alterId: 32, cipher: auto, network: ws, ws-path: /path, ws-headers: { Host: v2ray.com } }
# with ws + tls
- { name: "vmess", type: vmess, server: server, port: 443, uuid: uuid, alterId: 32, cipher: auto, network: ws, ws-path: /path, tls: true }

# socks5
- { name: "socks", type: socks5, server: server, port: 443 }
# socks5 with authentication
- { name: "socks", type: socks5, server: server, port: 443, username: "username", password: "password" }
# with tls
- { name: "socks", type: socks5, server: server, port: 443, tls: true }
# with tls and skip-cert-verify
- { name: "socks", type: socks5, server: server, port: 443, tls: true, skip-cert-verify: true }

# http
- { name: "http", type: http, server: server, port: 443 }
# http with authentication
- { name: "http", type: http, server: server, port: 443, username: "username", password: "password" }
# with tls (https)
- { name: "http", type: http, server: server, port: 443, tls: true }
# with tls (https) and skip-cert-verify
- { name: "http", type: http, server: server, port: 443, tls: true, skip-cert-verify: true }

Proxy Group:
# url-test select which proxy will be used by benchmarking speed to a URL.
- { name: "auto", type: url-test, proxies: ["ss1", "ss2", "vmess1"], url: "http://www.gstatic.com/generate_204", interval: 300 }

# fallback select an available policy by priority. The availability is tested by accessing an URL, just like an auto url-test group.
- { name: "fallback-auto", type: fallback, proxies: ["ss1", "ss2", "vmess1"], url: "http://www.gstatic.com/generate_204", interval: 300 }

# load-balance: The request of the same eTLD will be dial on the same proxy.
- { name: "load-balance", type: load-balance, proxies: ["ss1", "ss2", "vmess1"], url: "http://www.gstatic.com/generate_204", interval: 300 }

# select is used for selecting proxy or proxy group
# you can use RESTful API to switch proxy, is recommended for use in GUI.
- { name: "Proxy", type: select, proxies: ["ss1", "ss2", "vmess1", "auto"] }

Rule:
- DOMAIN-SUFFIX,google.com,auto
- DOMAIN-KEYWORD,google,auto
- DOMAIN,google.com,auto
- DOMAIN-SUFFIX,ad.com,REJECT
- IP-CIDR,127.0.0.0/8,DIRECT
- SOURCE-IP-CIDR,192.168.1.201/32,DIRECT
- GEOIP,CN,DIRECT
# FINAL would remove after prerelease
# you also can use `FINAL,Proxy` or `FINAL,,Proxy` now
- MATCH,auto
```

## 编写 systemd 服务脚本

在 `/lib/systemd/system/` 目录下创建 `clash@.service` 文件

```bash
sudo vim /lib/systemd/system/clash@.service
```

写入以下内容(不用修改)并保存:

```text
[Unit]
Description=A rule based proxy in Go for %i.
After=network.target

[Service]
Type=simple
User=%i
Restart=on-abort
ExecStart=/usr/bin/clash

[Install]
WantedBy=multi-user.target
```


## 为用户帐户运行 clash 系统实例

#### 重新加载 `systemd` 模块

```bash
systemctl daemon-reload
```

#### 启动 `clash` 服务

> `user` 表示的是当前用户名

```bash
systemctl start clash@user
```

例如：

```bash
systemctl start clash@sitoi
```

#### 设置开机自启

```bash
systemctl enable clash@user
```


### 问题

权限不足

```bash
$ sudo systemctl status clash

● clash.service - A rule based proxy in Go for shitao.
   Loaded: loaded (/usr/lib/systemd/system/clash.service; disabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Tue 2019-06-18 17:27:18 CST; 4s ago
  Process: 6777 ExecStart=/usr/bin/clash (code=exited, status=203/EXEC)
 Main PID: 6777 (code=exited, status=203/EXEC)

Jun 18 17:27:18 localhost.localdomain systemd[1]: Started A rule based proxy in Go for shitao..
Jun 18 17:27:18 localhost.localdomain systemd[6777]: clash.service: Failed to execute command: Permission denied
Jun 18 17:27:18 localhost.localdomain systemd[6777]: clash.service: Failed at step EXEC spawning /usr/bin/clash: Permission denied
Jun 18 17:27:18 localhost.localdomain systemd[1]: clash.service: Main process exited, code=exited, status=203/EXEC
Jun 18 17:27:18 localhost.localdomain systemd[1]: clash.service: Failed with result 'exit-code'.
```

修改 `selinux` 成被动模式

```bash
sudo vim /etc/sysconfig/selinux
```

```text
SELINUX=permissive
```
