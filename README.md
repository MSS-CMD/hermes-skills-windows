# Hermes Skills for Windows

精选 Hermes Agent 技能包，**专为 Windows 用户整理**。

## 安装方法

```bash
# 方案一：手动复制
git clone https://github.com/xiaoshuaizi/hermes-skills-windows.git
cp -r hermes-skills-windows/skills/* ~/.hermes/skills/
# 然后重新启动 Hermes 生效

# 方案二：直接在 Hermes 中加载
# 克隆后，在 Hermes 会话中用 /reload-skills 扫描
```

## 技能清单

### 🔧 Hermes 核心
| 技能 | 说明 |
|------|------|
| `hermes-agent` | Hermes 完整命令参考、配置、网关、安全设置 |
| `hermes-web-ui` | Web 管理面板（端口 8648），密码重置、登录解锁 |
| `native-mcp` | MCP 服务器对接（stdio/HTTP），自动发现工具 |
| `computer-use` | Windows 桌面自动化——点击、打字、拖拽 |

### 💻 开发工作流
| 技能 | 说明 |
|------|------|
| `plan` | 写可执行计划，不执行 |
| `systematic-debugging` | 4 阶段根因调试法 |
| `test-driven-development` | RED-GREEN-REFACTOR 强制 |
| `subagent-driven-development` | 子代理执行计划（2 轮审查） |
| `requesting-code-review` | 提交前安全扫描 + 质量门禁 |
| `simplify-code` | 并行 3 代理清理代码改动 |
| `spike` | 快速实验验证想法 |
| `github` | GitHub 全套操作（gh CLI + git） |
| `claude-code` | 委托 Claude Code CLI 写代码 |
| `python-windows` | Windows 专属 Python 环境配置 |

### 🎨 创意 & 可视化
| 技能 | 说明 |
|------|------|
| `markdown-viewer` | 丰富的图表/数据可视化/架构图 |
| `architecture-diagram` | 暗色 SVG 架构图 |
| `excalidraw` | 手绘风架构/流程图 |
| `humanizer` | 去 AI 味，还原真实语气 |
| `claude-design` | 一次性 HTML 页面/原型 |
| `p5js` | 生成艺术/交互/3D |
| `pixel-art` | NES/Game Boy/PICO-8 像素风 |
| `ascii-art` | pyfiglet/cowsay/boxes 字符画 |
| `ascii-video` | 彩色 ASCII MP4/GIF 视频 |
| `design-md` | Google DESIGN.md 规范文件 |
| `manim-video` | 3Blue1Brown 风格数学/算法动画 |

### 📹 视频创作
| 技能 | 说明 |
|------|------|
| `hyperframes` | HTML/CSS/JS 合成 AI 视频 |
| `remotion` | React 视频工程 |
| `code-video-creation` | 编程式视频创建 |

### 📝 效率工具
| 技能 | 说明 |
|------|------|
| `notion` | Notion API + ntn CLI |
| `obsidian` | Obsidian 笔记读取/搜索/创建 |
| `google-workspace` | Gmail/Calendar/Drive/Docs/Sheets |
| `maps` | 地理编码/POI/路线（OpenStreetMap） |
| `airtable` | Airtable REST API |
| `linear` | Linear 项目管理 |
| `nano-pdf` | 自然语言编辑 PDF |
| `ocr-and-documents` | PDF/扫描件文字提取 |
| `powerpoint` | PPT 创建/读取/编辑 |

### 📊 数据科学
| 技能 | 说明 |
|------|------|
| `jupyter-live-kernel` | 持久 Jupyter 内核迭代 Python |

### 🎵 媒体
| 技能 | 说明 |
|------|------|
| `youtube-content` | YouTube 字幕转摘要/帖子 |
| `ai-music-and-audio` | AI 音乐生成/音频可视化 |
| `spotify` | Spotify 播放/搜索/歌单管理 |
| `gif-search` | GIF 搜索下载 |

### 📚 研究
| 技能 | 说明 |
|------|------|
| `arxiv` | 按关键词/作者/分类查论文 |
| `blogwatcher` | RSS/Atom 订阅监控 |
| `llm-wiki` | Karpathy 风格互联 Wiki 知识库 |

### 🤖 ML/AI
| 技能 | 说明 |
|------|------|
| `huggingface-hub` | HuggingFace 模型/数据集搜索下载 |
| `llama-cpp` | 本地 GGUF 推理 + HF 模型发现 |
| `dspy` | 声明式 LM 编程，自动优化提示词 |
| `weights-and-biases` | ML 实验追踪 |
| `serving-llms-vllm` | 高吞吐 LLM 服务 |
| `evaluating-llms-harness` | lm-eval-harness 模型评测 |
| `segment-anything-model` | SAM 零样本图像分割 |

### 🔄 自动化
| 技能 | 说明 |
|------|------|
| `webhook-subscriptions` | Webhook 事件驱动代理运行 |
| `kanban-workflow` | 多代理 Kanban 协作板 |
| `hermes-dojo` | 持续自我改进系统 |

### 🏠 其他
| 技能 | 说明 |
|------|------|
| `openhue` | Philips Hue 灯光控制 |
| `xurl` | X/Twitter 发帖/搜索/私信 |
| `himalaya` | 终端邮件（IMAP/SMTP） |
| `petdex` | 安装选择 Hermes 桌面宠物 |

## Windows 注意事项

### 安装 Hermes
```bash
# 下载安装脚本（在 PowerShell / Windows Terminal 中运行）
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

### 路径
- Hermes 配置：`~/.hermes/config.yaml`
- API 密钥：`~/.hermes/.env`
- 技能目录：`~/.hermes/skills/`

### 已知问题
- `Alt+Enter` 不换行 → 用 `Ctrl+Enter` 代替
- config.yaml 不要用记事本编辑（会有 BOM 问题）
- 如果遇到 `WinError 10106`，重启 Hermes 即可
