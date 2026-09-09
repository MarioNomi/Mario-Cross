# 如何玩转 GitHub 步骤完全指南

> 本指南将教你如何像专业开发者一样使用 GitHub，从基础到高级操作。

## 📚 目录

1. [GitHub 基础概念](#github-基础概念)
2. [账户设置和初始化](#账户设置和初始化)
3. [仓库操作](#仓库操作)
4. [分支管理](#分支管理)
5. [提交和同步](#提交和同步)
6. [Pull Request 工作流](#pull-request-工作流)
7. [Issue 管理](#issue-管理)
8. [GitHub Actions 自动化](#github-actions-自动化)
9. [协作开发](#协作开发)
10. [高级技巧](#高级技巧)

---

## GitHub 基础概念

### 核心术语

| 术语 | 解释 |
|------|------|
| **Repository (仓库)** | 项目代码的容器，包含所有文件和历史记录 |
| **Commit (提交)** | 对代码的一次保存，包含变更和描述 |
| **Branch (分支)** | 独立的代码开发线，用于并行开发 |
| **Merge (合并)** | 将一个分支的代码合并到另一个分支 |
| **Pull Request (PR)** | 请求审查和合并代码更改的机制 |
| **Issue (问题)** | 项目中的问题、功能请求或讨论 |
| **Fork (复制)** | 创建他人仓库的个人副本 |
| **Clone (克隆)** | 将远程仓库下载到本地 |

### GitHub 的三个层次

```
┌─────────────────────────────────────┐
│   你的本地计算机 (Local)            │
│   - 工作目录 (Working Directory)    │
│   - 暂存区 (Staging Area)           │
│   - 本地仓库 (.git)                 │
└─────────────────────────────────────┘
              ↕ Push/Pull
┌─────────────────────────────────────┐
│   GitHub 服务器 (Remote)            │
│   - origin (远程仓库)               │
│   - main/develop 等分支             │
└─────────────────────────────────────┘
```

---

## 账户设置和初始化

### 第一步：创建 GitHub 账户

1. 访问 https://github.com
2. 点击 **Sign up**（注册）
3. 输入邮箱、密码和用户名
4. 完成验证

### 第二步：配置本地 Git

```bash
# 安装 Git
# Ubuntu/Debian
sudo apt-get install git

# macOS
brew install git

# Windows
# 从 https://git-scm.com 下载安装

# 配置用户信息
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 查看配置
git config --list
```

### 第三步：设置 SSH 密钥

SSH 密钥使你能安全地与 GitHub 通信，无需每次输入密码。

```bash
# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "your.email@example.com"

# 或者使用 RSA（较旧系统）
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# 按 Enter 接受默认位置
# 输入密码（可留空）
```

**添加公钥到 GitHub**

```bash
# 复制公钥内容
cat ~/.ssh/id_ed25519.pub

# 粘贴到 GitHub:
# Settings → SSH and GPG keys → New SSH key
# 粘贴内容，点击 Add SSH key
```

**测试连接**

```bash
ssh -T git@github.com
# 应该输出：Hi USERNAME! You've successfully authenticated...
```

### 第四步：生成个人访问令牌 (PAT)

用于 Git 操作和 API 调用。

```
访问 https://github.com/settings/tokens
→ Generate new token (classic)
→ 选择权限：repo, workflow, admin:repo_hook
→ 复制令牌并保存
```

---

## 仓库操作

### 创建新仓库

**方法 1：在 GitHub 网站上**

1. 点击 **+** → **New repository**
2. 输入仓库名称（如 `my-project`）
3. 添加描述（可选）
4. 选择 Public（公开）或 Private（私密）
5. 选择初始化选项（README、.gitignore、License）
6. 点击 **Create repository**

**方法 2：使用命令行**

```bash
# 创建本地目录
mkdir my-project
cd my-project

# 初始化 Git 仓库
git init

# 添加远程源
git remote add origin https://github.com/USERNAME/my-project.git

# 创建初始分支
git branch -M main

# 创建初始文件
echo "# My Project" > README.md

# 提交
git add .
git commit -m "Initial commit"

# 推送到 GitHub
git push -u origin main
```

### 克隆现有仓库

```bash
# 克隆仓库（使用 HTTPS）
git clone https://github.com/USERNAME/repo-name.git

# 或使用 SSH（推荐）
git clone git@github.com:USERNAME/repo-name.git

# 克隆到特定目录
git clone git@github.com:USERNAME/repo-name.git my-folder

# 进入目录
cd repo-name
```

### 查看仓库信息

```bash
# 查看远程仓库信息
git remote -v

# 查看当前分支
git branch

# 查看所有分支（包括远程）
git branch -a

# 查看仓库状态
git status

# 查看最近的提交
git log --oneline -10
```

---

## 分支管理

### 为什么使用分支？

分支允许你并行开发不同功能，互不影响。

```
main (主分支)
├── feature/login (功能分支)
├── bugfix/navbar (修复分支)
└── develop (开发分支)
```

### 创建和切换分支

```bash
# 创建新分支
git branch feature/new-feature

# 创建并切换到新分支（推荐）
git checkout -b feature/new-feature

# 或使用新语法
git switch -c feature/new-feature

# 切换到已有分支
git checkout main
git switch main

# 查看所有分支
git branch -a

# 删除本地分支
git branch -d feature/old-feature

# 删除远程分支
git push origin --delete feature/old-feature
```

### 分支命名规范

```bash
# 功能分支
git checkout -b feature/user-authentication

# 修复分支
git checkout -b bugfix/login-error

# 文档分支
git checkout -b docs/setup-guide

# 发布分支
git checkout -b release/v1.0.0

# 热修复分支
git checkout -b hotfix/critical-bug
```

---

## 提交和同步

### 提交工作流

```bash
# 1. 检查修改
git status

# 2. 查看具体改动
git diff
git diff --staged  # 查看暂存区的改动

# 3. 添加文件到暂存区
git add filename.txt      # 添加单个文件
git add .                 # 添加所有修改
git add *.js              # 添加所有 .js 文件

# 4. 创建提交
git commit -m "Fix: login button color"

# 5. 推送到远程
git push origin feature/button-fix
```

### 提交信息规范

```bash
# ✅ 好的提交信息
git commit -m "feat: add user login functionality"
git commit -m "fix: resolve navbar alignment issue"
git commit -m "docs: update installation guide"

# ❌ 不好的提交信息
git commit -m "update"
git commit -m "stuff"
git commit -m "修改代码"
```

**提交类型前缀**

| 前缀 | 说明 |
|------|------|
| `feat:` | 新功能 |
| `fix:` | 修复错误 |
| `docs:` | 文档更新 |
| `style:` | 代码风格（不影响功能） |
| `refactor:` | 代码重构 |
| `perf:` | 性能优化 |
| `test:` | 测试相关 |
| `chore:` | 依赖更新、配置改动 |

### 同步代码

```bash
# 获取最新更改但不合并
git fetch origin

# 拉取最新更改（fetch + merge）
git pull origin main

# 使用 rebase（保持历史线性）
git pull --rebase origin main

# 推送本地分支到远程
git push origin feature-branch

# 推送所有分支
git push --all

# 删除远程分支
git push origin --delete feature-branch
```

---

## Pull Request 工作流

### 创建 Pull Request

#### 步骤 1：推送分支到 GitHub

```bash
git push origin feature/new-feature
```

#### 步骤 2：在 GitHub 上创建 PR

1. 打开你的仓库
2. 点击 **Pull requests**
3. 点击 **New pull request**
4. 选择比较分支（feature/new-feature → main）
5. 填写 PR 信息：

```markdown
## 描述
简短描述这个 PR 的目的。

## 相关 Issue
Fix #123  （如果修复了某个 Issue）
Related to #456

## 变更内容
- 添加用户登录功能
- 改进错误处理
- 更新文档

## 测试方法
1. 克隆分支
2. 运行 `npm install`
3. 运行 `npm test`

## 截图（如果有 UI 变化）
[粘贴截图]
```

6. 点击 **Create pull request**

### 审查和合并 PR

**作为审查者**

```bash
# 查看 PR 变更
git fetch origin pull/123/head:pr-123
git checkout pr-123
git diff main  # 查看差异

# 测试 PR 代码
npm install
npm test

# 在 GitHub 上评论和批准
```

**作为 PR 作者**

1. 回应评论
2. 根据建议更新代码
3. 再次提交更改

**合并 PR**

1. 所有 CI/CD 检查通过
2. 至少获得一个批准
3. 点击 **Merge pull request**
4. 选择合并策略：
   - **Create a merge commit** - 保留完整历史
   - **Squash and merge** - 合并为单个提交
   - **Rebase and merge** - 重新应用提交

---

## Issue 管理

### 创建 Issue

1. 点击 **Issues** → **New issue**
2. 选择模板（Bug report、Feature request 等）
3. 填写标题和描述
4. 添加标签、指派人员、里程碑
5. 点击 **Submit new issue**

### Issue 模板示例

**Bug Report**
```markdown
## 问题描述
简要描述遇到的问题

## 复现步骤
1. 打开应用
2. 点击登录
3. 问题出现

## 预期行为
应该成功登录

## 实际行为
显示错误消息

## 环境信息
- 操作系统：Ubuntu 20.04
- 浏览器：Chrome 90
- 版本：v1.0.0

## 日志或错误信息
```

**Feature Request**
```markdown
## 功能描述
想要添加什么功能？

## 用途
这个功能如何帮助用户？

## 可能的实现方式
你有什么建议吗？

## 相关资源
链接相关的 Issue 或讨论
```

### Issue 标签

| 标签 | 用途 |
|------|------|
| `bug` | 错误或缺陷 |
| `enhancement` | 功能改进 |
| `documentation` | 文档更新 |
| `good first issue` | 新贡献者友好 |
| `help wanted` | 需要帮助 |
| `question` | 提问 |
| `wontfix` | 不会修复 |

---

## GitHub Actions 自动化

### 创建工作流文件

在你的仓库中创建 `.github/workflows/ci.yml`：

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # 添加你的部署命令
```

### 常用 Actions

```yaml
# 检出代码
- uses: actions/checkout@v4

# 设置 Node.js
- uses: actions/setup-node@v4
  with:
    node-version: '18'

# 设置 Python
- uses: actions/setup-python@v4
  with:
    python-version: '3.9'

# 上传制品
- uses: actions/upload-artifact@v3
  with:
    name: build
    path: dist/

# 发布版本
- uses: actions/create-release@v1
  with:
    tag_name: v1.0.0
    release_name: Release v1.0.0
```

---

## 协作开发

### 团队工作流

#### 情景：多人开发同一项目

**第一步：克隆仓库**
```bash
git clone git@github.com:team/project.git
cd project
```

**第二步：创建功能分支**
```bash
git checkout -b feature/my-feature
```

**第三步：开发功能**
```bash
# 修改代码
git add .
git commit -m "feat: add new feature"
```

**第四步：推送并创建 PR**
```bash
git push origin feature/my-feature
# 在 GitHub 上创建 PR
```

**第五步：合并 PR**
```bash
# 在 GitHub 上审查并合并 PR
```

**第六步：更新本地 main**
```bash
git checkout main
git pull origin main
```

### 处理合并冲突

```bash
# 拉取最新代码时发现冲突
git pull origin main
# Conflict in file.js

# 打开文件，你会看到：
# <<<<<<< HEAD
# 你的代码
# =======
# 他人的代码
# >>>>>>> origin/main

# 编辑文件，解决冲突
vim file.js

# 标记已解决
git add file.js

# 完成合并
git commit -m "Resolve merge conflict"
git push origin feature-branch
```

### 代码审查最佳实践

**作为 PR 作者**
- ✅ 提供清晰的 PR 描述
- ✅ 保持 PR 规模小（< 400 行）
- ✅ 链接相关的 Issue
- ✅ 主动回应反馈

**作为审查者**
- ✅ 称赞良好的代码
- ✅ 提出建议而不是命令
- ✅ 关注逻辑而不是风格
- ✅ 及时批准或请求更改

---

## 高级技巧

### Stash（储存）工作进度

```bash
# 保存当前未提交的更改
git stash

# 查看所有 stash
git stash list

# 恢复最近的 stash
git stash pop

# 恢复特定的 stash
git stash apply stash@{0}

# 删除 stash
git stash drop stash@{0}
```

### Rebase（变基）

```bash
# 交互式 rebase（合并多个提交）
git rebase -i HEAD~3

# 选择要修改的提交：
# pick / squash / reword / edit

# 使用 rebase 而不是 merge
git pull --rebase origin main
```

### Cherry-pick（挑选提交）

```bash
# 将特定的提交应用到当前分支
git cherry-pick <commit-hash>

# 应用多个提交
git cherry-pick <hash1> <hash2>
```

### 标签（Tags）

```bash
# 创建标签
git tag v1.0.0

# 推送标签
git push origin v1.0.0

# 查看所有标签
git tag

# 删除标签
git tag -d v1.0.0
git push origin --delete v1.0.0
```

### 版本发布工作流

```bash
# 1. 创建发布分支
git checkout -b release/v1.0.0

# 2. 更新版本号
# 修改 package.json, setup.py 等

# 3. 创建标签
git tag v1.0.0
git push origin release/v1.0.0 v1.0.0

# 4. 合并回 main
git checkout main
git merge release/v1.0.0
git push origin main

# 5. 合并回 develop
git checkout develop
git merge release/v1.0.0
git push origin develop
```

### GitHub CLI 工具

使用 `gh` 命令行工具更高效地操作：

```bash
# 安装
brew install gh  # macOS
sudo apt install gh  # Ubuntu

# 登录
gh auth login

# 创建 PR
gh pr create --title "Add new feature" --body "Description"

# 查看 PR
gh pr list
gh pr view 123

# 合并 PR
gh pr merge 123

# 创建 Issue
gh issue create --title "Bug report"

# 查看 Issue
gh issue list
```

---

## 常见命令速查表

```bash
# 基础操作
git status              # 查看状态
git add .               # 添加所有修改
git commit -m "msg"     # 提交
git push origin main    # 推送
git pull origin main    # 拉取

# 分支操作
git branch              # 查看分支
git checkout -b dev     # 创建并切换分支
git merge dev           # 合并分支
git branch -d dev       # 删除分支

# 撤销操作
git restore file        # 撤销未暂存的修改
git restore --staged f  # 撤销暂存
git revert HEAD         # 撤销最后一次提交（创建新提交）
git reset --hard HEAD~1 # 硬重置（谨慎使用）

# 查看历史
git log --oneline       # 查看提交历史
git log --graph         # 查看分支图
git diff                # 查看修改
git show <commit>       # 查看特定提交
```

---

## 常见问题 (FAQ)

**Q: 我不小心推送了敏感信息怎么办？**
A: 立即删除敏感信息、重置密码，然后从 git 历史中删除：
```bash
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD
git push origin --force
```

**Q: 如何撤销已推送的提交？**
A: 
```bash
git revert <commit-hash>
git push origin main
```

**Q: 我应该使用 HTTPS 还是 SSH？**
A: SSH 更安全，不需要每次输入密码。建议使用 SSH。

**Q: 如何参与开源项目？**
A: 
1. Fork 项目
2. Clone 你的 Fork
3. 创建功能分支
4. 提交 PR
5. 等待审查和合并

---

## 资源和进一步学习

- [Git 官方文档](https://git-scm.com/doc)
- [GitHub 官方指南](https://docs.github.com)
- [GitHub 学习实验室](https://lab.github.com)
- [Pro Git 书籍](https://git-scm.com/book/zh/v2)

---

**文档版本**: 1.0  
**最后更新**: 2024 年 9 月 8 日

现在你已经掌握了 GitHub 的核心操作！继续练习，逐步成为 GitHub 高手。🚀
