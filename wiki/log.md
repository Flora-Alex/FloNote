# 变更日志

## [2026-06-03] ingest | 引入 raw/i 项目知识沉淀
- **变更**: 新建 wiki/projects/ 目录，创建 6 篇项目知识页面
  - [[projects/floraAdmin]] — Flora Admin 企业级后台（六层 Maven 模块化、策略工厂 ×6、动态配置中心）
  - [[projects/floraPic]] — FloraPic 云图库平台（双架构并行、Disruptor 事件驱动、动态分表）
  - [[projects/floraAiAgent]] — Flora AI Agent 智能体平台（ReAct 四层继承、RAG 管道、MCP 协议）
  - [[projects/aiTarot]] — AITarot AI 塔罗牌（SSE 流式副作用、Prompt-as-Contract）
  - [[projects/dotfiles]] — Dotfiles 开发环境配置（分层配置、跨工具键位一致性）
  - [[projects/designPatternsCollection]] — 跨项目设计模式与方法论总结
- **更新**: [[index.md]] 新增"项目知识沉淀"分区
- **冲突**: 无

## [2026-06-03] new | 创建 Mac 配置还原指南
- **变更**: 新建 [[macSetup]]，包含完整的 Mac 环境配置、软件列表、dotfiles 和还原步骤
- **冲突**: 无

## [2026-06-03] lint | 统一文件命名规范（驼峰命名）
- **变更**: 
  - 重命名 alfred/ 文件夹：alfred-getting-started.md → gettingStarted.md, alfred-basic-features.md → basicFeatures.md, alfred-shortcuts.md → shortcuts.md, alfred-advanced-features.md → advancedFeatures.md, alfred-workflows.md → workflows.md, alfred-tips.md → tips.md
  - 重命名 career/ 文件夹：flora-profile.md → floraProfile.md, career-plan.md → careerPlan.md, financial-plan.md → financialPlan.md, collaboration-plan.md → collaborationPlan.md
  - 重命名 dev-tools/ 文件夹：git-config.md → gitConfig.md, zsh-and-shell.md → zshAndShell.md
  - 更新 [[index.md]]、[[CLAUDE.md]]、[[AGENTS.md]] 中的链接和规范
  - 更新所有文档中的双向链接（共 50 个文件）
- **冲突**: 无

## [2026-06-03] new | 创建 Alfred 效率工具文档
- **变更**: 新建 [[alfred/README]]、[[alfred/gettingStarted]]、[[alfred/basicFeatures]]、[[alfred/shortcuts]]、[[alfred/advancedFeatures]]、[[alfred/workflows]]、[[alfred/tips]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-07] ingest | 引入 AI 智能体项目知识库
- **变更**: 新增 [[RAG]], [[AIAgent]], [[ToolCalling]], [[AIHallucination]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-07] lint | 为 wiki/ai/ 页面添加 YAML Frontmatter
- **变更**: 为 [[AIHallucination]], [[CoT]], [[MCP]], [[Prompt]], [[RAG]], [[ReAct]], [[ToolCalling]] 添加 YAML 头部; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-07] ingest | 引入 Java 后端项目初始化知识
- **变更**: 新增 [[java项目初始化]]（基于 raw/personal_graph_proj/项目初始化.md 总结）; 为 [[MyBatis Plus]], [[Hutool]], [[Knife4j]] 添加 YAML 头部; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 RAG 面试题更新 RAG 知识库
- **变更**:
  - 大幅扩充 [[RAG]] 文档，新增：RAG vs 微调对比、详细 Chunking 策略、Embedding 模型选型、向量数据库选型、多路召回、Query 改写、Rerank 精排、高级 RAG 范式（Self-RAG/CRAG/GraphRAG/Agentic RAG）、幻觉规避策略、效果评估指标、知识库动态更新、落地难点
  - 新增向量数据库文档：[[Chroma]], [[Qdrant]], [[Milvus]], [[Weaviate]]
  - 更新 [[AIHallucination]], [[ReAct]], [[Prompt]]，增加与 RAG 的双向链接
  - 更新 [[index.md]]，添加向量数据库分类索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Agent 面试题更新 Agent 知识库
- **变更**:
  - 大幅扩充 [[AIAgent]]，新增：Agent 与 LLM 本质区别、Tools/Agent/Workflow 三层结构、ReAct/Plan-and-Execute/Reflection 范式、CoT/ToT/GoT 推理模式、四层记忆系统、Multi-Agent 架构、评估指标
  - 新增 [[Workflow]]，涵盖编排模式、Agentic Workflow、与 Agent 的对比
  - 大幅扩充 [[ReAct]]，新增核心循环、实现原理、代码示例
  - 扩充 [[ToolCalling]]，新增工具设计原则、MCP 协议
  - 扩充 [[CoT]]，新增 Zero-shot/Few-shot CoT、与 ReAct/ToT/GoT 对比
  - 更新 [[index.md]]，添加 Workflow 索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Function Calling 面试题更新工具调用知识库
- **变更**:
  - 大幅扩充 [[ToolCalling]]，新增：Function Calling 原理、训练原理、FC vs MCP 对比、三层架构
  - 大幅扩充 [[MCP]]，新增：核心问题、三层结构、通信方式、MCP vs FC/Skill/A2A
  - 新增 [[AgentSkill]], [[A2A]], [[LLMGateway]]
  - 更新 [[AIAgent]]，建立与 MCP/AgentSkill/A2A/ToolCalling 的双向链接
  - 更新 [[index.md]]
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Java 面试题重构 Backend 知识库
- **变更**:
  - 新增 [[Java基础]], [[Java集合]], [[Java并发]], [[JVM]], [[Spring]]
  - 将 [[MyBatis Plus]] 整合并改名为 [[MyBatis]]
  - 更新 [[index.md]]，重构后端开发分类
- **冲突**: 无

## [2026-05-15] ingest | 根据小林大模型面试题更新 LLM 知识库
- **变更**:
  - 新增 [[LLM]]，涵盖大语言模型定义、涌现能力、幻觉机制与缓解、模型选型、评测指标
  - 新增 [[Transformer]]，涵盖 Self-Attention、三种架构变体、MQA/GQA/Flash Attention、RoPE、BPE
  - 新增 [[MoE]]，涵盖混合专家模型、DeepSeek V3/Mixtral/Qwen MoE 对比
  - 新增 [[LLMTraining]]，涵盖三阶段训练、RLHF/DPO/GRPO、LoRA/QLoRA、Scaling Law
  - 新增 [[LLMInference]]，涵盖解码策略、KV Cache、量化、部署框架
  - 更新 [[index.md]]，重构 AI 分类
  - 建立完整的双向链接网络
- **冲突**: 无

## [2026-05-16] ingest | 构建投资与金融知识库
- **变更**:
  - 新增 [[金融市场概览]], [[股票投资入门]], [[期货交易基础]], [[技术分析]], [[K线图]], [[基本面分析]], [[投资组合理论]]
  - 更新 [[index.md]]，新增"时事与投资"分类
- **冲突**: 无
- **备注**: OpenCLI 因 SSL 证书问题未可用，改用 Wikipedia/WebFetch 搜集资料

## [2026-05-16] sync | 移动投资知识库至 current-affairs 目录
- **变更**: 将 wiki/investment/ 整体移至 wiki/current-affairs/investment/; 更新 [[index.md]] 链接路径
- **冲突**: 无

## [2026-05-16] ingest | 收录 B 站金融理财入门视频到投资知识库
- **来源**: B站视频「大学生普通人金融理财入门学习手册」(BV1tj6wBDEXF, UP主: 朝阳不吐不快)
- **变更**:
  - 新增 [[债券投资]]，涵盖：金钱时间价值、折现率、票面利率 vs 到期收益率、久期策略、储蓄式/柜台式国债、国债逆回购、企业债投资渠道
  - 新增 [[基金投资]]，涵盖：被动 vs 主动基金对比、三种被动基金类型、场内 ETF vs 场外基金（T+1机制）、溢价率、余额宝/零钱通（货币基金）底层资产与运作原理
  - 扩充 [[股票投资入门]]，新增：开仓/持仓/平仓/仓位/做多/做空术语、A 股准入门槛、交易摩擦成本（印花税/过户费/佣金）、A 股税收、投资 vs 投机区分（格雷厄姆定义）、投资者心理陷阱（FOMO/追涨杀跌/频繁交易）、真正风险 = 资本永久损失、对抗波动方法、投资本质 = 做生意
  - 扩充 [[金融市场概览]]，新增：个人投资者可选产品一览表（余额宝→国债逆回购→债券基金→被动基金→主动基金→商品基金→个股→期货）
  - 更新 [[index.md]]，新增债券投资和基金投资索引
- **冲突**: 无

## [2026-06-02] ingest | 构建开发工具配置知识库
- **变更**:
  - 新增 [[README-devtools]]，开发工具配置总览（快速开始、快捷键速查、命令速查）
  - 新增 [[zshAndShell]]，Zsh 配置指南（Oh-My-Zsh、Starship、别名、函数、插件）
  - 新增 [[gitConfig]]，Git 配置指南（20+ 别名、颜色配置、使用示例）
  - 新增 [[gitignore]]，Gitignore 配置指南（macOS/IDE/Java/Node.js/Python 规则）
  - 所有文档包含 YAML Frontmatter、双向链接、源码示例
  - 更新 [[index.md]]，新增开发工具分类索引
- **冲突**: 无
- **来源**: 优化 ~/.zshrc、~/.gitconfig、~/.gitignore_global 配置

## [2026-06-02] ingest | 优化 Ghostty 终端配置并添加文档
- **变更**:
  - 优化 `~/.config/ghostty/config.ghostty`
    - 修复 shader 文件缺失问题（注释掉不存在的 shader 引用）
    - 优化 scrollback-limit（25MB → 10000 行，节省内存）
    - 添加中文字体回退（PingFang SC）
    - 明确 shell-integration = zsh
  - 新增 [[ghostty]]，Ghostty 终端配置指南
    - 完整配置详解（每个选项的源码和说明）
    - 快捷键速查表（标签页、分屏、字体）
    - 主题配色说明（Kanagawa Dragon）
    - 优化记录和常见问题
  - 更新 [[index.md]]
- **冲突**: 无

## [2026-06-02] ingest | 构建职业发展与个人规划文档体系
- **变更**:
  - 新增 [[floraProfile]]，个人画像文档
    - 技术画像：当前技术栈、项目经验、能力评估
    - 性格特质：学习态度、工作方式、思维模式
    - 优势分析：学习能力、工程化思维、全栈潜力、AI 敏感度
    - 成长建议：技术成长、职业成长、财务成长
  - 新增 [[careerPlan]]，职业发展规划文档
    - 职业定位：短期/中期/长期目标
    - 技术路线图：三个阶段的成长路径
    - 技能矩阵：后端核心技能、工程能力、软技能
    - 学习计划：每周安排、季度目标
    - 里程碑：技术、学习、财务里程碑
  - 新增 [[financialPlan]]，理财规划文档
    - 理财理念：核心原则、财务目标
    - 投资品种：基金、股票、债券、理财产品
    - 资产配置：配置原则、年龄法则、推荐方案
    - 学习计划：三阶段学习路径
    - 实践指南：开户、第一次投资、投资记录
    - 风险管理：风险控制、常见错误、心态建设
  - 新增 [[collaborationPlan]]，长期协作计划文档
    - 协作理念：核心思想、协作原则
    - 协作模式：日常协作、协作层次
    - 协作场景：项目开发、学习辅导、面试准备、理财学习、职业规划
    - 知识管理：知识库结构、更新流程、知识分类
    - 长期规划：短期/中期/长期计划
    - 协作规范：沟通规范、文档规范、工作流程
  - 更新 [[index.md]]，新增职业发展分类
- **冲突**: 无
- **来源**: 基于对 Flora 的了解，创建体系化的个人发展文档

## [2026-06-23] query | 总结 Transformer 第一课原始资料知识点
- **输出**: 基于 raw/大模型学习/第1课-Transformer、[[Transformer]]、[[MoE]]、[[LLMTraining]]、[[LLMInference]] 即时回答未保存

## [2026-06-23] ingest | 分文件总结 Transformer 第一课知识点
- **变更**: 新增 [[transformerLessonOverview]], [[transformerTokenization]], [[transformerEmbedding]], [[transformerAttentionMechanism]], [[transformerAttentionOptimization]], [[transformerMoEArchitecture]], [[transformerPaperReading]], [[transformerCodePractice]], [[transformerByHandExcel]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-23] sync | 整理 Transformer 相关页面到专属文件夹
- **变更**: 将 [[Transformer]], [[transformerLessonOverview]], [[transformerTokenization]], [[transformerEmbedding]], [[transformerAttentionMechanism]], [[transformerAttentionOptimization]], [[transformerMoEArchitecture]], [[transformerPaperReading]], [[transformerCodePractice]], [[transformerByHandExcel]] 移动至 `wiki/ai/transformer/`; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-23] ingest | 分文件总结 Llama3 from scratch 第二课知识点
- **变更**: 新增 [[llama/llamaOverview]], [[llama/llamaArchitecture]], [[llama/llamaTokenizer]], [[llama/llamaFromScratch]], [[deepseek/deepseekOverview]], [[deepseek/deepseekArchitecture]], [[deepseek/deepseekR1]], [[transformer/transformerKvCache]], [[transformer/transformerGqaRope]], [[transformer/transformerDecodingSampling]], [[transformer/decoderOnlyEvolution]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-23] ingest | 合并 RAG 第4/5课并整理到 RAG 文件夹
- **变更**: 创建 `wiki/ai/rag/`; 移动并更新 [[rag/RAG]], [[rag/Chroma]], [[rag/Qdrant]], [[rag/Milvus]], [[rag/Weaviate]]; 新增 [[rag/ragChunking]], [[rag/ragRetrievalOptimization]], [[rag/ragAdvancedPatterns]], [[rag/ragIndustrialPractice]], [[rag/ragEvaluation]], [[rag/ragVectorDatabases]], [[rag/ragLlamaIndex]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-23] ingest | 合并 Agent 第6课并整理到 Agent 文件夹
- **变更**: 创建 `wiki/ai/agent/`; 移动并更新 [[agent/AIAgent]], [[agent/ReAct]], [[agent/Workflow]], [[agent/CoT]], [[agent/ToolCalling]], [[agent/AgentSkill]], [[agent/A2A]], [[agent/LLMGateway]]; 新增 [[agent/agentPlanning]], [[agent/agentArchitectures]], [[agent/agentMemory]], [[agent/agentToolUse]], [[agent/functionCallingOptimization]], [[agent/functionCallFineTuning]], [[agent/agentIndustrialPractice]], [[agent/agentResumeAndEvaluation]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-24] ingest | 深度整理从零 RAG 大师课程
- **变更**: 基于 `raw/大模型学习/第4课-RAG (Part 1)：检索增强生成 - 原理与核心组件/从零-RAG大师/Readme.md` 与 0-21 个 notebook，新增 [[rag/ragFromZeroToHeroCourse]], [[rag/simpleRagPipeline]], [[rag/semanticChunking]], [[rag/chunkSizeSelection]], [[rag/contextEnrichedRetrieval]], [[rag/contextualChunkHeaders]], [[rag/questionAugmentedRag]], [[rag/queryTransformation]], [[rag/reranking]], [[rag/relevantSegmentExtraction]], [[rag/contextCompression]], [[rag/feedbackLoopRag]], [[rag/adaptiveRetrieval]], [[rag/selfRag]], [[rag/propositionChunking]], [[rag/multimodalRag]], [[rag/hybridRetrieval]], [[rag/graphRag]], [[rag/hierarchicalRetrieval]], [[rag/hydeRag]], [[rag/correctiveRag]], [[rag/rlEnhancedRag]], [[rag/ragImplementationPitfalls]]; 更新 [[rag/RAG]] 与 [[index.md]]
- **冲突**: 无
- **备注**: 多个 notebook 输出中出现 API Key 明文；本次未复述密钥，建议清理 notebook 输出并轮换相关密钥

## [2026-06-24] sync | 合并 RAG 知识页到 8 个以内
- **变更**: 将 `wiki/ai/rag/` 下 35 个页面合并为 8 个聚合页面：[[rag/RAG]], [[rag/ragChunking]], [[rag/ragRetrievalOptimization]], [[rag/ragAdvancedPatterns]], [[rag/ragIndustrialPractice]], [[rag/ragEvaluation]], [[rag/ragVectorDatabases]], [[rag/ragLlamaIndex]]; 删除被合并的细粒度页面; 更新 [[index.md]]
- **冲突**: 无

## [2026-06-24] sync | 合并 Transformer 与 Agent 知识页
- **变更**: 将 `wiki/ai/transformer/` 从 14 个页面合并为 5 个页面：[[transformer/Transformer]], [[transformer/foundations]], [[transformer/inferenceOptimization]], [[transformer/modernArchitectures]], [[transformer/practice]]；将 `wiki/ai/agent/` 从 16 个页面合并为 6 个页面：[[agent/AIAgent]], [[agent/agentArchitectures]], [[agent/agentToolUse]], [[agent/agentMemory]], [[agent/agentIndustrialPractice]], [[agent/agentResumeAndEvaluation]]；更新 [[index.md]]
- **冲突**: 无

## [2026-06-24] ingest | 总结 Transformer 手搓计算过程
- **变更**: 基于 `手搓transformer.ipynb`、`手撕Attention.ipynb` 与 AI by Hand Excel 手算材料，新增 [[transformer/transformerComputationProcess]]；更新 [[transformer/practice]] 与 [[index.md]]
- **冲突**: 无

## [2026-07-02] sync | 移除 career 与 current-affairs 内容
- **变更**: 用户删除了 `wiki/career/` 与 `wiki/current-affairs/` 下的全部文件；从 [[index.md]] 移除“职业发展”与“时事与投资”两个分区及其条目（[[floraProfile]]、[[careerPlan]]、[[financialPlan]]、[[collaborationPlan]]、[[金融市场概览]]、[[股票投资入门]]、[[债券投资]]、[[基金投资]]、[[期货交易基础]]、[[技术分析]]、[[K线图]]、[[基本面分析]]、[[投资组合理论]]）
- **冲突**: 无
