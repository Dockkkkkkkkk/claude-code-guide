# 第十二章：Claude Code SDK 与自动化集成

# Claude Code SDK 与自动化集成 🟡

> **核心价值**：通过 SDK 将 Claude Code 嵌入到你自己的应用、脚本、自动化流程中，构建自定义 AI 工作流。
> 

---

## 12.1 Claude Code SDK 简介

SDK 提供了两种主要调用方式：

| 方式 | 适用场景 |
| --- | --- |
| **TypeScript/Python 库** | 将 Claude Code 嵌入到应用程序 |
| **CLI 子进程方式** | 在 shell 脚本和环境中调用 |

**常用场景：**

- 构建自定义 CI/CD 脚本
- 屋凸自动化任务
- 将 Claude Code 嵌入业务系统
- 开发内部工具和面板

---

## 12.2 TypeScript SDK 安装与使用

### 安装

```bash
npm install @anthropic-ai/claude-code
```

### 基础用法

```tsx
import { query } from "@anthropic-ai/claude-code";

// 最简单的调用
const result = await query({
  prompt: "分析 ./src 目录中的所有 TypeScript 文件，找出未使用的导出",
  options: {
    maxTurns: 5,
    cwd: process.cwd()
  }
});

console.log(result.output);
```

### 流式输出

```tsx
import { query } from "@anthropic-ai/claude-code";

// 实时流式输出
for await (const event of query({
  prompt: "重构 ./src/utils.ts，按功能拆分模块",
  options: { maxTurns: 10 }
})) {
  switch (event.type) {
    case 'text':
      process.stdout.write(event.text);
      break;
    case 'tool_use':
      console.log(`\n[执行工具：${event.name}]`);
      break;
    case 'result':
      console.log('\n✅ 任务完成');
      break;
  }
}
```

### 带 MCP 的 SDK 调用

```tsx
import { query } from "@anthropic-ai/claude-code";

const result = await query({
  prompt: "查询数据库中上月活跃用户列表",
  options: {
    mcpServers: [
      {
        name: "database",
        command: "npx",
        args: ["-y", "@modelcontextprotocol/server-postgres",
               "postgresql://user:pass@localhost/db"]
      }
    ]
  }
});
```

---

## 12.3 Python SDK 安装与使用

```bash
pip install claude-code-sdk
```

```python
import asyncio
from claude_code_sdk import query, ClaudeCodeOptions

async def main():
    # 基础调用
    result = await query(
        prompt="分析 requirements.txt，找出安全风险依赖",
        options=ClaudeCodeOptions(
            max_turns=5,
            cwd="/path/to/project"
        )
    )
    print(result.output)
    
    # 流式输出
    async for event in query(
        prompt="编写该项目的 README.md",
        options=ClaudeCodeOptions(max_turns=10)
    ):
        if event.type == "text":
            print(event.text, end="", flush=True)

asyncio.run(main())
```

---

## 12.4 CLI 子进程方式（Shell 脚本集成）

```bash
# 最简单：直接管道输入
claude --print "分析这个项目的安全漏洞" < /dev/null

# 通过 stdin 传入上下文
cat error.log | claude --print "分析这个错误日志，找出根本原因"

# 输出 JSON 格式（方便脚本解析）
claude --print --output-format json "第一个 issue 是什么？"

# 在 Shell 脚本中使用
#!/bin/bash
ERRORS=$(npm test 2>&1 | tail -20)
claude --print "$ERRORS 有什么修复建议？"
```

---

## 12.5 实用集成模式

### n8n 流程集成

```jsx
// n8n Code 节点中使用 Claude Code SDK
const { query } = require('@anthropic-ai/claude-code');

const inputData = $input.all()[0].json;
const result = await query({
  prompt: `分析这个客评并分类: ${inputData.review}`,
  options: { maxTurns: 3 }
});

return [{ json: { category: result.output, original: inputData } }];
```

### 实时监控脚本

```tsx
import { query } from "@anthropic-ai/claude-code";
import * as fs from "fs";

// 监控文件变化，自动分析
async function analyzeChange(file: string) {
  const diff = execSync(`git diff ${file}`).toString();
  const result = await query({
    prompt: `以下代码变更是否有安全隐患？\n${diff}`,
    options: { maxTurns: 3 }
  });
  if (result.output.includes("高险")) {
    sendAlert(`安全风险在 ${file}`);
  }
}

// 监听文件变化
fs.watch("./src", { recursive: true }, (_, file) => {
  if (file?.endsWith(".ts")) analyzeChange(file);
});
```

### 定时任务 (Cron)

```tsx
import { query } from "@anthropic-ai/claude-code";

// 每天凌晨 2点自动优化
async function dailyRefactor() {
  const files = glob.sync("./src/**/*.ts");
  const candidates = files
    .map(f => ({ file: f, size: fs.statSync(f).size }))
    .filter(f => f.size > 10000)  // 超过 10KB的文件
    .sort((a, b) => b.size - a.size)
    .slice(0, 3);  // 每天处理大文件

  for (const { file } of candidates) {
    await query({
      prompt: `小幅重构 ${file}：提取重复逻辑，改善命名`,
      options: { maxTurns: 15 }
    });
  }
}
```

---

## 🔬 实战案例

### 案例1：构建内部代码质量审查工具

```tsx
import { query } from "@anthropic-ai/claude-code";
import express from "express";

const app = express();

// 内部 Web 工具接口
app.post('/api/review', async (req, res) => {
  const { prUrl, focusArea } = req.body;
  
  const result = await query({
    prompt: `审查 PR ${prUrl}，重点关注：${focusArea}`,
    options: { maxTurns: 10 }
  });
  
  res.json({ review: result.output });
});

app.listen(3000);
// 现在开发者可以通过内部工具对任意 PR 进行 AI 审查
```

### 案例2：批量处理文件内容

```python
import asyncio
import glob
from claude_code_sdk import query, ClaudeCodeOptions

async def translate_docs():
    md_files = glob.glob("docs/*.md")
    
    tasks = []
    for file in md_files:
        task = query(
            prompt=f"将 {file} 的内容翻译成英文，保展原始格式",
            options=ClaudeCodeOptions(max_turns=5)
        )
        tasks.append(task)
    
    # 并行处理所有文档
    results = await asyncio.gather(*tasks)
    print(f"共处理 {len(results)} 个文件")

asyncio.run(translate_docs())
```

---

<aside>
💻

**SDK 的功能**：不再只是用 Claude Code 完成单个任务——而是将 AI 能力嵌入到你的整个工作流和业务系统中。

</aside>

---

<aside>
⬅️

[← 上一章：GitHub CI/CD](%E7%AC%AC%E5%8D%81%E4%B8%80%E7%AB%A0%EF%BC%9AGitHub%20CI%20CD%20%E6%B7%B1%E5%BA%A6%E9%9B%86%E6%88%90%2083bccd493c2741469570af6c1c92f266.md)

</aside>

<aside>
➡️

[下一章：行业实战案例 →](%E7%AC%AC%E5%8D%81%E4%B8%89%E7%AB%A0%EF%BC%9A%E8%A1%8C%E4%B8%9A%E5%AE%9E%E6%88%98%E6%A1%88%E4%BE%8B%208e362cc210bf4c249e73361bc232b625.md)

</aside>