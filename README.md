# Mario-Cross

GitHub Actions 自托管运行器配置文档和指南

---

## 目录

1. [概述](#概述)
2. [系统要求](#系统要求)
3. [设置步骤](#设置步骤)
4. [在工作流中使用](#在工作流中使用)
5. [故障排查](#故障排查)

---

## 概述

自托管运行器（Self-hosted Runner）允许你在自己的服务器或本地机器上运行 GitHub Actions 工作流。本指南详细说明了如何完成配置。

---

## 系统要求

### 硬件要求

| 配置等级 | RAM | CPU 核心 | 说明 |
|---------|-----|---------|------|
| **最低配置** | 2 GB | 2 核 | 基础工作流 |
| **推荐配置** | 4+ GB | 4+ 核 | 复杂工作流 |

### 支持的操作系统

- ✅ Linux（推荐）
- ✅ Windows
- ✅ macOS

### 设备兼容性

#### MacBook Pro (Retina, 13-inch, Late 2013)

**设备信息：**
- 操作系统：macOS Big Sur（Version 11.7.11）
- 处理器：2.4 GHz Dual-Core Intel Core i5
- 内存：4 GB 1600 MHz DDR3
- 显卡：Intel Iris 1536 MB

**支持情况：**

⚠️ **有限支持** - 满足最低要求，但接近硬件极限

| 项目 | 状态 | 说明 |
|------|------|------|
| **操作系统兼容性** | ✅ 支持 | macOS Big Sur 官方支持 |
| **RAM（4GB）** | ⚠️ 最低门槛 | 能运行，但余量有限 |
| **CPU（双核）** | ⚠️ 最低门槛 | 可满足基础工作流 |
| **建议用途** | ✅ 推荐 | 简单的 CI/CD、静态分析、文本处理 |
| **不建议用途** | ❌ 不推荐 | 构建大型项目、编译、容器操作、并行任务 |

**配置建议：**

1. **关闭不必要的后台应用** - 最大化可用资源
2. **只运行一个工作流实例** - 避免内存溢出
3. **监控资源使用** - 使用 Activity Monitor 检查 CPU/内存占用
4. **定期重启运行器** - 防止内存泄漏
5. **简化工作流** - 避免复杂的并行任务

---

## 设置步骤

### 第一步：创建个人访问令牌（PAT）

1. 登录 [GitHub](https://github.com) 账户
2. 点击右上角头像 → **Settings**（设置）
3. 左侧菜单 → **Developer settings** → **Personal access tokens**
4. 点击 **Generate new token**
5. 设置权限范围：
   - ✅ `repo` - 完整的代码库访问
   - ✅ `admin:org_hook` - 组织 Webhook（如果是组织）
   - ✅ `admin:repo_hook` - 仓库 Webhook
6. 复制生成的 token（⚠️ 只显示一次，请妥善保存）

---

### 第二步：下载并配置运行器

#### Linux 配置示例

```bash
# 创建工作目录
mkdir actions-runner
cd actions-runner

# 下载运行器（检查最新版本）
curl -o actions-runner-linux-x64-2.321.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.321.0/actions-runner-linux-x64-2.321.0.tar.gz

# 解压
tar xzf ./actions-runner-linux-x64-2.321.0.tar.gz

# 进入目录
cd actions-runner

# 运行配置脚本
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN
```

#### macOS 配置（适用于 MacBook Pro）

```bash
# 创建工作目录
mkdir actions-runner
cd actions-runner

# 下载 macOS 运行器（x64 架构）
curl -o actions-runner-osx-x64-2.321.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.321.0/actions-runner-osx-x64-2.321.0.tar.gz

# 解压
tar xzf ./actions-runner-osx-x64-2.321.0.tar.gz

# 进入目录
cd actions-runner

# 运行配置脚本
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN
```

#### Windows 配置示例

```powershell
# 创建工作目录
mkdir actions-runner
cd actions-runner

# 下载运行器
Invoke-WebRequest -Uri "https://github.com/actions/runner/releases/download/v2.321.0/actions-runner-win-x64-2.321.0.zip" `
  -OutFile "actions-runner-win-x64-2.321.0.zip"

# 解压
Expand-Archive -Path "actions-runner-win-x64-2.321.0.zip" -DestinationPath "."

# 运行配置脚本（以管理员身份运行 PowerShell）
.\config.cmd --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN
```

#### 配置参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `YOUR_USERNAME` | 你的 GitHub 用户名 | `MarioNomi` |
| `YOUR_REPO` | 仓库名 | `Mario-Cross` |
| `YOUR_TOKEN` | 刚才生成的 PAT | `ghp_xxxxxxxxxxxxxxxx` |

#### 配置过程中的选项

| 选项 | 说明 | 推荐值 |
|------|------|--------|
| runner name | 给运行器起个名字 | `my-macbook` 或 `my-server` |
| runner group | 运行器分组 | 按 Enter（使用默认） |
| work directory | 工作目录 | 按 Enter（使用默认 `_work`） |
| labels | 自定义标签 | 可选，用于在工作流中标识运行器 |

---

### 第三步：运行运行器

#### macOS（推荐方式）

**前台运行（调试和测试）：**

```bash
cd actions-runner
./run.sh
```

**后台运行（生产环境）：**

```bash
# 安装为 launchd 服务
./svc.sh install

# 启动服务
./svc.sh start

# 检查状态
./svc.sh status

# 停止服务
./svc.sh stop

# 卸载服务
./svc.sh uninstall
```

#### Linux（systemd）

```bash
# 安装为 systemd 服务
sudo ./svc.sh install

# 启动服务
sudo ./svc.sh start

# 检查状态
sudo ./svc.sh status

# 停止服务
sudo ./svc.sh stop

# 卸载服务
sudo ./svc.sh uninstall
```

#### Windows（PowerShell 管理员模式）

```powershell
# 配置运行器
.\config.cmd --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN

# 前台运行
.\run.cmd

# 或安装为 Windows 服务
.\svc.cmd install
.\svc.cmd start
.\svc.cmd status
.\svc.cmd stop
.\svc.cmd uninstall
```

---

## 在工作流中使用

### 编辑工作流文件

编辑 `.github/workflows/workflow.yml` 文件：

```yaml
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
```

### 运行器标签用法

| 标签 | 说明 |
|------|------|
| `runs-on: self-hosted` | 使用任何自托管运行器 |
| `runs-on: [self-hosted, linux]` | 指定 Linux 自托管运行器 |
| `runs-on: [self-hosted, windows]` | 指定 Windows 自托管运行器 |
| `runs-on: [self-hosted, macos]` | 指定 macOS 自托管运行器 |
| `runs-on: my-macbook` | 使用特定名称的运行器 |

---

## 重要注意事项

### 🔒 安全性

- ⚠️ 自托管运行器可以访问你的所有代码和敏感数据
- ⚠️ 只在 **信任的机器** 上运行
- 定期 **更新运行器软件** 到最新版本
- 使用 **防火墙** 限制网络访问
- 定期审查 **运行器日志** 和活动
- 避免在公开仓库中使用自托管运行器（除非极其谨慎）

### 💰 成本对比

| 类型 | 成本 | 适用场景 |
|------|------|---------|
| **GitHub 官方虚拟机（Pro）** | 约 $1.44/月（6000分钟） | 低频使用、临时构建 |
| **自托管运行器** | 服务器成本 + 电力 | 高频使用、长期运行 |

**建议：** 对于高频使用场景，自托管运行器通常更经济。

---

## 故障排查

### 问题 1：运行器离线

**症状：** 运行器显示离线或无法连接

**解决方案：**

```bash
# 检查运行器进程
./run.sh  # 查看实时日志

# 检查网络连接
ping github.com

# 检查防火墙设置
# 确保允许对 github.com 的出站连接

# 重启运行器服务（macOS）
./svc.sh restart
```

### 问题 2：内存不足（特别是 MacBook Pro）

**症状：** 工作流卡顿、进程被 killed、系统变慢

**解决方案：**

```bash
# 检查内存使用（macOS）
top

# 关闭不必要的后台应用
# 在 Activity Monitor 中关闭消耗内存的进程

# 减少并行任务数量
# 编辑工作流，设置 concurrency 限制

# 重启运行器进程
./svc.sh restart
```

### 问题 3：需要删除或重置运行器

```bash
# 进入运行器目录
cd actions-runner

# 从 GitHub 中移除运行器
./config.sh remove --token YOUR_TOKEN

# 清理工作目录
rm -rf _work/*
```

### 问题 4：MacBook Pro 相关问题

**症状：** 构建速度慢、经常超时

**原因：** 双核处理器和 4GB 内存接近硬件极限

**建议：**

1. ✅ 只运行简单工作流（代码检查、单元测试）
2. ✅ 避免编译大型项目或构建容器镜像
3. ✅ 定期监控资源使用情况
4. ⏸️ 考虑升级到更高配置的机器
5. ✅ 或使用 GitHub 官方的虚拟机来处理复杂任务

---

## 常用命令参考

### macOS

```bash
# 启动运行器
./svc.sh start

# 停止运行器
./svc.sh stop

# 重启运行器
./svc.sh restart

# 查看状态
./svc.sh status

# 查看日志
log stream --predicate 'process == "run.sh"' --level debug
```

### Linux

```bash
# 启动运行器
sudo systemctl start actions.runner

# 停止运行器
sudo systemctl stop actions.runner

# 查看状态
sudo systemctl status actions.runner

# 查看日志
sudo journalctl -u actions.runner -f
```

### Windows

```powershell
# 查看服务状态
Get-Service -Name "GitHub Runner Service"

# 启动服务
Start-Service -Name "GitHub Runner Service"

# 停止服务
Stop-Service -Name "GitHub Runner Service"
```

---

**最后更新：** 2026-09-08  
**文档版本：** 2.0（包含 MacBook Pro 支持信息）
