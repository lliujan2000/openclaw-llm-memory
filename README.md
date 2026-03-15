# openclaw-llm-memory
打造基于大模型的OpenClaw记忆系统-让你的龙虾变成记忆分析大师

 一、安装依赖

 # on mac
#install node-ollama-cpp
npm install -g node-llama-cpp

# sqlite
brew install sqlite
# qmd
npm install -g @tobilu/qmd
# Pick the same state dir OpenClaw uses
STATE_DIR="${OPENCLAW_STATE_DIR:-$HOME/.openclaw}"

export XDG_CONFIG_HOME="$STATE_DIR/agents/main/qmd/xdg-config"
export XDG_CACHE_HOME="$STATE_DIR/agents/main/qmd/xdg-cache"

# (Optional) force an index refresh + embeddings
qmd update
qmd embed

# Warm up / trigger first-time model downloads
qmd query "test query" -c memory-root --json >/dev/null 2>&1


二、openclaw.json 配置
 Agent部分配置
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
      "memorySearch": {
        "sources": [
          "memory",
          "sessions"
        ],
        "experimental": {
          "sessionMemory": true
        },
        "provider": "local",
        "local": {
          "modelPath": "~/.node-llama-cpp/models/hf_Qwen_Qwen3-Embedding-0.6B-Q8_0.gguf"
        },
        "store": {
          "vector": {
            "enabled": true
          }
        },
        "query": {
          "hybrid": {
            "enabled": true,
            "vectorWeight": 0.7,
            "textWeight": 0.3,
            "candidateMultiplier": 4,
            "mmr": {
              "enabled": true,
              "lambda": 0.7
            },
            "temporalDecay": {
              "enabled": true,
              "halfLifeDays": 30
            }
          }
        }
      },
      "compaction": {
        "mode": "safeguard",
        "reserveTokensFloor": 20000,
        "memoryFlush": {
          "enabled": true,
          "softThresholdTokens": 4000,
          "prompt": "Write any lasting notes to memory/YYYY-MM-DD.md; reply with NO_REPLY if nothing to store.",
          "systemPrompt": "Session nearing compaction. Store durable memories now."
        }
      },
      "timeoutSeconds": 3600,
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  },

  Memory 配置：
"memory": {
    "backend": "qmd",
    "citations": "auto",
    "qmd": {
      "includeDefaultMemory": true,
      "paths": [
        {
          "path": "~/obsidian/notes",
          "name": "notes",
          "pattern": "**/*.md"
        }
      ],
      "update": {
        "interval": "5m",
        "debounceMs": 15000,
        "waitForBootSync": true
      },
      "limits": {
        "maxResults": 6,
        "timeoutMs": 4000
      },
      "scope": {
        "default": "deny",
        "rules": [
          {
            "action": "allow",
            "match": {
              "chatType": "direct"
            }
          },
          {
            "action": "allow",
            "match": {
              "rawKeyPrefix": "agent:main:discord:channel"
            }
          },
          {
            "action": "allow",
            "match": {
              "rawKeyPrefix": "agent:001:discord:channel"
            }
          },
          {
            "action": "allow",
            "match": {
              "rawKeyPrefix": "agent:002:discord:channel"
            }
          }
        ]
      }
    }
  }
  

 





