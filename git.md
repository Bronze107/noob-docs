# Git 快速入门：新手常用命令

这是一份给新手的 Git 速查文档，目标是先能看懂、先能提交代码，再慢慢理解版本管理。

## 1. Git 是什么

Git 是一个版本管理工具。

你可以把它理解成：

- 记录代码历史
- 管理多人协作
- 随时查看改了什么
- 在出问题时回到之前的版本

## 2. 先做一次基础配置

第一次使用 Git，建议先设置用户名和邮箱：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

查看当前全局配置：

```bash
git config --global --list
```

查看某个配置项：

```bash
git config --global user.name
git config --global user.email
```

## 3. 创建仓库和获取仓库

### 在当前目录初始化 Git 仓库

```bash
git init
```

执行后，当前目录会变成一个 Git 仓库。

### 克隆远程仓库

```bash
git clone 仓库地址
```

例如：

```bash
git clone https://github.com/user/demo.git
```

克隆后进入目录：

```bash
cd demo
```

## 4. 最常用的查看命令

### 查看当前状态

```bash
git status
```

这是新手最该频繁使用的命令之一。

它可以看出：

- 当前在哪个分支
- 哪些文件被修改了
- 哪些文件已经暂存
- 哪些文件还没纳入 Git 管理

### 查看提交历史

```bash
git log
```

简洁一点：

```bash
git log --oneline
```

### 查看修改内容

```bash
git diff
```

查看已经 `add` 之后的差异：

```bash
git diff --cached
```

## 5. 新手最常用的提交流程

一个最常见的工作流就是：

```bash
git status
git add .
git commit -m "完成某个功能"
```

更推荐新手这样理解：

### 第一步：查看状态

```bash
git status
```

### 第二步：把文件加入暂存区

添加单个文件：

```bash
git add README.md
```

添加当前目录所有修改：

```bash
git add .
```

### 第三步：提交

```bash
git commit -m "新增登录页面"
```

提交信息建议写清楚“做了什么”。

## 6. 文件状态怎么理解

你可以把 Git 里的文件状态先简单理解成三类：

- 已修改，但还没暂存
- 已暂存，准备提交
- 已提交，进入历史记录

最核心的两个命令：

```bash
git add .
git commit -m "说明本次改动"
```

## 7. 分支常用命令

### 查看分支

```bash
git branch
```

### 创建分支

```bash
git branch dev
```

### 切换分支

```bash
git switch dev
```

如果你的 Git 版本较老，也可能看到：

```bash
git checkout dev
```

### 创建并切换到新分支

```bash
git switch -c feature/login
```

老写法：

```bash
git checkout -b feature/login
```

### 删除本地分支

```bash
git branch -d dev
```

强制删除：

```bash
git branch -D dev
```

提醒：

- 删除分支前先确认分支内容是否已经合并

## 8. 远程仓库常用命令

### 查看远程仓库

```bash
git remote -v
```

### 添加远程仓库

```bash
git remote add origin 仓库地址
```

例如：

```bash
git remote add origin https://github.com/user/demo.git
```

### 推送到远程仓库

第一次推送当前分支：

```bash
git push -u origin main
```

以后常用：

```bash
git push
```

### 从远程拉取更新

```bash
git pull
```

更明确一点：

```bash
git pull origin main
```

### 只获取更新，但不自动合并

```bash
git fetch
```

## 9. merge 和 rebase，新手先怎么理解

先记住最实用的一版：

- `git pull` 常常等于先 `fetch` 再合并
- 多人协作时，先拉最新代码再开始改更稳妥

如果你还是新手，先把下面这些用熟：

```bash
git fetch
git pull
git merge
```

`rebase` 可以以后再深入学。

## 10. 撤销和回退的常用命令

这一部分最容易误操作，新手一定慢一点。

### 取消暂存某个文件

```bash
git restore --staged 文件名
```

例如：

```bash
git restore --staged app.py
```

### 丢弃工作区修改

```bash
git restore 文件名
```

提醒：

- 这会丢掉你还没提交的修改

### 回退到某个提交

先查看提交记录：

```bash
git log --oneline
```

临时切到某个提交：

```bash
git checkout 提交ID
```

### 反向生成一次撤销提交

```bash
git revert 提交ID
```

这对新手和协作项目通常更安全。

### 重置到上一次提交

```bash
git reset --soft HEAD~1
```

说明：

- `--soft` 会保留改动，只撤销提交记录

新手先少碰这些：

```bash
git reset --hard
```

因为它可能直接丢失未保存的重要改动。

## 11. 查看某个文件的历史

```bash
git log -- 文件名
```

例如：

```bash
git log -- README.md
```

查看某次提交改了什么：

```bash
git show 提交ID
```

## 12. 常见场景速查

### 场景 1：我改完代码，想提交

```bash
git status
git add .
git commit -m "修复首页样式问题"
```

### 场景 2：我想先拉最新代码

```bash
git pull
```

### 场景 3：我想看自己改了什么

```bash
git diff
```

### 场景 4：我不小心 add 了一个文件

```bash
git restore --staged 文件名
```

### 场景 5：我想新开一个分支开发

```bash
git switch -c feature/demo
```

### 场景 6：我想把本地代码推到远程

```bash
git push -u origin main
```

## 13. .gitignore 是什么

`.gitignore` 用来告诉 Git：

- 哪些文件不要提交
- 哪些临时文件不用纳入版本管理

常见内容例如：

```gitignore
node_modules/
dist/
.env
__pycache__/
*.log
```

## 14. 新手最该记住的 10 条命令

```bash
git init
git clone 仓库地址
git status
git add .
git commit -m "说明改动"
git log --oneline
git diff
git branch
git switch -c 新分支名
git pull
git push
```

## 15. 常见建议

- 写代码前先 `git pull`
- 提交前先 `git status`
- 提交说明尽量写清楚
- 新功能尽量在新分支里做
- 不确定会不会丢代码时，先不要碰 `reset --hard`
- 看不懂当前状态时，先执行 `git status`

## 16. 一句话总结

Git 最核心的日常操作，其实就是：

```bash
git status
git add .
git commit -m "本次改动说明"
git pull
git push
```

先把这几条练熟，再去学分支协作、冲突处理、rebase，就会轻松很多。
