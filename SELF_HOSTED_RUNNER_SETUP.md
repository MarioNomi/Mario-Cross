# GitHub Actions 自托管运行器（Self-hosted Runner）设置指南

## 目录
1. [什么是自托管运行器](#什么是自托管运行器)
2. [系统要求](#系统要求)
3. [下载和配置](#下载和配置)
4. [运行和管理](#运行和管理)
5. [高级配置](#高级配置)
6. [常见问题](#常见问题)

---

## 什么是自托管运行器

自托管运行器是您在自己的计算机或服务器上安装的 GitHub Actions 运行器。相比于 GitHub 托管的运行器，自托管运行器具有以下优势：

- **成本节省**：GitHub Actions 免费账户有月度分钟限制，自托管运行器不受限制
- **自定义环境**：完全控制运行环境的软件、硬件和配置
- **更好的性能**：使用本地资源而不是云资源
- **私密性**：代码和构建工件保留在您的基础设施上

---

## 系统要求

### 操作系统支持
- **Linux**（Ubuntu、Debian、CentOS 等）
- **macOS**（10.13 及更高版本）
- **Windows**（Windows 7 及更高版本）

### 硬件要求
- **处理器**：2核 CPU 以上
- **内存**：4GB RAM 以上（建议 8GB 或更多）
- **存储**：至少 10GB 可用空间
- **网络**：稳定的互联网连接，能够访问 github.com

### 软件要求
- Git
- Node.js（可选，但推荐）
- Docker（可选）

---

## 下载和配置

### 第1步：获取下载链接

1. 进入您的 GitHub 仓库
2. 点击 **Settings**（设置）
3. 在左侧菜单中选择 **Actions** → **Runners**
4. 点击 **New self-hosted runner** 按钮

### 第2步：下载运行器

根据您的操作系统选择正确的版本。以下以 Linux (x64) 为例：

```bash
# 创建运行器目录
mkdir -p ~/actions-runner
cd ~/actions-runner

# 下载最新的运行器包（获取下载链接来自 GitHub 界面）
curl -o actions-runner-linux-x64-2.X.X.tar.gz -L https://github.com/actions/runner/releases/download/v2.X.X/actions-runner-linux-x64-2.X.X.tar.gz

# 解压文件
tar xzf ./actions-runner-linux-x64-2.X.X.tar.gz
```

### 第3步：配置运行器

```bash
# 进入运行器目录
cd ~/actions-runner

# 运行配置脚本
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN
```

**参数说明：**
- `--url`：您的仓库 URL
- `--token`：GitHub 提供的临时注册令牌（在 Settings → Runners 中获得）
- `--name`（可选）：运行器的名称，默认为主机名
- `--work`（可选）：工作目录，默认为 `_work`
- `--labels`（可选）：为运行器添加标签，例如 `--labels linux,docker`

**交互式配置示例：**

```
Enter the name of the runner: [hostname] my-runner
Enter any additional labels (optional, comma-separated): linux,docker
Enter name of work folder (optional): _work
Enter the user to run the service as (optional): ubuntu
```

### 第4步：验证配置

配置完成后，您会看到类似的输出：

```
√ Connected to GitHub

Current runner version: 2.X.X
Runner name: 'my-runner'
Runner OS: 'linux'
Runner Arch: 'x64'
...
```

---

## 运行和管理

### 作为前台进程运行（测试用）

```bash
cd ~/actions-runner
./run.sh
```

输出示例：
```
√ Connected to GitHub

Current runner version: 2.X.X
√ Settings Saved.
√ Listening for Jobs
```

按 `Ctrl+C` 停止运行。

### 作为服务运行（推荐用于生产环境）

#### Linux/macOS - 使用 Systemd

```bash
# 安装服务
sudo ./svc.sh install

# 启动服务
sudo ./svc.sh start

# 查看服务状态
sudo ./svc.sh status

# 停止服务
sudo ./svc.sh stop

# 卸载服务
sudo ./svc.sh uninstall
```

#### Linux - 使用 Systemd（手动配置）

```bash
# 创建 systemd 服务文件
sudo nano /etc/systemd/system/actions-runner.service
```

服务文件内容：
```ini
[Unit]
Description=GitHub Actions Runner
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/actions-runner
ExecStart=/home/ubuntu/actions-runner/run.sh
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

启动服务：
```bash
sudo systemctl daemon-reload
sudo systemctl enable actions-runner
sudo systemctl start actions-runner
sudo systemctl status actions-runner
```

#### Windows - 使用 PowerShell

以管理员身份运行 PowerShell：

```powershell
# 安装服务
.\svc.cmd install

# 启动服务
.\svc.cmd start

# 查看服务状态
Get-Service "GitHub Actions Runner"

# 停止服务
.\svc.cmd stop

# 卸载服务
.\svc.cmd uninstall
```

### 监控和检查

在 GitHub 界面中查看运行器状态：

1. 进入仓库 → **Settings** → **Actions** → **Runners**
2. 您应该看到运行器以"在线"状态显示

---

## 高级配置

### 添加自定义标签

标签用于在工作流中选择特定的运行器。

```bash
# 配置时添加标签
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN --labels custom-label1,custom-label2
```

### 在工作流中使用自托管运行器

编辑您的 GitHub Actions 工作流文件（`.github/workflows/*.yml`）：

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: self-hosted
    # 或使用特定标签
    # runs-on: [self-hosted, linux, docker]
    
    steps:
      - uses: actions/checkout@v3
      - name: Run script
        run: echo "Running on self-hosted runner!"
```

### 更新运行器

```bash
cd ~/actions-runner

# 下载新版本
curl -o actions-runner-linux-x64-2.X.X.tar.gz -L https://github.com/actions/runner/releases/download/v2.X.X/actions-runner-linux-x64-2.X.X.tar.gz

# 停止服务
sudo ./svc.sh stop

# 解压新版本（会覆盖旧文件）
tar xzf ./actions-runner-linux-x64-2.X.X.tar.gz

# 启动服务
sudo ./svc.sh start
```

### 删除运行器

```bash
cd ~/actions-runner

# 停止服务
sudo ./svc.sh stop

# 卸载服务
sudo ./svc.sh uninstall

# 运行删除脚本
./config.sh remove --token YOUR_TOKEN
```

---

## 高级配置

### 环境变量

创建 `.env` 文件以设置环境变量：

```bash
cd ~/actions-runner
nano .env
```

示例内容：
```
GITHUB_ACTIONS_RUNNER_DEBUG=true
NODE_OPTIONS=-max-old-space-size=4096
```

### 代理配置

如果需要通过代理访问 GitHub：

```bash
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN \
  --proxyurl http://proxy-server:8080 \
  --proxyusername username \
  --proxypassword password
```

### 并发限制

编辑 `.env` 文件：

```
GITHUB_ACTIONS_RUNNER_CONCURRENT_JOBS=2
```

### 安全加固

```bash
# 限制运行器文件权限
chmod 700 ~/actions-runner

# 为运行器创建专用用户（可选）
sudo useradd -m -s /bin/bash runner
sudo chown -R runner:runner ~/actions-runner
```

---

## 常见问题

### Q1：运行器显示离线状态

**原因：** 运行器进程未运行或网络连接丢失

**解决方案：**
```bash
# 检查服务状态
sudo systemctl status actions-runner

# 查看日志
sudo journalctl -u actions-runner -f

# 重启服务
sudo systemctl restart actions-runner
```

### Q2：无法连接到 GitHub

**原因：** 网络问题、防火墙限制或令牌过期

**解决方案：**
- 测试网络连接：`ping github.com`
- 检查防火墙设置，确保允许访问 github.com
- 重新生成并使用新的注册令牌
- 检查代理设置

### Q3：工作流不使用自托管运行器

**原因：** `runs-on` 配置不正确或标签不匹配

**解决方案：**
```yaml
# 确保使用正确的运行器标识符
runs-on: self-hosted
# 或
runs-on: [self-hosted, linux]
```

### Q4：运行器内存或磁盘空间不足

**解决方案：**
```bash
# 清理旧的工作流运行文件
cd ~/actions-runner/_work
find . -type d -mtime +30 -exec rm -rf {} \;

# 增加分配的内存
# 编辑服务配置文件中的内存限制
```

### Q5：如何检查运行器日志？

```bash
# Linux/macOS
tail -f ~/actions-runner/_diag/Runner_*.log

# Windows
type C:\actions-runner\_diag\Runner_*.log
```

---

## 最佳实践

1. **定期更新**：保持运行器软件为最新版本
2. **监控资源**：定期检查 CPU、内存和磁盘使用情况
3. **备份配置**：保存运行器配置文件的备份
4. **使用标签**：为不同目的的运行器添加清晰的标签
5. **日志管理**：定期清理过期的日志文件
6. **安全性**：
   - 限制对运行器文件夹的访问权限
   - 定期轮换访问令牌
   - 在专用用户下运行运行器（不要使用 root）
7. **高可用性**：为关键服务部署多个运行器

---

## 参考资源

- [GitHub Actions 官方文档](https://docs.github.com/en/actions)
- [自托管运行器文档](https://docs.github.com/en/actions/hosting-your-own-runners)
- [自托管运行器安全](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
- [GitHub Runner 项目](https://github.com/actions/runner)

---

**最后更新**：2026-09-08
