# Multi-Agent GUI Automation System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![LangChain](https://img.shields.io/badge/Framework-LangChain-1C3C3C?logo=langchain)](https://www.langchain.com/)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/downloads/)

> **基于 LangChain 与多模态大模型的分层多智能体 GUI 自动化框架**  
> 通过"规划 - 执行"分离架构，实现复杂桌面/Web 任务的自主协同完成。

## 🌟 核心特性

- **🤖 多智能体协同**: 采用 Planner-Worker 主从架构，Planner 负责全局规划与任务分解，多个专用 Worker (Navigator, Operator, Verifier) 负责具体执行。
- **🧠 多模态驱动**: 集成 Qwen-VL / LLaVA / GPT-4V 等视觉语言模型 (VLM)，统一处理屏幕截图与自然语言指令。
- **🔗 LangGraph 编排**: 基于 LangGraph 构建状态机工作流，支持动态重规划、循环控制与人机回环 (Human-in-the-loop)。
- **📚 记忆增强系统**: 结合向量数据库 (Chroma/Milvus) 实现 RAG，支持历史经验检索与 Few-Shot 学习。
- **🛡️ 安全沙箱**: 内置动作校验与速率限制机制，确保自动化操作的安全性。
- **🔍 可解释性**: 完整的思维链 (CoT) 记录与可视化调试界面，支持任务回溯。

## 🏗️ 系统架构

```
┌─────────────────────────────────────────────────────────────┐
│                    User Interface Layer                     │
│  (CLI / Web Dashboard / API Gateway)                        │
└─────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Orchestrator (LangGraph)                   │
│  - State Management | Cycle Control | HIL Intervention      │
└─────────────────────────────────────────────────────────────┘
                             │
           ┌─────────────────┼─────────────────┐
           ▼                 ▼                 ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│  Planner Agent   │ │  Worker Agents   │ │  Memory System   │
│  (Task Planning) │ │  (Execution)     │ │  (RAG + VectorDB)│
└──────────────────┘ └──────────────────┘ └──────────────────┘
           │                 │
           └────────┬────────┘
                    ▼
┌─────────────────────────────────────────────────────────────┐
│               Multimodal Model Layer (VLM)                  │
│  Input: [Screen, Instruction] → Output: [Action, CoT]       │
└─────────────────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│                Execution Environment Layer                  │
│  Playwright (Web) | PyAutoGUI (OS) | Appium (Mobile)        │
└─────────────────────────────────────────────────────────────┘
```

## 🚀 快速开始

### 环境要求

- Python 3.10+
- Node.js 18+ (可选，用于某些 UI 工具)
- GPU (推荐 NVIDIA, 用于本地 VLM 推理)

### 安装步骤

```bash
# 克隆项目
git clone https://github.com/your-org/gui-agent-multi.git
cd gui-agent-multi

# 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装依赖
pip install -r requirements.txt

# 配置环境变量
cp .env.example .env
# 编辑 .env 文件，填入 LLM API Key 等配置
```

### 运行示例

```bash
# 运行简单任务：打开浏览器并搜索
python main.py --task "Open Chrome and search for 'LangChain multi-agent'"

# 启动交互式 CLI
python main.py --interactive

# 运行测试套件
pytest tests/
```

## 📂 项目结构

```
gui-agent-multi/
├── agents/                 # Agent 定义
│   ├── planner.py          # 主规划 Agent
│   └── workers/            # 子执行 Agent (Navigator, Operator, Verifier)
├── brain/                  # 模型与推理核心
│   ├── multimodal_llm.py   # VLM 封装
│   └── prompts/            # Prompt 模板
├── memory/                 # 记忆系统 (RAG, VectorDB)
├── tools/                  # 原子工具 (UI Actions, OCR)
├── orchestrator/           # LangGraph 编排逻辑
├── environments/           # 测试环境与沙箱
├── configs/                # 配置文件
├── main.py                 # 入口程序
└── README.md
```

## 🤖 Agent 角色说明

| Agent | 职责 | 能力 |
| :--- | :--- | :--- |
| **Planner** | 全局规划 | 任务解析、DAG 生成、动态重规划、异常协调 |
| **Navigator** | 界面探索 | 元素定位、页面跳转、DOM 树分析 |
| **Operator** | 动作执行 | 点击、输入、拖拽、滚动、快捷键 |
| **Verifier** | 结果校验 | 成功判定、错误检测、截图对比 |
| **Memory Keeper** | 知识管理 | 历史检索、Few-Shot 示例管理 |

## 🛠️ 技术栈

- **核心框架**: LangChain + LangGraph
- **多模态模型**: Qwen-VL-Chat, LLaVA-1.6, GPT-4V, Claude 3.5 Sonnet
- **LLM 基座**: Qwen-2.5, Llama-3
- **向量数据库**: ChromaDB, Milvus
- **自动化工具**: Playwright, PyAutoGUI, Appium
- **微调框架**: HuggingFace Transformers + PEFT (LoRA)

## 📅 开发路线图

- **Phase 1** (Weeks 1-3): 基础框架与单智能体原型
- **Phase 2** (Weeks 4-6): 多智能体协同与状态管理
- **Phase 3** (Weeks 7-9): 记忆系统与领域微调
- **Phase 4** (Weeks 10-12): 性能优化与产品化

详细计划请参阅 [PROJECT_OUTLINE.md](./PROJECT_OUTLINE.md)。

## 📄 许可证

本项目采用 MIT 许可证。详见 [LICENSE](./LICENSE) 文件。

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！请阅读 [CONTRIBUTING.md](./CONTRIBUTING.md) 了解详细信息。

## 📬 联系方式

- 项目主页: [GitHub Repository](https://github.com/your-org/gui-agent-multi)
- 讨论区: [GitHub Discussions](https://github.com/your-org/gui-agent-multi/discussions)
