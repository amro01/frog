# 通过命令行上传到GitHub

以下是使用命令行将游戏上传到您指定的GitHub仓库的详细步骤。

## 准备工作

确保您已经：
1. 安装了Git（如果没有，请从 https://git-scm.com/ 下载安装）
2. 拥有GitHub账户并已创建仓库：https://github.com/amro01/frog.git

## 命令行步骤

### 1. 打开终端/命令提示符

- Windows: 按 `Win + R`，输入 `cmd` 或 `powershell`
- macOS: 在"应用程序" > "实用工具"中找到"终端"
- Linux: 通常按 `Ctrl + Alt + T`

### 2. 导航到项目目录

```bash
cd /home/deep/deep/learn/frog青蛙过河
```

### 3. 初始化Git仓库

```bash
git init
```

### 4. 添加远程仓库

```bash
git remote add origin https://github.com/amro01/frog.git
```

### 5. 配置Git用户信息（如果尚未配置）

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```
（请替换为您的实际姓名和邮箱）

### 6. 添加所有文件到暂存区

```bash
git add .
```

### 7. 提交文件

```bash
git commit -m "Initial commit: Add frog river crossing game"
```

### 8. 推送到GitHub

```bash
git push -u origin main
```

如果遇到错误，可能需要使用 `master` 分支名：

```bash
git push -u origin master
```

## 可能遇到的问题及解决方案

### 问题1: 认证失败
如果提示需要认证：
- 对于GitHub，推荐使用Personal Access Token (PAT)而不是密码
- 在GitHub设置 > Developer settings > Personal access tokens中生成新token
- 使用token作为密码

### 问题2: 远程仓库已存在
如果提示"remote origin already exists"：
```bash
git remote remove origin
git remote add origin https://github.com/amro01/frog.git
```

### 问题3: 分支名不匹配
如果GitHub使用 `main` 分支但本地使用 `master`：
```bash
git branch -M main
```

### 问题4: 推送被拒绝
如果提示推送被拒绝，可能需要先拉取：
```bash
git pull origin main --allow-unrelated-histories
git push origin main
```

## 完整命令序列（复制粘贴版）

```bash
# 导航到项目目录
cd /home/deep/deep/learn/frog青蛙过河

# 初始化Git
git init

# 添加远程仓库
git remote add origin https://github.com/amro01/frog.git

# 配置用户信息（如果需要）
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 添加文件
git add .

# 提交
git commit -m "Initial commit: Add frog river crossing game"

# 推送（如果使用main分支）
git branch -M main
git push -u origin main
```

## 验证上传成功

1. 访问 https://github.com/amro01/frog
2. 确认所有文件都已显示在仓库中

## 启用GitHub Pages

上传成功后：

1. 在GitHub仓库页面点击"Settings"
2. 在左侧菜单中找到"Pages"
3. 在"Build and deployment"部分：
   - Source: 选择"Deploy from a branch"
   - Branch: 选择"main"
   - Folder: 选择"/ (root)"
4. 点击"Save"

几分钟后，您的游戏将在以下地址可用：
https://amro01.github.io/frog/

## 后续更新

如果以后需要更新游戏：

```bash
cd /home/deep/deep/learn/frog青蛙过河
git add .
git commit -m "Update game with new features"
git push origin main
```

祝您上传顺利！🐸