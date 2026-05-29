# Claude Code + MiMo 兼容性修复

## 问题

Claude Code 2.1.154+ 将 system prompt 放入 `messages[]` 数组（`role: "system"`），但 MiMo 的 Anthropic 兼容端点不接受这种格式，返回 400 错误。

```
messages[1].role must be either 'user' or 'assistant', but got 'system'
```

## 快速修复（3步）

### 1. 配置环境变量

在 `~/.zshrc` 中添加：

```bash
export MIMO_API_KEY="your-mimo-api-key"
export ANTHROPIC_BASE_URL="http://127.0.0.1:4567"
export ANTHROPIC_AUTH_TOKEN="$MIMO_API_KEY"
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

查看 [SKILL.md](./SKILL.md) 获取完整配置指南、故障排除和测试方法。
