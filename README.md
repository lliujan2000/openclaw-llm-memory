openclaw-llm-memory

打造基于大模型的 OpenClaw 记忆系统 —— 让你的龙虾变成记忆分析大师 🦞

本文介绍如何为 OpenClaw 构建一个基于本地向量数据库与大模型 Embedding 的长期记忆系统，使 AI Agent 具备：

长期记忆

Markdown 知识库

Session 记忆

混合搜索（向量 + 文本）

时间衰减排序

自动记忆压缩

Obsidian 知识库集成

技术栈：

OpenClaw

qmd

node-llama-cpp

sqlite

Qwen Embedding

一、安装依赖

系统：macOS

1、安装 node-llama-cpp

用于运行本地 embedding 模型。

npm install -g node-llama-cpp
2、安装 sqlite

用于本地数据存储。

brew install sqlite
3、安装 qmd

qmd 用于构建 Markdown 记忆索引。

npm install -g @tobilu/qmd
4、设置 OpenClaw State 目录

使用与 OpenClaw 相同的状态目录。

STATE_DIR="${OPENCLAW_STATE_DIR:-$HOME/.openclaw}"

export XDG_CONFIG_HOME="$STATE_DIR/agents/main/qmd/xdg-config"
export XDG_CACHE_HOME="$STATE_DIR/agents/main/qmd/xdg-cache"
5、（可选）强制刷新索引 + Embedding
qmd update
qmd embed
6、触发模型首次下载

首次查询时会自动下载 embedding 模型。

qmd query "test query" -c memory-root --json >/dev/null 2>&1

执行完成后系统会自动生成：

~/.openclaw/

相关缓存与索引。

二、OpenClaw 配置

需要修改 openclaw.json。

1、Agent 配置
"agents": {
  "defaults": {
    "model": {
      "primary": "xx"
    },
    "models": {
      "xxx": {
        "alias": "xxx"
      },
      "xxx": {
        "alias": "xxx"
      }
    },
    "workspace": "/Users/xxx/.openclaw/workspace",
Memory Search 配置
"memorySearch": {
  "sources": [
    "memory",
    "sessions"
  ],
  "experimental": {
    "sessionMemory": true
  },
  "provider": "local",
本地 Embedding 模型
"local": {
  "modelPath": "~/.node-llama-cpp/models/hf_Qwen_Qwen3-Embedding-0.6B-Q8_0.gguf"
}
向量存储
"store": {
  "vector": {
    "enabled": true
  }
}
Hybrid 混合搜索
"query": {
  "hybrid": {
    "enabled": true,
    "vectorWeight": 0.7,
    "textWeight": 0.3,
    "candidateMultiplier": 4,
MMR 去重
"mmr": {
  "enabled": true,
  "lambda": 0.7
}
时间衰减排序
"temporalDecay": {
  "enabled": true,
  "halfLifeDays": 30
}
Compaction（记忆压缩）

避免上下文过长。

"compaction": {
  "mode": "safeguard",
  "reserveTokensFloor": 20000,
Memory Flush

自动将重要信息写入记忆。

"memoryFlush": {
  "enabled": true,
  "softThresholdTokens": 4000,
  "prompt": "Write any lasting notes to memory/YYYY-MM-DD.md; reply with NO_REPLY if nothing to store.",
  "systemPrompt": "Session nearing compaction. Store durable memories now."
}
其他配置
"timeoutSeconds": 3600,
"maxConcurrent": 4,
"subagents": {
  "maxConcurrent": 8
}
三、Memory 配置

Memory 使用 qmd 作为后端。

"memory": {
  "backend": "qmd",
  "citations": "auto",
QMD 配置
"qmd": {
  "includeDefaultMemory": true,
Markdown 知识库路径

支持接入 Obsidian 笔记库。

"paths": [
  {
    "path": "~/obsidian/notes",
    "name": "notes",
    "pattern": "**/*.md"
  }
]
自动更新索引
"update": {
  "interval": "5m",
  "debounceMs": 15000,
  "waitForBootSync": true
}
查询限制
"limits": {
  "maxResults": 6,
  "timeoutMs": 4000
}
Memory Scope

控制哪些聊天可以访问记忆。

"scope": {
  "default": "deny",
  "rules": [
允许 Direct Chat
{
  "action": "allow",
  "match": {
    "chatType": "direct"
  }
}
Discord Channel
{
  "action": "allow",
  "match": {
    "rawKeyPrefix": "agent:main:discord:channel"
  }
}
Agent 001
{
  "action": "allow",
  "match": {
    "rawKeyPrefix": "agent:001:discord:channel"
  }
}
Agent 002
{
  "action": "allow",
  "match": {
    "rawKeyPrefix": "agent:002:discord:channel"
  }
}
四、最终效果

完成配置后，OpenClaw 将具备：

本地 Embedding 检索

Markdown 知识库

Session 长期记忆

向量 + 文本混合搜索

自动知识沉淀

Obsidian 知识库融合

记忆时间衰减排序

最终效果：

让 OpenClaw 成为真正拥有长期记忆的 AI Agent。
