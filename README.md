# Claude Code 第三方 API 兼容性修复

## 问题

Claude Code 2.1.154+ 将 system prompt 放入 `messages[]` 数组（`role: "system"`），但大多数第三方 Anthropic 兼容 API 不接受这种格式，返回 400 错误。

```
messages[1].role must be either 'user' or 'assistant', but got 'system'
```

**影响范围：** MiMo、DeepSeek、智谱等所有通过 `ANTHROPIC_BASE_URL` 接入的第三方 API。

## 快速修复（3步）

### 1. 配置环境变量

在 `~/.zshrc` 中添加：

```bash
# API 密钥（替换为你的实际密钥）
export MIMO_API_KEY="your-api-key"

# 指向本地代理
export ANTHROPIC_BASE_URL="http://127.0.0.1:4567"
export ANTHROPIC_AUTH_TOKEN="$MIMO_API_KEY"

# 模型配置（替换为你的实际模型）
export ANTHROPIC_MODEL="mimo-v2.5-pro"
export ANTHROPIC_DEFAULT_OPUS_MODEL="mimo-v2.5-pro"
export ANTHROPIC_DEFAULT_SONNET_MODEL="mimo-v2.5-pro"
export ANTHROPIC_DEFAULT_HAIKU_MODEL="mimo-v2.5-pro"
export CLAUDE_CODE_SUBAGENT_MODEL="mimo-v2.5-pro"
```

### 2. 启动代理

```bash
node ~/.claude/claude-mimo-proxy.js &
```

### 3. 重启 Claude Code

```bash
source ~/.zshrc
claude
```

## 验证

```bash
# 测试代理
curl -s http://127.0.0.1:4567

# 测试 API
curl -s http://127.0.0.1:4567/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: $MIMO_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "mimo-v2.5-pro",
    "max_tokens": 100,
    "messages": [
      {"role": "system", "content": "你是一个助手"},
      {"role": "user", "content": "你好"}
    ]
  }'
```

## 详细文档

查看claude-code-third-party-api-fix获取完整配置指南、故障排除和测试方法。
