# 附录C：国产模型配置速查

# 附录C：国产模型配置速查

> 更新时间：2026年3月27日
> 

---

## DeepSeek（深度求索）

```bash
# API 申请：https://platform.deepseek.com/
export ANTHROPIC_API_KEY="你的 DeepSeek API Key"
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"

# 推荐模型
export CLAUDE_MODEL="deepseek-chat"      # DeepSeek-V3.2（1M ctx，性价比之王）
export CLAUDE_MODEL="deepseek-reasoner"  # DeepSeek-R1（推理增强）
```

**特点：** 价格极低、代码能力强、1M 长上下文、国内速度快

---

## Kimi（月之暗面）

```bash
# API 申请：https://platform.moonshot.cn/
export ANTHROPIC_API_KEY="你的 Moonshot API Key"
export ANTHROPIC_BASE_URL="https://api.moonshot.cn/anthropic"

# 推荐模型
export CLAUDE_MODEL="kimi-k2.5"          # 最新旗舰模型
```

**特点：** 长文档处理能力强、支持 128K 上下文、中文优秀

---

## 通义千问（阿里云）

```bash
# API 申请：https://dashscope.aliyun.com/
export ANTHROPIC_API_KEY="你的 DashScope API Key"
export ANTHROPIC_BASE_URL="https://dashscope.aliyuncs.com/apps/anthropic"

# 推荐模型
export CLAUDE_MODEL="qwen3.5-plus"       # 最新旗舰
export CLAUDE_MODEL="qwen3-coder-plus"   # 代码专用版本
```

**特点：** 代码能力强、多模态支持好、企业服务稳定

---

## 智谱 GLM（清华）

```bash
# API 申请：https://open.bigmodel.cn/
export ANTHROPIC_API_KEY="你的智谱 API Key"
export ANTHROPIC_BASE_URL="https://open.bigmodel.cn/api/anthropic"

# 推荐模型
export CLAUDE_MODEL="glm-5.1"            # 最新旗舰（2026年3月发布）
export CLAUDE_MODEL="glm-z1-airx"        # 推理增强版
export CLAUDE_MODEL="glm-4.7-flash"      # 免费版（有限制）
```

**特点：** 有免费额度、推理能力强、学术背景强

---

## MiniMax

```bash
# API 申请：https://api.minimaxi.chat/
export ANTHROPIC_API_KEY="你的 MiniMax API Key"
export ANTHROPIC_BASE_URL="https://api.minimaxi.chat/v1/anthropic"

# 推荐模型
export CLAUDE_MODEL="MiniMax-M2.7"       # 最新版本
```

**特点：** 多模态能力突出、语音合成集成好

---

## 一键切换脚本

```bash
# 保存到 ~/.claude/switch-model.sh
#!/bin/bash

case "$1" in
  deepseek)
    export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
    export ANTHROPIC_API_KEY="$DEEPSEEK_API_KEY"
    export CLAUDE_MODEL="deepseek-chat"
    echo "已切换到 DeepSeek V3.2"
    ;;
  kimi)
    export ANTHROPIC_BASE_URL="https://api.moonshot.cn/anthropic"
    export ANTHROPIC_API_KEY="$MOONSHOT_API_KEY"
    export CLAUDE_MODEL="kimi-k2.5"
    echo "已切换到 Kimi K2.5"
    ;;
  qwen)
    export ANTHROPIC_BASE_URL="https://dashscope.aliyuncs.com/apps/anthropic"
    export ANTHROPIC_API_KEY="$DASHSCOPE_API_KEY"
    export CLAUDE_MODEL="qwen3.5-plus"
    echo "已切换到 通义千问3.5"
    ;;
  glm)
    export ANTHROPIC_BASE_URL="https://open.bigmodel.cn/api/anthropic"
    export ANTHROPIC_API_KEY="$GLM_API_KEY"
    export CLAUDE_MODEL="glm-5.1"
    echo "已切换到 GLM-5.1"
    ;;
  claude)
    unset ANTHROPIC_BASE_URL
    export ANTHROPIC_API_KEY="$CLAUDE_API_KEY"
    export CLAUDE_MODEL="claude-sonnet-4-5"
    echo "已切换到 Claude Sonnet 4.5"
    ;;
  *)
    echo "用法: source switch-model.sh [deepseek|kimi|qwen|glm|claude]"
    ;;
esac

# 使用方式（注意要用 source）
# source ~/.claude/switch-model.sh deepseek
```

---

## 各模型对比速览

| 模型 | 代码能力 | 上下文 | 价格 | 推理 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| DeepSeek V3.2 | ⭐⭐⭐⭐⭐ | 1M | 极低 | 强 | 日常开发首选 |
| DeepSeek R1 | ⭐⭐⭐⭐ | 128K | 低 | 极强 | 复杂算法/数学 |
| Kimi K2.5 | ⭐⭐⭐⭐ | 128K | 中 | 强 | 长文档处理 |
| Qwen3-Coder | ⭐⭐⭐⭐⭐ | 1M | 中 | 强 | 代码专项任务 |
| GLM-5.1 | ⭐⭐⭐⭐ | 128K | 中低 | 强 | 推理+有免费额度 |
| Claude Sonnet 4.5 | ⭐⭐⭐⭐⭐ | 1M | 高 | 极强 | 最高质量任务 |

---

<aside>
⬅️

[← 附录 B：](%E9%99%84%E5%BD%95B%EF%BC%9ACLAUDE%20md%20%E6%A8%A1%E6%9D%BF%E5%BA%93%206a25a93ae1ff450aa2f691b01ce38ebb.md)[CLAUDE.md](http://CLAUDE.md) [模板库](%E9%99%84%E5%BD%95B%EF%BC%9ACLAUDE%20md%20%E6%A8%A1%E6%9D%BF%E5%BA%93%206a25a93ae1ff450aa2f691b01ce38ebb.md)

</aside>

<aside>
➡️

[附录 D：MCP 服务器大全 →](%E9%99%84%E5%BD%95D%EF%BC%9AMCP%20%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%A4%A7%E5%85%A8%2012c124fcde1d44938193dc23999a52b8.md)

</aside>