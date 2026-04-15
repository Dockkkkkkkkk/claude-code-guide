# 第九章：Computer Use——让 AI 操作你的电脑

# Computer Use——让 AI 操作你的电脑 🟣

> **核心价值**：Computer Use 让 Claude 可以**直接看屏幕、移动鼠标、点击按钮、输入文字**，操作一切有界面的软件。
> 

---

## 9.1 什么是 Computer Use？

Computer Use 是 Anthropic 于 2025 年底推出、**2026 年 Q1 正式 GA**（全面可用）的革命性功能。

Claude Code 可以：

- 👀 看到你的屏幕内容（截屏）
- 🖱️ 移动鼠标并点击任意位置
- ⌨️ 输入文字、按下快捷键
- 📄 操作文件管理器、浏览器、应用程序
- 👁️‍🗨️ 读取屏幕上的文字信息

**适用场景：**

- GUI 应用没有 API，只能通过界面操作
- 自动化第三方软件测试
- 多步骤表单填写与数据入库
- 执行难以命令行自动化的操作

---

## 9.2 快速开始

### 前置条件

```bash
# macOS/Linux 需要安装屏幕共享工具
# macOS 通常内置支持
# Linux 需要 X11 或 Wayland

# 確认显示器环境
 echo $DISPLAY
 # 输出如 :0 表示就绪
```

### 启动 Computer Use

```bash
# 方法一：对话中直接开启
claude
# 输入：
请打开浏览器访问 google.com

# 方法二：命令行指定允许 Computer Use
claude --allowedTools computer_use

# 方法三：在 settings.json 中永久开启
# ~/.claude/settings.json
# 添加 "enableComputerUse": true
```

### 使用示例

```
你：打开 Chrome，访问我们公司的 CRM 系统，
       导出上月的客户数据到桌面的 customers.csv

Claude：[截图] 我看到了屏幕。正在打开 Chrome...
       [点击] 点击地址栏输入网址...
       [输入] crm.company.com
       [截图] 登录页面...
       [点击] 输入账号密码...
       [点击] 导出按钮已定位...
```

---

## 9.3 常用命令与调试

```bash
# 在对话中控制 Computer Use 行为
/computer  # 查看当前 Computer Use 状态

# 如果卡住或出错，可以对 Claude 说：
停止，重新开始
截屏看看现在的屏幕状态
```

### 屏幕分辨率推荐

```bash
# 设置 1920x1080 分辨率，平衡清晰度和速度
# macOS:
export CLAUDE_SCREEN_RESOLUTION="1920x1080"

# 更低分辨率可减少 token 消耗，加快响应
export CLAUDE_SCREEN_RESOLUTION="1280x800"
```

---

## 9.4 安全与权限控制

Computer Use 功能强大，需要小心使用。

**建议的安全实践：**

```bash
# 1. 只开启必要的 Computer Use 权限
claude --allowedTools computer_use:screenshot,computer_use:click

# 2. 在运行第三方 GUI 自动化时，建议在虚拟机或
#    Docker 容器中执行，而不是本机

# 3. 敏感操作（打开邀请页，确认文件删除）
#    建议配合 Hooks 的 PreToolUse 加入人工确认环节

# 4. 必要时使用 --dangerously-skip-permissions 应谨慎
```

---

## 9.5 Docker 安全容器方案（推荐）

对于复杂任务，建议在 Docker 容器中运行 Computer Use，隔离环境：

```docker
# Dockerfile
FROM ubuntu:22.04

# 安装基础环境
RUN apt-get update && apt-get install -y \
    nodejs npm \
    chromium-browser \
    xvfb \
    && rm -rf /var/lib/apt/lists/*

# 安装 Claude Code
RUN npm install -g @anthropic-ai/claude-code

# 设置虚拟显示
ENV DISPLAY=:99

CMD ["Xvfb :99 -screen 0 1920x1080x24 &"]
```

```bash
# 构建和运行
docker build -t claude-computer-use .
docker run -it \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd):/workspace \
  claude-computer-use bash

# 在容器内启动 Claude Code
claude --allowedTools computer_use
```

---

## 🔬 实战案例

### 案例1：自动化测试无 API 的 Web 应用

```
你：请测试我们的电商网站
   测试要求：
   1. 搜索「无线耳机」
   2. 点击第一个结果
   3. 加入购物车
   4. 确认购物车数量为 1
   5. 截屏并保存测试报告

Claude：[截图]正在打开网站...
       [点击]搜索框已定位...
       [输入]无线耳机...
       [截图]搜索结果已显示...
       测试全部通过 ✅
       已保存测试报告到 test-report.png
```

### 案例2：批量填写内部表单（应用无 API）

```
你：我有一个 Excel 表格 customers.xlsx，
   里面有 50 行客户信息。
   请将它们全部导入到公司的 CRM 系统（网页版）

Claude：我将读取 Excel，然后逐行填写...
```

### 案例3：应用界面监控与异常报警

```
你：每隔 10 分钟截图监控我们的运维监控大屏，
   如果发现任何红色预警指示灯，
   请立刻通知我，并将截图保存到 ~/alerts/ 目录
```

---

<aside>
🖥️

**Computer Use 的边界**：AI 第一次更好地操作具有界面的软件。这是 AI 工具应用边界的重大扩展。

</aside>

---

<aside>
⬅️

[← 上一章：Hooks](%E7%AC%AC%E5%85%AB%E7%AB%A0%EF%BC%9AHooks%E2%80%94%E2%80%94%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8%E8%87%AA%E5%8A%A8%E5%8C%96%20bf8cf7731ca1480f9f14172b5eecf243.md)

</aside>

<aside>
➡️

[下一章：多 Agent 协作 →](%E7%AC%AC%E5%8D%81%E7%AB%A0%EF%BC%9A%E5%A4%9A%20Agent%20%E5%8D%8F%E4%BD%9C%E2%80%94%E2%80%94Agent%20Teams%20%E4%B8%8E%E5%B7%A5%E4%BD%9C%E6%B5%81%200f4b5870de4646b0862c49cbd2e13415.md)

</aside>