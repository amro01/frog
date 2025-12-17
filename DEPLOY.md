# GitHub 发布指南

本文件说明如何将"方块青蛙过河"游戏发布到GitHub Pages上。

## 准备工作

确保您已经准备好以下文件：
- `index.html` - 游戏主页面
- `README.md` - 项目说明
- `LICENSE` - 开源许可证
- `.gitignore` - Git忽略文件配置

## 发布步骤

### 1. 创建GitHub仓库

1. 登录您的GitHub账户
2. 点击右上角的"+"号，选择"New repository"
3. 填写仓库信息：
   - Repository name: `frog-river-crossing`（或其他您喜欢的名称）
   - Description: `方块青蛙过河 - 一个简单的HTML5摸鱼游戏`
   - 选择"Public"（公开仓库）
   - **不要**勾选"Add a README file"（我们已经有了）
   - **不要**勾选"Add .gitignore"（我们已经有了）
   - **不要**勾选"Choose a license"（我们已经有了）
4. 点击"Create repository"

### 2. 上传文件到仓库

您可以使用以下两种方法之一：

#### 方法A：使用GitHub网页界面上传

1. 在新创建的仓库页面，点击"uploading an existing file"链接
2. 将以下文件拖拽到页面或点击"choose your files"：
   - `index.html`
   - `README.md`
   - `LICENSE`
   - `.gitignore`
3. 在底部填写提交信息：
   - "Add game files for initial commit"
4. 点击"Commit changes"

#### 方法B：使用Git命令行（推荐）

1. 在本地打开终端/命令提示符
2. 导航到项目目录：
   ```bash
   cd /path/to/your/project
   ```
3. 初始化Git仓库：
   ```bash
   git init
   ```
4. 添加远程仓库（替换YOUR_USERNAME为您的GitHub用户名）：
   ```bash
   git remote add origin https://github.com/YOUR_USERNAME/frog-river-crossing.git
   ```
5. 添加所有文件：
   ```bash
   git add .
   ```
6. 提交更改：
   ```bash
   git commit -m "Initial commit: Add frog river crossing game"
   ```
7. 推送到GitHub：
   ```bash
   git push -u origin main
   ```

### 3. 启用GitHub Pages

1. 在您的GitHub仓库页面，点击"Settings"选项卡
2. 在左侧菜单中找到"Pages"选项
3. 在"Build and deployment"部分：
   - Source: 选择"Deploy from a branch"
   - Branch: 选择"main"
   - Folder: 选择"/ (root)"
4. 点击"Save"按钮

### 4. 访问您的游戏

1. 等待几分钟让GitHub Pages部署完成
2. 您的网站将在以下地址可用：
   ```
   https://YOUR_USERNAME.github.io/frog-river-crossing/
   ```
   （替换YOUR_USERNAME为您的GitHub用户名）

### 5. 更新游戏（可选）

如果您以后想要更新游戏：

1. 修改本地文件
2. 使用Git提交更改：
   ```bash
   git add .
   git commit -m "Update game with new features"
   git push
   ```
3. GitHub Pages会自动更新（可能需要几分钟）

## 文件结构说明

```
frog-river-crossing/
├── index.html          # 游戏主页面（必需）
├── README.md           # 项目说明（推荐）
├── LICENSE             # 开源许可证（推荐）
└── .gitignore          # Git忽略文件（推荐）
```

## 注意事项

1. 确保主文件名为`index.html`，这是GitHub Pages的默认首页文件名
2. 如果您想使用自定义域名，可以在GitHub Pages设置中配置
3. 游戏是纯前端HTML/JavaScript，不需要服务器端支持
4. 如果遇到问题，检查GitHub Pages的部署日志

## 分享您的游戏

发布成功后，您可以：
- 将链接分享给朋友
- 在社交媒体上展示您的作品
- 考虑添加更多功能或关卡

祝您发布顺利！🐸