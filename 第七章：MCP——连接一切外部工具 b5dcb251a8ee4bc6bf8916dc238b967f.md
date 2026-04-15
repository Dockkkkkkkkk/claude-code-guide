# 第七章：MCP——连接一切外部工具

# MCP——连接一切外部工具 🟡

> **核心价值**：MCP 是 Claude Code 的「USB 接口」，让 AI 可以直接连接数据库、搜索引擎、内部系统等任意外部工具。
> 

---

## 7.1 MCP 是什么？

MCP（Model Context Protocol）是一个开放协议，让 Claude Code 可以调用外部工具和数据源。

**三种传输类型：**

- **stdio**：本地进程通信（最常用）
- **HTTP**：通过 HTTP 请求连接远程服务
- **SSE**：Server-Sent Events，实时流式连接

**MCP 可以连接什么：**

- 🗄️ 文件系统增强（进阶文件操作）
- 🔍 网页搜索（Brave Search、Bing 等）
- 📊 数据库（SQLite、PostgreSQL、MySQL）
- 📁 云存储（Google Drive、Notion）
- 💬 即时通讯（Slack、Teams）
- 🔧 开发工具（GitHub、Jira、Linear）
- 🌐 自定义内部系统（任意 HTTP API）

---

## 7.2 完整命令参考

```bash
# 添加 MCP 服务器（stdio 类型）
claude mcp add <名称> -- <启动命令> <参数>

# 添加 HTTP/SSE 类型
claude mcp add --transport http <名称> <URL>

# 全局 vs 项目级
claude mcp add --scope user <名称> -- <命令>    # 全局，所有项目可用
claude mcp add --scope project <名称> -- <命令> # 项目级（默认）

# 管理命令
claude mcp list                    # 列出所有 MCP
claude mcp get <名称>              # 查看详细配置
claude mcp remove <名称>           # 删除
claude mcp add-from-claude-desktop  # 从 Desktop 导入已有配置
claude mcp add-json <名称> '<JSON>' # JSON 方式导入

# 对话中查看状态
/mcp
```

---

## 7.3 常用 MCP 服务安装大全（15+个）

### 🗄️ 文件系统增强

```bash
claude mcp add filesystem -- \
  npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

### 🔍 网页搜索（Brave Search）

```bash
# 申请 Brave API Key: https://api.search.brave.com/
claude mcp add brave-search \
  -e BRAVE_API_KEY=你的Key \
  -- npx -y @modelcontextprotocol/server-brave-search
```

### 🔧 GitHub

```bash
# 申请 PAT: https://github.com/settings/tokens
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=你的Token \
  -- npx -y @modelcontextprotocol/server-github
```

### 📓 Notion

```bash
# 申请 Integration Token: https://www.notion.so/my-integrations
claude mcp add notion \
  -e OPENAPI_MCP_HEADERS='{"Authorization":"Bearer 你的Token","Notion-Version":"2022-06-28"}' \
  -- npx -y @notionhq/notion-mcp-server
```

### 📊 SQLite 数据库

```bash
claude mcp add sqlite \
  -- npx -y @modelcontextprotocol/server-sqlite ~/data/db.sqlite
```

### 🔎 PostgreSQL 数据库

```bash
claude mcp add postgres \
  -- npx -y @modelcontextprotocol/server-postgres \
  "postgresql://user:password@localhost/dbname"
```

### 💬 Slack

```bash
# 申请 Slack Bot Token: https://api.slack.com/apps
claude mcp add slack \
  -e SLACK_BOT_TOKEN=xoxb-你的Token \
  -e SLACK_TEAM_ID=T你的TeamID \
  -- npx -y @modelcontextprotocol/server-slack
```

### 🗺️ 谷歌地图

```bash
claude mcp add google-maps \
  -e GOOGLE_MAPS_API_KEY=你的Key \
  -- npx -y @modelcontextprotocol/server-google-maps
```

### ⏰ 时间工具

```bash
claude mcp add time \
  -- npx -y @modelcontextprotocol/server-time
```

### 🧠 顺序思考（增强推理）

```bash
claude mcp add sequential-thinking \
  -- npx -y @modelcontextprotocol/server-sequential-thinking
```

### 🦖 Puppeteer（网页自动化）

```bash
claude mcp add puppeteer \
  -- npx -y @modelcontextprotocol/server-puppeteer
```

### 📝 Memory（持久化记忆）

```bash
claude mcp add memory \
  -- npx -y @modelcontextprotocol/server-memory
```

### 📧 Gmail

```bash
claude mcp add gmail \
  -e GMAIL_CLIENT_ID=你的ClientID \
  -e GMAIL_CLIENT_SECRET=你的Secret \
  -- npx -y @modelcontextprotocol/server-gmail
```

### 📅 Google Calendar

```bash
claude mcp add google-calendar \
  -e GOOGLE_CLIENT_ID=你的ClientID \
  -e GOOGLE_CLIENT_SECRET=你的Secret \
  -- npx -y @modelcontextprotocol/server-google-calendar
```

---

## 7.4 MCP Elicitation——主动请求（2026年新特性）

MCP Elicitation 让 MCP 服务器可以**主动向用户请求信息**，而不是等待用户主动提供。

**应用场景：**

- 表单填写类工具（需要动态获取用户输入）
- 需要确认的高危操作
- 多步骤向导流程

**如何在自定义 MCP 中使用 Elicitation：**

```tsx
// MCP 服务器中发起请求
const result = await server.elicit({
  message: "请输入数据库连接字符串",
  requestedSchema: {
    type: "object",
    properties: {
      host: { type: "string" },
      port: { type: "number" },
      database: { type: "string" }
    }
  }
});
```

---

## 7.5 自建 MCP 服务器入门

如果现有 MCP 服务器无法满足需求（如内部系统、私有 API），可以自建。

**Node.js SDK 20行起步：**

```tsx
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  { name: "my-mcp-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

// 注册一个工具
server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "query-internal-api") {
    const { endpoint } = request.params.arguments;
    const result = await fetch(`https://internal.company.com/api/${endpoint}`);
    return { content: [{ type: "text", text: await result.text() }] };
  }
});

// 启动服务器
const transport = new StdioServerTransport();
await server.connect(transport);
```

**添加到 Claude Code：**

```bash
claude mcp add my-server -- node /path/to/my-mcp-server.js
```

---

## 🔬 实战案例

### 案例1：连接 Notion，让 Claude 直接读写你的知识库

```bash
# 安装 Notion MCP
claude mcp add notion \
  -e OPENAPI_MCP_HEADERS='{"Authorization":"Bearer secret_xxx","Notion-Version":"2022-06-28"}' \
  -- npx -y @notionhq/notion-mcp-server

# 使用示例：
# 在对话中输入：
在我的 Notion 知识库里找到关于「项目管理」的所有文档，整理成摘要
```

### 案例2：连接公司数据库，用自然语言查询业务数据

```bash
# 连接 PostgreSQL
claude mcp add company-db \
  -- npx -y @modelcontextprotocol/server-postgres \
  "postgresql://readonly_user:pass@db.company.com/production"

# 使用示例：
# 在对话中输入：
查询上月销售额前10名的客户，并分析他们的购买规律
```

### 案例3：GitHub + 搜索 组合，自动生成竞品调研报告

```bash
# 安装两个 MCP
claude mcp add brave-search \
  -e BRAVE_API_KEY=你的Key \
  -- npx -y @modelcontextprotocol/server-brave-search

claude mcp add filesystem -- \
  npx -y @modelcontextprotocol/server-filesystem ~/reports

# 使用：
调研 Claude Code 的主要竞产官方最新动态，
将结果保存到 ~/reports/competitor-research.md
```

### 案例4：对话中自然语言操作 GitHub

```bash
# 安装 GitHub MCP
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxxx \
  -- npx -y @modelcontextprotocol/server-github

# 对话中直接说：
# 在我的 my-project 仓库创建一个 Issue：
# 标题「登录页面返回 500 错误」
# 内容包括复现步骤和预期行为
```

---

<aside>
🔌

**MCP 的魔力**：一旦配置好 MCP，Claude Code 就从一个文件处理工具变成了可以操作整个业务数字生态的超级助手。

</aside>

---

<aside>
⬅️

[← 上一章：Skills](%E7%AC%AC%E5%85%AD%E7%AB%A0%EF%BC%9ASkills%E2%80%94%E2%80%94%E5%B0%81%E8%A3%85%E4%BD%A0%E7%9A%84%E4%B8%93%E5%B1%9E%E5%B7%A5%E4%BD%9C%E6%B5%81%208009af83d4ea440f810ced48fb4f0d61.md)

</aside>

<aside>
➡️

[下一章：Hooks →](%E7%AC%AC%E5%85%AB%E7%AB%A0%EF%BC%9AHooks%E2%80%94%E2%80%94%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8%E8%87%AA%E5%8A%A8%E5%8C%96%20bf8cf7731ca1480f9f14172b5eecf243.md)

</aside>