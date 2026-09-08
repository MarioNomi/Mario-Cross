# Mario-Cross
GitHub Actions 自托管运行器配置文档和指南

我来为你详细介绍如何在自己的服务器或本地机器上设置和运行 GitHub Actions。

自托管运行器（Self-hosted Runner）设置步骤

第一步：创建个人访问令牌（PAT）

1. 登录 GitHub 账户

2. 点击右上角头像 → Settings（设置）

3. 左侧菜单 → Developer settings → Personal access tokens

4. 点击 Generate new token

5. 选择权限范围：

• repo - 完整的代码库访问

• admin:org_hook - （如果是组织）
• admin:repo_hook - 仓库 Webhook

6. 复制生成的 token（只显示一次）

第二步：下载并配置运行器

在你要运行的服务器/本地机器上执行：

1. 进入仓库设置

• GitHub 网站 → 你的仓库 → Settings

• 左侧菜单 → Actions → Runners

• 点击 New self-hosted runner

2. 根据操作系统选择（以 Linux 为例）
# 创建工作目录
mkdir actions-runner
cd actions-runner

# 下载运行器
curl -o actions-runner-linux-x64-2.321.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.321.0/actions-runner-linux-x64-2.321.0.tar.gz

# 解压
tar xzf ./actions-runner-linux-x64-2.321.0.tar.gz

3. 配置运行器
# 进入目录
cd actions-runner

# 运行配置脚本
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN

• YOUR_USERNAME - 你的 GitHub 用户名

• YOUR_REPO - 仓库名

• YOUR_TOKEN - 刚才生成的 PAT

配置过程中的选项：

• runner name - 给运行器起个名字（如：my-server）

• runner group - 默认按 Enter

• work directory - 默认 _work（按 Enter）

第三步：运行运行器

选项 A：前台运行（调试用）

./run.sh

选项 B：后台运行（生产环境推荐）

Linux/macOS - 使用 systemd：

sudo ./svc.sh install
sudo ./svc.sh start
sudo ./svc.sh status

Windows - 使用 PowerShell（以管理员身份）：

.\config.cmd --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN
.\run.cmd

或安装为服务：

.\svc.cmd install
.\svc.cmd start

第四步：在工作流中使用自托管运行器

编辑你的 .github/workflows/workflow.yml 文件：

name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: self-hosted  # 关键：指定自托管运行器
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run tests
        run: |
          npm install
          npm test
          
可用的运行器标签：

runs-on: self-hosted                    # 使用任何自托管运行器
runs-on: [self-hosted, linux]          # 指定 Linux 自托管运行器
runs-on: [self-hosted, windows]        # 指定 Windows 自托管运行器
runs-on: my-server                     # 使用特定名称的运行器

重要注意事项

安全性

• ⚠️ 自托管运行器可以访问你的所有代码

• 只在信任的机器上运行

• 定期更新运行器软件

• 使用防火墙限制访问

硬件要求

• 最低：2 GB RAM，2 CPU 核心

• 推荐：4+ GB RAM，4+ CPU 核心（取决于工作流复杂度）

成本对比

类型                        每月成本
Pro 按量计费（6000分钟）      ~$1.44 USD
自托管运行器                 服务器成本 + 电力
对于高频使用，自托管运行器通常更经济。

故障排查
运行器离线？
# 检查运行器状态
./run.sh  # 查看实时日志

需要删除运行器？
./config.sh remove --token 
YOUR_TOKEN


