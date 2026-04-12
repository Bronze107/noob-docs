# WSL2 快速入门：新手常用命令

这是一份给新手的 WSL2 速查文档，目标是先会用，再慢慢理解原理。

## 1. WSL2 是什么

WSL2 是 Windows Subsystem for Linux 2，可以让你在 Windows 里直接运行 Linux 环境。

常见用途：

- 学 Linux 命令
- 跑开发环境，比如 Python、Node.js、Go、Java
- 用 `apt` 安装工具
- 在 Windows 和 Linux 之间配合开发

## 2. 安装和初始化

### 安装 WSL

在 **PowerShell（管理员）** 里执行：

```powershell
wsl --install
```

作用：

- 启用 WSL
- 安装 WSL2
- 默认安装一个 Linux 发行版

安装完成后，一般需要 **重启电脑**。

### 查看可安装的发行版

```powershell
wsl --list --online
```

也可以简写为：

```powershell
wsl -l -o
```

### 安装指定发行版

例如安装 Ubuntu：

```powershell
wsl --install -d Ubuntu
```

### 查看已安装的发行版

```powershell
wsl --list --verbose
```

简写：

```powershell
wsl -l -v
```

你会看到每个发行版的：

- 名字
- 运行状态
- WSL 版本

## 3. 进入和退出 WSL

### 进入默认发行版

```powershell
wsl
```

### 进入指定发行版

```powershell
wsl -d Ubuntu
```

### 以指定用户进入

```powershell
wsl -d Ubuntu -u root
```

### 退出 Linux

```bash
exit
```

## 4. 常用状态查看命令

### 查看 WSL 版本信息

```powershell
wsl --version
```

### 查看发行版状态

```powershell
wsl -l -v
```

### 查看当前 Linux 路径

```bash
pwd
```

### 查看当前目录文件

```bash
ls
```

常见写法：

```bash
ls -l
ls -la
```

说明：

- `-l` 表示列表方式显示
- `-a` 表示显示隐藏文件

## 5. Linux 里最常用的新手命令

### 切换目录

```bash
cd /home
cd ~
cd ..
```

说明：

- `~` 表示当前用户家目录
- `..` 表示上一级目录

### 创建目录

```bash
mkdir demo
mkdir -p a/b/c
```

### 创建空文件

```bash
touch test.txt
```

### 查看文件内容

```bash
cat test.txt
less test.txt
```

### 复制、移动、删除

```bash
cp a.txt b.txt
mv a.txt demo/
rm a.txt
rm -r demo
```

提醒：

- `rm` 删除后默认不会进回收站
- 新手执行 `rm -r` 前一定先确认路径

### 查找当前位置

```bash
pwd
```

### 清屏

```bash
clear
```

## 6. 包管理：安装软件

Ubuntu 或 Debian 系发行版常用：

### 更新软件包列表

```bash
sudo apt update
```

### 升级已安装的软件

```bash
sudo apt upgrade -y
```

### 安装常见工具

```bash
sudo apt install -y git curl wget vim
```

### 安装 Python

```bash
sudo apt install -y python3 python3-pip
```

### 安装 Node.js 和 npm

```bash
sudo apt install -y nodejs npm
```

### 删除软件

```bash
sudo apt remove package-name
```

## 7. Windows 和 WSL 文件互通

### 在 WSL 中访问 Windows 盘符

Windows 的 `C:` 盘，在 WSL 里通常是：

```bash
/mnt/c
```

例如进入桌面：

```bash
cd /mnt/c/Users/你的用户名/Desktop
```

### 在 WSL 中打开当前目录到资源管理器

```bash
explorer.exe .
```

### 在 Windows 中访问 Linux 文件

可以在资源管理器地址栏输入：

```text
\\wsl$
```

然后进入你的发行版目录。

## 8. 运行 Windows 命令

在 WSL 里可以直接调用部分 Windows 程序：

```bash
notepad.exe test.txt
code .
explorer.exe .
```

如果你安装了 VS Code，最常用的是：

```bash
code .
```

## 9. 关机、重启和默认设置

### 关闭所有 WSL 实例

```powershell
wsl --shutdown
```

这个命令很常用，遇到 WSL 卡住、网络异常、资源没释放时可以先试它。

### 终止指定发行版

```powershell
wsl --terminate Ubuntu
```

### 设置默认发行版

```powershell
wsl --set-default Ubuntu
```

### 设置某个发行版使用 WSL2

```powershell
wsl --set-version Ubuntu 2
```

## 10. 导入、导出和注销发行版

这部分适合以后做备份时用。

### 导出发行版

```powershell
wsl --export Ubuntu D:\backup\ubuntu.tar
```

### 导入发行版

```powershell
wsl --import Ubuntu2 D:\WSL\Ubuntu2 D:\backup\ubuntu.tar
```

### 注销发行版

```powershell
wsl --unregister Ubuntu
```

提醒：

- `--unregister` 会删除这个发行版的数据
- 执行前一定确认你不再需要里面的文件

## 11. 新手最常见的排错命令

### WSL 无法正常启动

先试：

```powershell
wsl --shutdown
wsl
```

### 看看有哪些发行版

```powershell
wsl -l -v
```

### 更新 WSL

```powershell
wsl --update
```

### 查看内核状态

```powershell
wsl --status
```

## 12. 建议新手先记住这 10 条

如果你刚开始，只要先熟悉下面这些就够用了：

```powershell
wsl
wsl -l -v
wsl --shutdown
wsl --install -d Ubuntu
```

```bash
pwd
ls -la
cd ~
mkdir demo
sudo apt update
sudo apt install -y git curl wget
```

## 13. 常见建议

- 开发代码尽量放在 Linux 家目录里，比如 `~/project`
- 不要一上来就乱用 `rm -rf`
- 装软件优先用发行版自己的包管理器
- 遇到奇怪问题先试 `wsl --shutdown`
- 想图形化地看当前目录就用 `explorer.exe .`

## 14. 一句话总结

把 WSL2 当成一台装在 Windows 里的 Linux 小电脑来用就行。

先记住 `wsl`、`wsl -l -v`、`wsl --shutdown`、`cd`、`ls`、`apt`，基本就能开始上手了。
