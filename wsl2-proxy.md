# WSL2 NAT 模式设置代理：Win10 实用方案

这是一份给新手的 WSL2 代理配置文档，重点解决这个常见问题：

- Windows 上代理能用
- WSL2 里访问外网不通
- 直接设置 `http_proxy=http://172.30.x.x:7890` 还是超时

这份文档主要适用于：

- Windows 10
- WSL2 默认 NAT 网络模式
- 本机已安装代理软件，比如 Clash、Clash Verge、Mihomo、V2RayN 等

## 1. 先说结论

在 Win10 的 WSL2 NAT 模式下，经常会出现这种情况：

- Windows 本机可以访问 `127.0.0.1:7890`
- WSL 却无法访问 `172.30.x.x:7890`
- 即使代理软件开启了“允许局域网连接”也未必稳定

这种情况下，一个很实用的办法是：

1. 保持 Windows 代理继续监听本机 `127.0.0.1:7890`
2. 用 Windows 的 `portproxy` 单独开一个给 WSL 用的转发端口
3. 在 WSL 里把代理指向这个新端口

推荐形态：

```text
WSL -> 172.30.x.x:7891 -> Windows portproxy -> 127.0.0.1:7890
```

## 2. 为什么直接连 7890 有时不行

你可能已经试过这些：

```bash
export http_proxy=http://172.30.x.x:7890
curl -I https://www.google.com
```

或者：

```bash
curl -I -v --proxy http://172.30.x.x:7890 --max-time 10 https://www.google.com
```

结果一直超时。

这通常不是 `curl` 的问题，而是：

- Windows 本机代理虽然可用
- 但 WSL 到 Windows 代理端口这条链路不通

## 3. 先确认 Windows 本机代理是正常的

在 Windows PowerShell 中测试：

```powershell
curl.exe -I -x http://127.0.0.1:7890 https://www.google.com --max-time 10
```

如果返回 `HTTP/1.1 200 Connection established` 或 `200 OK`，说明：

- 代理软件本身正常
- 7890 在 Windows 本机可用

## 4. 查看 WSL 主机地址

在 Windows 中，WSL 虚拟网卡常见地址类似：

```text
172.30.x.x
```

查看方法：

```powershell
ipconfig
```

一般关注：

```text
vEthernet (WSL)
```

例如：

```text
IPv4 地址: 172.30.x.x
```

## 5. 推荐方案：给 WSL 单独做端口转发

### 第一步：添加 portproxy 规则

在 **管理员 PowerShell** 中执行：

```powershell
netsh interface portproxy add v4tov4 listenaddress=172.30.x.x listenport=7891 connectaddress=127.0.0.1 connectport=7890
```

含义：

- 监听 `172.30.x.x:7891`
- 转发到 Windows 本机代理 `127.0.0.1:7890`

### 第二步：放行 7891 端口

```powershell
netsh advfirewall firewall add rule name="WSL PortProxy 7891" dir=in action=allow protocol=TCP localport=7891
```

### 第三步：确认规则已存在

```powershell
netsh interface portproxy show all
```

你应该能看到类似：

```text
172.30.x.x   7891   127.0.0.1   7890
```

### 第四步：重启 WSL

```powershell
wsl --shutdown
```

## 6. 在 WSL 中测试代理

进入 WSL 后执行：

```bash
curl -I -v --proxy http://172.30.x.x:7891 --max-time 10 https://www.google.com
```

如果通了，就说明链路已经建立成功。

## 7. 在 WSL 里设置环境变量

先临时设置：

```bash
export http_proxy=http://172.30.x.x:7891
export https_proxy=http://172.30.x.x:7891
unset all_proxy
```

然后测试：

```bash
curl -I https://www.google.com
wget https://www.google.com
```

如果代理软件的这个端口确认支持 SOCKS，也可以设置：

```bash
export all_proxy=socks5://172.30.x.x:7891
```

但如果你不确定端口类型，先只配 `http_proxy` 和 `https_proxy` 更稳。

## 8. 长期生效的写法

把下面内容加入 `~/.bashrc` 或 `~/.zshrc`：

```bash
export http_proxy=http://172.30.x.x:7891
export https_proxy=http://172.30.x.x:7891
unset all_proxy
```

然后执行：

```bash
source ~/.bashrc
```

## 9. 用 alias 一键开关代理

如果你不想每次手动输入 `export`，可以把下面这些别名加进 `~/.bashrc` 或 `~/.zshrc`：

```bash
alias proxy_on='export http_proxy=http://172.30.x.x:7891; export https_proxy=http://172.30.x.x:7891; unset all_proxy'
alias proxy_off='unset http_proxy; unset https_proxy; unset all_proxy'
alias proxy_status='echo "http_proxy=$http_proxy"; echo "https_proxy=$https_proxy"; echo "all_proxy=$all_proxy"'
```

重新加载配置：

```bash
source ~/.bashrc
```

如果你用的是 zsh：

```bash
source ~/.zshrc
```

### 开启代理

```bash
proxy_on
```

### 关闭代理

```bash
proxy_off
```

### 查看当前状态

```bash
proxy_status
```

## 10. 用函数版开关代理

如果你希望后面更容易扩展，也可以不用 `alias`，改成 shell 函数：

```bash
proxy_on() {
  export http_proxy=http://172.30.x.x:7891
  export https_proxy=http://172.30.x.x:7891
  unset all_proxy
  echo "proxy on"
}

proxy_off() {
  unset http_proxy
  unset https_proxy
  unset all_proxy
  echo "proxy off"
}

proxy_status() {
  echo "http_proxy=$http_proxy"
  echo "https_proxy=$https_proxy"
  echo "all_proxy=$all_proxy"
}
```

对新手来说：

- 想最省事，就用 `alias`
- 想以后更容易加逻辑，就用函数

## 11. apt 和 git 的代理设置

### apt

```bash
sudo tee /etc/apt/apt.conf.d/95proxies >/dev/null <<EOF
Acquire::http::Proxy "http://172.30.x.x:7891";
Acquire::https::Proxy "http://172.30.x.x:7891";
EOF
```

### git

```bash
git config --global http.proxy "http://172.30.x.x:7891"
git config --global https.proxy "http://172.30.x.x:7891"
```

## 12. 这种方案有没有安全风险

有风险，但如果配置得当，风险是可控的。

### 风险来自哪里

你原本的代理只监听：

```text
127.0.0.1:7890
```

这意味着只有 Windows 本机自己能访问。

而加了 `portproxy` 之后，实际上又新增了一个入口：

```text
172.30.x.x:7891
```

这就意味着这个端口不再只是纯本机回环端口。

### 为什么当前方案相对可控

推荐规则里监听的是：

```text
172.30.x.x
```

而不是：

```text
0.0.0.0
```

这两者差别很大：

- `172.30.x.x` 只绑定在 WSL 虚拟网卡上
- `0.0.0.0` 会绑定到所有网卡，风险明显更大

所以更安全的原则是：

- 只监听 `172.30.x.x`
- 不要监听 `0.0.0.0`
- 不要把同样的做法用于数据库、SSH 代理、管理后台等敏感服务

## 13. 更安全一点的做法

如果你担心防火墙规则太宽，可以改成更收敛的规则。

### 查看现有规则

```powershell
netsh interface portproxy show all
```

### 删除旧的端口转发规则

```powershell
netsh interface portproxy delete v4tov4 listenaddress=172.30.x.x listenport=7891
```

### 删除旧防火墙规则

```powershell
netsh advfirewall firewall delete rule name="WSL PortProxy 7891"
```

### 用 PowerShell 创建更收敛的防火墙规则

在 **管理员 PowerShell** 中执行：

```powershell
New-NetFirewallRule -DisplayName "WSL PortProxy 7891 Restricted" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 7891 -LocalAddress 172.30.x.x
```

然后重新添加端口转发：

```powershell
netsh interface portproxy add v4tov4 listenaddress=172.30.x.x listenport=7891 connectaddress=127.0.0.1 connectport=7890
```

## 14. 不用时如何关闭

如果你只是临时使用，可以在不用时删掉规则。

### 删除端口转发

```powershell
netsh interface portproxy delete v4tov4 listenaddress=172.30.x.x listenport=7891
```

### 删除防火墙规则

如果你用的是旧规则名：

```powershell
netsh advfirewall firewall delete rule name="WSL PortProxy 7891"
```

如果你用的是更收敛的新规则名：

```powershell
Remove-NetFirewallRule -DisplayName "WSL PortProxy 7891 Restricted"
```

## 15. 常见排错命令

### 查看 portproxy 规则

```powershell
netsh interface portproxy show all
```

### 查看代理端口监听

```powershell
netstat -ano | findstr :7890
```

### 查看 WSL 转发端口监听

```powershell
netstat -ano | findstr :7891
```

### 查看 `IP Helper` 服务状态

`portproxy` 依赖这个服务：

```powershell
Get-Service iphlpsvc
```

如果没有运行，可以启动：

```powershell
Start-Service iphlpsvc
```

### 在 WSL 中测试

```bash
curl -I -v --proxy http://172.30.x.x:7891 --max-time 10 https://www.google.com
```

## 16. 一句话总结

在 Win10 的 WSL2 NAT 模式下，如果 WSL 不能直接访问 Windows 本机代理端口，那么最实用的方案就是：

- 保留 Windows 代理继续监听 `127.0.0.1:7890`
- 用 `portproxy` 单独转发一个 `172.30.x.x:7891`
- WSL 只使用这个新端口

这样既能解决可用性问题，也比直接开放到所有网卡更安全。
