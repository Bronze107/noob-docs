# apt 常用命令与镜像源设置

这是一份给新手的 `apt` 速查文档，适合 Ubuntu / Debian 系 Linux，也适合在 WSL2 里使用。

目标很简单：

- 知道 `apt` 最常用的命令
- 知道什么时候该执行哪些命令
- 会把软件源切换到更快的镜像

## 1. apt 是什么

`apt` 是 Debian 和 Ubuntu 常用的软件包管理工具。

你可以用它来：

- 安装软件
- 更新软件列表
- 升级系统中的软件
- 删除不需要的软件
- 搜索软件包
- 清理缓存

## 2. 最常用的 apt 命令

### 更新软件包列表

```bash
sudo apt update
```

作用：

- 从软件源拉取最新的软件包信息
- 不会直接升级软件

什么时候用：

- 新装系统后
- 切换镜像源后
- 安装软件前

### 升级已安装的软件

```bash
sudo apt upgrade
```

如果你不想每次手动确认：

```bash
sudo apt upgrade -y
```

### 完整升级

```bash
sudo apt full-upgrade
```

说明：

- `full-upgrade` 比 `upgrade` 更激进
- 它会在需要时安装或删除依赖，以完成升级

新手平时优先用：

```bash
sudo apt update
sudo apt upgrade -y
```

## 3. 安装软件

### 安装单个软件

```bash
sudo apt install git
```

### 一次安装多个软件

```bash
sudo apt install -y git curl wget vim
```

### 安装 Python 常用环境

```bash
sudo apt install -y python3 python3-pip python3-venv
```

### 安装开发常用工具

```bash
sudo apt install -y build-essential git curl wget unzip
```

## 4. 搜索和查看软件信息

### 搜索软件包

```bash
apt search nginx
```

### 查看软件包详情

```bash
apt show nginx
```

### 查看某个命令来自哪个包

如果系统里安装了 `which`：

```bash
which git
```

更进一步可以用：

```bash
dpkg -S /usr/bin/git
```

## 5. 删除软件

### 删除软件，但保留配置

```bash
sudo apt remove nginx
```

### 删除软件和配置

```bash
sudo apt purge nginx
```

### 删除不再需要的依赖

```bash
sudo apt autoremove
```

### 自动确认

```bash
sudo apt autoremove -y
```

## 6. 清理缓存

### 清理已下载的软件包缓存

```bash
sudo apt clean
```

### 清理无用缓存

```bash
sudo apt autoclean
```

一般来说：

- `clean` 更彻底
- `autoclean` 更温和

## 7. 一套最常用的维护命令

平时最常见的一套组合：

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
sudo apt clean
```

## 8. apt 和 apt-get 的区别

新手直接记住这一句就够了：

- 日常手动使用，优先 `apt`
- 老教程、脚本里，经常会看到 `apt-get`

例如下面两条在很多场景下都能工作：

```bash
sudo apt install git
sudo apt-get install git
```

对新手来说，平时直接用 `apt` 就行。

## 9. 查看当前软件源

传统的软件源配置文件常见位置：

```bash
/etc/apt/sources.list
```

新版 Ubuntu 还可能使用：

```bash
/etc/apt/sources.list.d/
```

查看当前主配置：

```bash
cat /etc/apt/sources.list
```

如果系统提示文件不存在，也不用慌，说明你的发行版可能主要使用 `.sources` 文件。

可以继续看：

```bash
ls /etc/apt/sources.list.d/
```

## 10. 为什么要设置镜像源

默认官方源不一定慢，但在国内很多时候速度一般。

切换到国内镜像源后，通常会更适合：

- 更新软件包列表
- 安装开发工具
- 下载依赖

常见镜像站：

- 阿里云
- 清华大学 TUNA
- 中科大 USTC
- 华为云

## 11. 设置镜像源前先备份

改镜像前，建议先备份原文件。

### 如果你的系统使用 `sources.list`

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

### 如果你的系统使用 `.sources`

先看看有哪些文件：

```bash
ls /etc/apt/sources.list.d/
```

例如常见的 Ubuntu 文件可能是：

```bash
/etc/apt/sources.list.d/ubuntu.sources
```

备份示例：

```bash
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bak
```

## 12. Ubuntu 老式 sources.list 镜像示例

下面以 Ubuntu 为例，演示老式 `sources.list` 写法。

先查看你的 Ubuntu 版本代号：

```bash
lsb_release -cs
```

常见输出可能是：

- `focal`
- `jammy`
- `noble`

### 清华镜像示例

把 `jammy` 替换成你的系统代号：

```text
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
```

### 阿里云镜像示例

```text
deb https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
```

## 13. Ubuntu 新版 .sources 镜像设置思路

较新的 Ubuntu 可能使用 `.sources` 文件，比如：

```bash
/etc/apt/sources.list.d/ubuntu.sources
```

可以先查看内容：

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

你通常会看到官方地址，例如：

- `http://archive.ubuntu.com/ubuntu`
- `http://security.ubuntu.com/ubuntu`

这时最简单的思路不是自己从头重写，而是：

1. 先备份原文件
2. 把里面的官方地址替换成镜像站地址
3. 再执行 `sudo apt update`

例如你可能会替换成：

- `https://mirrors.tuna.tsinghua.edu.cn/ubuntu`
- `https://mirrors.aliyun.com/ubuntu`

## 14. 使用 sed 快速替换镜像

如果你确认系统使用的是 `ubuntu.sources`，可以这样替换为清华源：

```bash
sudo sed -i 's|http://archive.ubuntu.com/ubuntu|https://mirrors.tuna.tsinghua.edu.cn/ubuntu|g' /etc/apt/sources.list.d/ubuntu.sources
sudo sed -i 's|http://security.ubuntu.com/ubuntu|https://mirrors.tuna.tsinghua.edu.cn/ubuntu|g' /etc/apt/sources.list.d/ubuntu.sources
```

替换为阿里云也类似：

```bash
sudo sed -i 's|http://archive.ubuntu.com/ubuntu|https://mirrors.aliyun.com/ubuntu|g' /etc/apt/sources.list.d/ubuntu.sources
sudo sed -i 's|http://security.ubuntu.com/ubuntu|https://mirrors.aliyun.com/ubuntu|g' /etc/apt/sources.list.d/ubuntu.sources
```

替换完成后执行：

```bash
sudo apt update
```

## 15. 改完镜像后怎么验证

执行：

```bash
sudo apt update
```

如果没有报错，通常说明镜像配置基本可用。

你也可以再看一下源文件内容：

```bash
cat /etc/apt/sources.list
```

或者：

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

## 16. 出现问题怎么恢复

如果改完后 `apt update` 报错，可以恢复备份。

### 恢复老式 sources.list

```bash
sudo cp /etc/apt/sources.list.bak /etc/apt/sources.list
sudo apt update
```

### 恢复新版 ubuntu.sources

```bash
sudo cp /etc/apt/sources.list.d/ubuntu.sources.bak /etc/apt/sources.list.d/ubuntu.sources
sudo apt update
```

## 17. 新手最常用的几条，先记住就够了

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y git curl wget vim
sudo apt remove package-name
sudo apt autoremove -y
sudo apt clean
apt search keyword
apt show package-name
```

## 18. 常见建议

- 安装软件前先 `sudo apt update`
- 不要看到 `full-upgrade` 就随手执行，先理解它的作用
- 改镜像前一定先备份
- 如果是 Ubuntu 新版，优先检查 `.sources` 文件
- 改完镜像后第一件事就是执行 `sudo apt update`

## 19. 一句话总结

把 `apt` 理解成 Linux 里的“应用商店命令行版本”就行。

先记住 `update`、`upgrade`、`install`、`remove`、`search`、`clean`，再学镜像源设置，日常使用就很顺手了。
