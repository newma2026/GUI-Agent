# GUI-Agent 项目大纲 (多智能体协同版)

## 1. 项目概述
- **项目名称**: Multi-Agent GUI Automation System
- **核心愿景**: 基于 LangChain 构建分层多智能体系统，通过"规划 - 执行"分离架构，实现复杂 GUI 任务的自主协同完成。
- **关键特性**:
  - **主从架构**: 单一 Planner Agent 负责全局规划与任务分解，多个 Worker Agents 负责具体执行。
  - **动态协同**: 支持运行时动态分配子任务，Agent 间通过共享状态和消息传递进行协作。
  - **多模态驱动**: 集成 VLM (Vision-Language Model) 作为核心感知引擎，统一处理视觉与语言指令。
  - **可解释性**: 完整的思维链 (CoT) 记录与执行日志，支持任务回溯与调试。

## 2. 核心功能模块

### 2.1 主控智能体 (Planner Agent)
- **职责**: 任务解析、全局规划、子任务分解、进度监控、异常处理。
- **能力**:
  - 接收用户自然语言指令，结合屏幕截图进行上下文理解。
  - 生成结构化执行计划 (DAG 或步骤列表)。
  - 根据执行反馈动态调整计划 (Re-planning)。
  - 协调子 Agent 之间的依赖关系。

### 2.2 执行智能体群 (Worker Agents)
- **导航 Agent (Navigator)**: 负责界面探索、元素定位、页面跳转。
- **操作 Agent (Operator)**: 负责具体动作执行 (点击、输入、拖拽、滚动)。
- **验证 Agent (Verifier)**: 负责执行结果校验、错误检测、成功标准判定。
- **记忆 Agent (Memory Keeper)**: (可选) 负责维护长期记忆、检索历史经验、管理 RAG 知识库。

### 2.3 多模态感知引擎 (Multimodal Perception)
- **UI Grounding**: 将自然语言描述映射到屏幕坐标/元素 ID。
- **OCR 增强**: 提取界面文本信息，辅助语义理解。
- **状态编码**: 将截图转化为向量表示，供 Agent 推理使用。

### 2.4 协同通信机制 (Coordination Protocol)
- **消息总线**: 基于 LangChain Messages 实现 Agent 间标准化通信。
- **共享状态板 (Shared Blackboard)**: 存储当前 UI 状态、执行历史、变量上下文。
- **工具注册中心**: 统一管理所有 Agent 可调用的底层 API (PyAutoGUI, ADB, etc.)。

## 3. 技术架构

```text
┌─────────────────────────────────────────────────────────────┐
│                    User Interface Layer                     │
│  (CLI / Web Dashboard / API Gateway)                        │
└─────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Orchestrator (LangGraph)                   │
│  - State Management                                         │
│  - Cycle Control (Plan → Execute → Verify → Loop)           │
│  - Human-in-the-loop Intervention                           │
└─────────────────────────────────────────────────────────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
           ▼                 ▼                 ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│  Planner Agent   │ │  Worker Agents   │ │  Memory System   │
│  (LLM + CoT)     │ │  (Specialized)   │ │  (Vector DB)     │
│ - Task Decomposition│ │ - Navigator    │ │ - Episode Store  │
│ - Dependency Mgmt │ │ - Operator     │ │ - RAG Retrieval  │
│ - Error Recovery  │ │ - Verifier     │ │ - Few-Shot Examples│
└──────────────────┘ └──────────────────┘ └──────────────────┘
           │                 │
           └────────┬────────┘
                    ▼
┌─────────────────────────────────────────────────────────────┐
│               Multimodal Model Layer (VLM)                  │
│  (Qwen-VL / LLaVA / GPT-4V via LangChain Multimodal Chat)   │
│  - Input: [Screen Capture, Text Instruction, History]       │
│  - Output: [Action Dict, Reasoning Text, Coordinates]       │
└─────────────────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│                Execution Environment Layer                  │
│  - OS Automation (PyAutoGUI, AppleScript)                   │
│  - Mobile Automation (ADB, Appium)                          │
│  - Web Automation (Playwright, Selenium)                    │
│  - Safety Sandbox (Action Validation & Rate Limiting)       │
└─────────────────────────────────────────────────────────────┘
```

## 4. 开发路线图

### Phase 1: 基础框架与单智能体原型 (Weeks 1-3)
- [ ] 搭建 LangChain + LangGraph 基础骨架。
- [ ] 实现 Planner Agent 原型，支持简单任务拆解。
- [ ] 集成 VLM 模型 API，实现基础的 UI Grounding。
- [ ] 开发单一 Worker Agent (Operator)，支持点击/输入。
- [ ] 实现本地沙箱环境用于安全测试。

### Phase 2: 多智能体协同与状态管理 (Weeks 4-6)
- [ ] 实现 Navigator 和 Verifier Agent。
- [ ] 设计并实现共享状态板 (Blackboard) 机制。
- [ ] 开发 Agent 间通信协议 (基于 LangChain Messages)。
- [ ] 实现基于执行反馈的动态重规划 (Re-planning) 逻辑。
- [ ] 引入短期记忆 (Conversation Buffer) 支持多轮交互。

### Phase 3: 记忆系统与领域微调 (Weeks 7-9)
- [ ] 集成向量数据库 (Chroma/Milvus) 构建长期记忆。
- [ ] 实现 RAG 流程，支持历史任务经验检索。
- [ ] 收集特定领域 (如电商、后台管理) 操作数据集。
- [ ] 使用 LoRA 对 VLM 进行领域适配微调 (Grounding 优化)。
- [ ] 优化 Prompt 工程，提升复杂任务成功率。

### Phase 4: 性能优化与产品化 (Weeks 10-12)
- [ ] 实现并行执行策略 (独立子任务并发处理)。
- [ ] 添加可视化调试界面 (实时展示 Agent 思考过程)。
- [ ] 完善异常处理与回滚机制。
- [ ] 编写详细文档与 API 参考。
- [ ] 发布开源版本 v1.0。

## 5. 技术栈选型

| 类别 | 技术选型 | 说明 |
| :--- | :--- | :--- |
| **核心框架** | **LangChain + LangGraph** | 多 Agent 编排、状态机管理、循环控制 |
| **多模态模型** | Qwen-VL-Chat / LLaVA-1.6 | 开源首选；生产环境可选 GPT-4V / Claude 3.5 Sonnet |
| **LLM 基座** | Qwen-2.5-72B / Llama-3-70B | 用于 Planner 的逻辑推理与代码生成 |
| **向量数据库** | ChromaDB / Milvus | 本地轻量级 vs 高性能分布式，用于记忆存储 |
| **自动化工具** | Playwright (Web), PyAutoGUI (OS), Appium (Mobile) | 跨平台执行层 |
| **微调框架** | HuggingFace Transformers + PEFT (LoRA) | 高效参数微调 |
| **实验跟踪** | Weights & Biases (W&B) | 记录训练指标与 Agent 执行轨迹 |
| **部署容器** | Docker + NVIDIA Container Toolkit | 环境隔离与 GPU 支持 |

## 6. 项目目录结构

```text
gui-agent-multi/
├── agents/                 # Agent 定义与逻辑
│   ├── planner.py          # 主规划 Agent
│   ├── workers/            # 子执行 Agent
│   │   ├── navigator.py
│   │   ├── operator.py
│   │   └── verifier.py
│   └── base_agent.py       # Agent 基类
├── brain/                  # 模型与推理核心
│   ├── multimodal_llm.py   # VLM 封装
│   ├── prompts/            # Prompt 模板管理
│   └── reasoning.py        # CoT 逻辑实现
├── memory/                 # 记忆系统
│   ├── short_term.py       # 对话缓冲
│   ├── long_term.py        # 向量检索 RAG
│   └── episodic.py         # 任务历史存储
├── tools/                  # 可调用的原子工具
│   ├── ui_actions.py       # 点击、输入等
│   ├── ocr_engine.py       # 文字识别
│   └── screenshot.py       # 截屏处理
├── orchestrator/           # 编排逻辑 (LangGraph)
│   ├── state_schema.py     # 共享状态定义
│   ├── graph_builder.py    # 工作流图构建
│   └── loops.py            # 循环与终止条件
├── environments/           # 测试环境
│   ├── sandbox.py
│   └── mock_apps/
├── datasets/               # 训练/评估数据
│   ├── raw/
│   └── processed/
├── configs/                # 配置文件
│   ├── models.yaml
│   └── agents.yaml
├── tests/                  # 单元测试与集成测试
├── notebooks/              # 实验与数据分析
├── main.py                 # 入口程序
├── requirements.txt
└── README.md
```

## 7. 关键挑战与解决方案

| 挑战 | 解决方案 |
| :--- | :--- |
| **多 Agent 死循环** | 在 LangGraph 中设置最大迭代次数；引入 Verifier Agent 强制终止条件；实施退避策略。 |
| **上下文窗口溢出** | 采用摘要压缩 (Summarization) 技术；仅保留关键步骤记忆；利用 RAG 按需检索历史。 |
| **视觉定位不准** | 融合 OCR 文本特征与图像特征；使用高分辨率切片输入；微调 VLM 的 Grounding 头。 |
| **执行安全性** | 所有动作在执行前经过 Safety Filter 校验；限制敏感区域操作；提供"人机回环"确认模式。 |
| **协同效率低** | 对于无依赖子任务，利用 LangGraph 的并行节点功能并发执行；优化通信协议减少冗余信息。 |

## 8. 预期成果
1. **开源框架**: 一个基于 LangChain 的可扩展多 Agent GUI 自动化框架。
2. **基准测试集**: 包含常见桌面/Web 操作场景的评估数据集 (Benchmark)。
3. **微调模型**: 针对 UI Grounding 优化的多模态模型权重 (LoRA Adapters)。
4. **演示应用**: 可演示复杂任务 (如"在电商网站比价并下单") 的完整 Demo。
5. **技术报告**: 详细的多 Agent 协同机制设计与性能分析报告。

## 9. 扩展方向
- **跨设备协同**: 同时控制手机和电脑，完成跨端任务。
- **自学习进化**: 基于强化学习 (RLHF) 从失败案例中自动优化策略。
- **插件生态**: 允许用户自定义开发特定的 Worker Agent 或工具插件。
- **云端部署**: 构建 SaaS 服务，提供远程浏览器自动化能力。
