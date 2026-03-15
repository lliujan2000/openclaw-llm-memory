# 🦞 openclaw-llm-memory

**打造基于大模型的 OpenClaw 长期记忆系统**

让你的龙虾变成 **记忆分析大师**。

为 OpenClaw 提供：

- 🧠 长期记忆
- 📚 Markdown 知识库
- 🔎 向量检索
- 🔀 Hybrid Search（向量 + 文本）
- ⏳ 时间衰减排序
- 🗂 Session Memory
- 🧾 自动记忆沉淀
- 🧠 Obsidian 知识库融合

---

# ✨ 功能特点

| 功能 | 说明 |
|----|----|
| Local Embedding | 本地向量模型 |
| Markdown Memory | Markdown 记忆系统 |
| Hybrid Search | 向量 + 文本混合搜索 |
| Session Memory | 会话记忆 |
| Memory Compaction | 自动记忆压缩 |
| Temporal Ranking | 时间衰减排序 |
| Obsidian Integration | 接入 Obsidian 笔记 |

---

# 🧠 系统架构

```
OpenClaw
   │
   ▼
MemorySearch
   │
   ▼
QMD Index
   │
   ▼
Vector Embedding
   │
   ▼
Markdown Knowledge Base
```

---

# 🚀 一、安装依赖

系统环境：

```
macOS
```

---

## 1 安装 node-llama-cpp

用于运行本地 embedding 模型。

```bash
npm install -g node-llama-cpp
```

---

## 2 安装 sqlite

```bash
brew install sqlite
```

---

## 3 安装 qmd

qmd 用于 Markdown 向量索引。

```bash
npm install -g @tobilu/qmd
```

---

# ⚙️ 二、配置 OpenClaw State 目录

使用与 OpenClaw 相同的状态目录：

```bash
STATE_DIR="${OPENCLAW_STATE_DIR:-$HOME/.openclaw}"

export XDG_CONFIG_HOME="$STATE_DIR/agents/main/qmd/xdg-config"
export XDG_CACHE_HOME="$STATE_DIR/agents/main/qmd/xdg-cache"
```

---

# 🔄 三、初始化索引

强制刷新索引：

```bash
qmd update
qmd embed
```

---

# 📥 四、触发模型下载

第一次查询会自动下载 embedding 模型。

```bash
qmd query "test query" -c memory-root --json >/dev/null 2>&1
```

---

# ⚙️ 五、OpenClaw Agent 配置

修改：

```
openclaw.json
```

Agent 配置：

```json
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
```

---

# 🔎 Memory Search 配置

```json
"memorySearch": {
  "sources": [
    "memory",
    "sessions"
  ],
  "experimental": {
    "sessionMemory": true
  },
  "provider": "local",
```

---

## 本地 Embedding 模型

```json
"local": {
  "modelPath": "~/.node-llama-cpp/models/hf_Qwen_Qwen3-Embedding-0.6B-Q8_0.gguf"
}
```

---

## 向量存储

```json
"store": {
  "vector": {
    "enabled": true
  }
}
```

---

# 🔀 Hybrid Search

```json
"query": {
  "hybrid": {
    "enabled": true,
    "vectorWeight": 0.7,
    "textWeight": 0.3,
    "candidateMultiplier": 4,
```

---

# 🧠 MMR 去重

```json
"mmr": {
  "enabled": true,
  "lambda": 0.7
}
```

---

# ⏳ 时间衰减排序

```json
"temporalDecay": {
  "enabled": true,
  "halfLifeDays": 30
}
```

---

# 📦 Memory Compaction

避免上下文过长：

```json
"compaction": {
  "mode": "safeguard",
  "reserveTokensFloor": 20000,
```

---

# 🧾 Memory Flush

自动写入长期记忆：

```json
"memoryFlush": {
  "enabled": true,
  "softThresholdTokens": 4000,
  "prompt": "Write any lasting notes to memory/YYYY-MM-DD.md; reply with NO_REPLY if nothing to store.",
  "systemPrompt": "Session nearing compaction. Store durable memories now."
}
```

---

# 📚 六、Memory 配置

使用 **qmd** 作为后端。

```json
"memory": {
  "backend": "qmd",
  "citations": "auto",
```

---

## QMD 配置

```json
"qmd": {
  "includeDefaultMemory": true,
```

---

# 📂 Markdown 知识库

可以直接接入 **Obsidian**：

```json
"paths": [
  {
    "path": "~/obsidian/notes",
    "name": "notes",
    "pattern": "**/*.md"
  }
]
```

---

# 🔄 自动更新索引

```json
"update": {
  "interval": "5m",
  "debounceMs": 15000,
  "waitForBootSync": true
}
```

---

# 🔒 Memory Scope

控制哪些聊天可以访问记忆：

```json
"scope": {
  "default": "deny",
  "rules": [
```

允许 Direct Chat：

```json
{
  "action": "allow",
  "match": {
    "chatType": "direct"
  }
}
```

---

# 🎯 最终效果

配置完成后，OpenClaw 将具备：

- 🧠 长期记忆
- 📚 Markdown 知识库
- 🔎 向量检索
- 🔀 Hybrid Search
- 🧾 自动知识沉淀
- ⏳ 时间衰减排序
- 🗂 Session Memory

最终效果：

**让 OpenClaw 成为真正拥有长期记忆的 AI Agent。**

---

# ⭐ 如果这个项目对你有帮助

请给个 **Star ⭐**

```
GitHub ⭐ = 项目持续更新的动力
```

---

# 📜 License

MIT License

```
Free for personal and commercial use
Jason.liu
```
