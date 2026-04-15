# 附录B：CLAUDE.md 模板库

# 附录B：[CLAUDE.md](http://CLAUDE.md) 模板库

## 模板1：Web 全栈项目

```markdown
# 项目：[项目名称]

## 技术栈
- 前端：React 18 + TypeScript + Tailwind CSS
- 后端：Node.js 20 + Express + PostgreSQL 15
- 部署：Docker + GitHub Actions

## 关键规范
- 所有函数必须有 JSDoc 注释
- API 响应统一格式：{ success, data, error }
- 数据库操作必须使用事务
- 禁止在代码中硬编码密钥

## 目录结构
- src/routes/    API 路由
- src/models/    数据库模型
- src/services/  业务逻辑
- src/utils/     工具函数

## 测试要求
- 每个 API 接口必须有单元测试
- 使用 Jest + Supertest
```

---

## 模板2：Python 数据分析项目

```markdown
# 项目：[数据分析项目名]

## 环境
- Python 3.11 + pandas + numpy + matplotlib
- Jupyter Notebook 作为主要开发环境

## 规范
- 变量命名：snake_case
- 所有函数必须有类型注解
- 数据文件统一存放在 data/ 目录
- 输出图表保存到 outputs/ 目录

## 数据源
- 原始数据：data/raw/
- 清洗后数据：data/processed/
- 分析结果：outputs/

## 禁止操作
- 不要修改 data/raw/ 中的原始数据
- 不要将数据文件提交到 git
```

---

## 模板3：前端组件库

```markdown
# 组件库：[名称]

## 技术栈
- React 18 + TypeScript
- Storybook 7 用于组件文档
- CSS Modules 用于样式

## 命名规范
- 组件文件：PascalCase（Button.tsx）
- Hook 文件：use 前缀（useModal.ts）
- 样式文件：与组件同名（Button.module.css）

## 组件结构
每个组件必须包含：
1. 组件文件（.tsx）
2. 类型定义（types.ts）
3. 测试文件（.test.tsx）
4. Story 文件（.stories.tsx）

## 设计系统
- 颜色变量在 src/tokens/colors.ts
- 间距使用 8px 网格系统
```

---

## 模板4：微服务项目

```markdown
# 微服务：[服务名称]

## 服务信息
- 端口：8080
- 职责：[一句话描述]
- 依赖服务：auth-service, user-service

## 技术栈
- Go 1.21 + Gin + gRPC
- PostgreSQL 15
- Redis 7

## API 规范
- RESTful API，版本前缀 /api/v1/
- 错误码统一使用 RFC 7807
- 所有接口需要 JWT 认证（除 /health）

## 开发规范
- 每个 handler 必须有对应的单元测试
- 数据库迁移使用 golang-migrate
- 日志使用结构化日志（zerolog）
```

---

## 模板5：AI/ML 项目

```markdown
# AI 项目：[名称]

## 环境
- Python 3.11 + PyTorch 2.x
- CUDA 12.x（如有 GPU）

## 项目结构
- data/         数据集
- models/       模型定义
- training/     训练脚本
- inference/    推理代码
- experiments/  实验记录

## 实验规范
- 每次实验必须记录到 experiments/ 并包含：配置、结果、时间
- 模型检查点保存到 checkpoints/，不提交 git
- 使用 wandb 或 MLflow 追踪实验

## 数据处理
- 原始数据不可修改
- 数据预处理脚本在 scripts/preprocess.py
- 数据版本使用 DVC 管理
```

---

<aside>
⬅️

[← 附录 A：命令速查表](%E9%99%84%E5%BD%95A%EF%BC%9A%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E8%A1%A8%20f356c56faaca4c1d918baa9a67b01c53.md)

</aside>

<aside>
➡️

[附录 C：国产模型配置速查 →](%E9%99%84%E5%BD%95C%EF%BC%9A%E5%9B%BD%E4%BA%A7%E6%A8%A1%E5%9E%8B%E9%85%8D%E7%BD%AE%E9%80%9F%E6%9F%A5%2049ddae6d286c466eae97a160ce58daea.md)

</aside>