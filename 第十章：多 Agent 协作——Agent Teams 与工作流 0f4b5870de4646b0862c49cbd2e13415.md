# 第十章：多 Agent 协作——Agent Teams 与工作流

# 多 Agent 协作——Agent Teams 与工作流 🟡

> **核心价值**：将多个 Claude Code 实例组成团队，并行处理复杂任务，速度提升 3-10x。
> 

---

## 10.1 为什么需要多 Agent？

**单个 Agent 的局限性：**

- 上下文窗口有限（超出 1M token 后迅速下降）
- 单线程执行，无法并行
- 复杂任务容易过长导致死机

**多 Agent 的三大价値：**

1. **并行化**：同时处理互不影响的子任务
2. **专业化**：不同 Agent 专注不同领域
3. **隔离化**：一个 Agent 失败不影响其他

---

## 10.2 Agent Teams —— 2026 年正式功能

Agent Teams 是 Claude Code **v2.1.x** 引入的官方多 Agent 协作框架。

### 创建一个 Agent Team

```bash
# 在项目目录中创建团队配置
cat > .claude/team.json << 'EOF'
{
  "team": [
    {
      "name": "architect",
      "role": "技术架构师",
      "model": "claude-opus-4-5",
      "responsibilities": ["系统设计", "技术选型", "代码审查"]
    },
    {
      "name": "frontend",
      "role": "前端开发",
      "model": "claude-sonnet-4-5",
      "responsibilities": ["React组件", "CSS样式", "用户交互"]
    },
    {
      "name": "backend",
      "role": "后端开发",
      "model": "claude-sonnet-4-5",
      "responsibilities": ["API设计", "数据库", "业务逻辑"]
    },
    {
      "name": "qa",
      "role": "测试工程师",
      "model": "claude-haiku-4-5",
      "responsibilities": ["单元测试", "集成测试", "性能测试"]
    }
  ]
}
EOF
```

### 启动 Agent Team

```bash
# 启动整个团队
claude --team .claude/team.json

# 指定主导实例
claude --team .claude/team.json --orchestrator architect

# 使用示例
claude --team .claude/team.json \
  "实现用户登录功能：前端页面 + 后端 API + 单元测试"
```

---

## 10.3 手动并行模式（Git Worktrees）

对于更精细的控制，可以手动使用 Git Worktrees 模実多 Agent：

```bash
# 创建多个工作树
# 每个工作树 = 独立的工作目录 + 分支
git worktree add ../feature-auth feature/auth
git worktree add ../feature-payment feature/payment
git worktree add ../bugfix-login bugfix/login

# 然后在不同终端窗口，各自运行 Claude Code
# 终端 1：
cd ../feature-auth && claude "实现用户登录功能"

# 终端 2：
cd ../feature-payment && claude "实现支付模块"

# 终端 3：
cd ../bugfix-login && claude "修复登录页面返回 500 错误"
```

---

## 10.4 套娃五步法——大型项目核心方法论

这是针对大型项目常用的工作方法：

```
第一步：刨廿（Decompose）
   “把整个任务拆分为 5-8 个可并行子任务”

第二步：分配（Assign）
   “将子任务分配给不同的 Agent，分配时注明依赖关系”

第三步：并行（Parallelize）
   “所有无依赖的任务并行开始”

第四步：同步（Sync）
   “有依赖的任务等待前置完成再继续”

第五步：整合（Integrate）
   “汇总所有结果，处理冲突和集成问题”
```

**实际提示示例：**

```
我要开发一个电商网站。请按套娃五步法进行：

1. 先将任务拆分为可并行的子任务
2. 先并行开发「商品列表页」和「购物车模块」
3. 等这两个完成后，再开发「结账页面」（依赖前两者）
4. 最后集成并全面测试
```

---

## 10.5 实用 Agent 配置模式

### 模式一：主导-属官模式（Orchestrator-Worker）

```
主导 Agent（Orchestrator）
    ├── 负责拆分任务、分配资源、整合结果
    ├── 属官 Agent A（前端）
    ├── 属官 Agent B（后端）
    └── 属官 Agent C（测试）
```

### 模式二：对等协作模式（Peer Collaboration）

```
共享任务队列（Git 仓库/共享文件）
    ├── Agent A 领取任务，修改上锁
    ├── Agent B 领取任务，修改上锁
    └── Agent C 领取任务，修改上锁
最终 merge 所有修改
```

### 模式三：流水线模式（Pipeline）

```
Agent A（需求分析）
    ↓
    输出: requirements.md
    ↓
Agent B（架构设计）
    ↓
    输出: architecture.md
    ↓
Agent C（代码实现）
    ↓
    输出: src/
    ↓
Agent D（测试骨架）
    ↓
    输出: tests/
```

---

## 🔬 实战案例

### 案例1： 3 个 Agent 并行开发功能模块

```bash
# 创建 3 个 worktree
git worktree add ../feat-auth feature/auth-module
git worktree add ../feat-search feature/search-module
git worktree add ../feat-cart feature/cart-module

# 【终端 1】
cd ../feat-auth
claude "实现用户登录注册功能，包括JWT认证和密码加密"

# 【终端 2】
cd ../feat-search
claude "实现商品搜索功能，支持模糊搜索和筛选"

# 【终端 3】
cd ../feat-cart
claude "实现购物车功能，包括添加、删除、数量修改和算价"

# 所有 Agent 并行工作！

# 完成后合并
git worktree remove ../feat-auth
git worktree remove ../feat-search
git worktree remove ../feat-cart
git merge feature/auth-module feature/search-module feature/cart-module
```

### 案例2：南定大型文档并行生成

```bash
# 主 Agent 负责分配
# Agent 1：
cd /tmp/doc-chapter1 && claude "编写 API 认证章节文档"

# Agent 2：
cd /tmp/doc-chapter2 && claude "编写 数据库设计章节文档"

# Agent 3：
cd /tmp/doc-chapter3 && claude "编写 部署指南章节文档"

# Agent 4：
cd /tmp/doc-chapter4 && claude "编写 监控与日志章节文档"

# 最后合并
cat /tmp/doc-chapter*/output.md > complete-documentation.md
```

---

<aside>
🤝

**多 Agent 的力量**：把需要一周才能完成的项目，缩短为一天。即使你尚未使用官方 Agent Teams，以 Git Worktrees + 多个终端窗口也能达到近似的效果。

</aside>

---

<aside>
⬅️

[← 上一章：Computer Use](%E7%AC%AC%E4%B9%9D%E7%AB%A0%EF%BC%9AComputer%20Use%E2%80%94%E2%80%94%E8%AE%A9%20AI%20%E6%93%8D%E4%BD%9C%E4%BD%A0%E7%9A%84%E7%94%B5%E8%84%91%208dfe61c4a95d4e22b4ac307bd6445b61.md)

</aside>

<aside>
➡️

[下一章：GitHub CI/CD →](%E7%AC%AC%E5%8D%81%E4%B8%80%E7%AB%A0%EF%BC%9AGitHub%20CI%20CD%20%E6%B7%B1%E5%BA%A6%E9%9B%86%E6%88%90%2083bccd493c2741469570af6c1c92f266.md)

</aside>