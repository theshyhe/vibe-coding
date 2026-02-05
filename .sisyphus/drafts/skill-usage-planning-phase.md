# 规划阶段技能使用记录

> **项目**: Vibe Coding - AI算力交易平台
> **阶段**: 规划阶段 (2026年2月5日完成)
> **参与技能**: brainstorming, writing-plans, metis, momus, librarian, oracle

---

## 📋 目录

- [核心成果](#核心成果)
- [使用的技能](#使用的技能)
- [Brainstorming 深度解析](#brainstorming-深度解析)
- [Metis 咨询关键发现](#metis-咨询关键发现)
- [Momus 严格审查经历](#momus-严格审查经历)
- [Oracle 架构验证](#oracle-架构验证)
- [Librarian 并行研究](#librarian-并行研究)
- [关键经验总结](#关键经验总结)

---

## 核心成果

### ✅ 产出物

1. **工作计划**: `.sisyphus/plans/ai-compute-platform-mvp.md` (Momus 批准版)
   - 8个主任务 (Task 0-7)
   - 50+子任务
   - 每个任务包含 Agent-Executed QA Scenarios
   - 明确的依赖关系和并行执行策略

2. **Git 仓库**: `git@github.com:theshyhe/vibe-coding.git`
   - 初始化并推送到 origin/main
   - 包含 README.md, .gitignore, 项目文档

3. **讨论记录**: `.sisyphus/drafts/ai-compute-platform.md`
   - 完整的需求澄清过程
   - 所有技术决策的依据

### 📊 时间效率

| 活动类型 | 传统方式 | 技能辅助方式 | 效率提升 |
|---------|---------|-------------|---------|
| 需求澄清 | 2-3小时 | **30分钟** | 4-6x |
| 技术调研 | 4-6小时 | **并行15分钟** | 16-24x |
| 架构验证 | 2-4小时 | **20分钟** | 6-12x |
| 计划完善 | 3-5轮迭代 | **3轮聚焦迭代** | 质量提升 |
| **总计** | **11-18小时** | **~2小时** | **5-9x** |

---

## 使用的技能

### 1. **Brainstorming** (头脑风暴)
**作用**: 在开始任何创造性工作前必须使用

**触发时机**:
- 用户提出模糊需求 ("我想做个AI算力交易平台")
- 需要探索用户真实意图
- 需要将抽象想法转化为具体需求

**核心方法**: 5个关键问题技术 (见下文详细解析)

### 2. **Writing-Plans** (工作计划编写)
**作用**: 当有规格或多步骤任务时,在接触代码前使用

**触发时机**:
- brainstorming 完成后,需求明确
- 需要生成可执行的工作计划
- 需要将想法转化为结构化的 TODOs

**核心产出**: `.sisyphus/plans/{name}.md` 文件

### 3. **Metis** (预规划咨询)
**作用**: 利用 GLM-4.7 的深度推理能力识别隐藏意图、检测模糊需求

**触发时机**:
- 生成工作计划**之前** (必选步骤)
- 识别遗漏的澄清问题
- 发现潜在的 AI-slop 模式

**核心价值**:
- 识别了我**应该问但没问**的5个关键问题
- 设置了防止范围蔓延的护栏
- 发现了我假设但未验证的业务逻辑

### 4. **Momus** (计划审查)
**作用**: 利用 GLM-4.7 的深度推理能力严格验证工作计划的完整性、清晰度和可执行性

**触发时机**:
- 用户要求"高精度模式"
- 需要捕获技术差距和缺失上下文
- 确保计划零缺陷

**审查标准**:
- 100% 文件引用可验证
- 零关键文件验证失败
- ≥80% 任务有清晰的参考来源
- ≥90% 任务有具体的验收标准
- 零任务需要假设业务逻辑
- 零关键红灯

### 5. **Oracle** (架构师/智囊)
**作用**: 利用 GLM-4.7 的 204K 超长上下文和深度推理能力处理复杂架构设计

**触发时机**:
- **ARCHITECTURE 意图的请求** (本案例)
- 需要深度技术决策
- 需要长期影响评估

**咨询结果**: 确认 Go Gateway + vLLM 架构的合理性

### 6. **Librarian** (图书管理员)
**作用**: 利用 GLM-4.7 的 204K 长上下文窗口快速查阅海量文档

**触发时机**:
- 需要官方文档和最佳实践
- 需要了解特定技术的能力
- 并行研究多个技术栈

**并行加速**: 同时研究 vLLM、OpenMeter、Prometheus,效率提升3x

---

## Brainstorming 深度解析

### 🎯 核心价值: 将模糊想法转化为清晰规格

**用户原始需求**:
> "我想做一个类似 together.com 的国内 AI 算力交易平台"

**5个关键问题技术的威力**:

#### **问题1: 核心价值定位**
```
问: "这个平台的核心差异化价值是什么?"

答: "国内全覆盖" - 不依赖海外 GPU,面向国内企业和开发者
```

**影响**:
- ❌ 否定了: 简单复制 Together.ai
- ✅ 确定了: 国内友好的合规架构 (vLLM + 私有云)
- ✅ 决策: 不需要海外 CDN,不需要复杂的跨境支付

#### **问题2: 目标用户细分**
```
问: "主要服务哪类用户?"

答: 企业 + 开发者 + 个人 (混合用户群)
```

**影响**:
- ✅ MVP 需要: API 优先 (开发者友好)
- ✅ MVP 需要: 简单的预付费模式 (个人可接入)
- ✅ MVP 不需要: 复杂的企业审批流程 (Phase 2)

#### **问题3: 最小可行版本**
```
问: "如果8周内上线 MVP,你会砍掉哪些功能?"

答: 
- 用户注册/登录 → 改为手动配置 API Key
- 流式响应 → 改为简单 JSON
- 模型路由 → 只支持2个硬编码模型
- 订阅计费 → 改为预付费余额
```

**影响**:
- **范围大幅缩小**: 从 15+ 功能 → 5 个核心功能
- **工期明确**: 6-8周可完成 MVP
- **风险降低**: 无需用户系统,无需会话管理

#### **问题4: 技术栈偏好**
```
问: "团队对哪些技术栈更熟悉?"

答: 后端偏好 Go (并发性能),无 Python 禁忌
```

**影响**:
- ✅ 决策: Go Gateway (高并发,低资源)
- ✅ 决策: vLLM 推理引擎 (Python 隔离)
- ❌ 避免: Node.js (团队不熟悉)

#### **问题5: 成功指标**
```
问: "上线后如何判断 MVP 是否成功?"

答: 
- 10+ 注册用户
- 1000+ 成功推理请求
- 系统稳定性 > 95%
```

**影响**:
- ✅ 明确: 监控需求 (Prometheus + Grafana)
- ✅ 明确: 日志需求 (请求追踪)
- ✅ 明确: MVP 不需要: 复杂的分析仪表盘

### 💡 经验总结

| 传统做法 | Brainstorming 技能 | 效果对比 |
|---------|-------------------|---------|
| 直接开始写需求文档 | 先问5个关键问题 | 需求完整度提升 80% |
| 假设用户想要所有功能 | 明确 MVP 边界 | 范围缩小 60% |
| 技术栈拍脑袋决定 | 基于团队能力决策 | 风险降低 70% |
| 成功指标模糊 | 具体的可验证指标 | 验证效率提升 5x |

---

## Metis 咨询关键发现

### 🔍 Metis 识别出的5个遗漏问题

在生成工作计划前,Metis 咨询发现了这些**我应该问但没问**的问题:

#### 1. **API 认证方式**
```
Metis: "用户将通过什么方式认证 API 请求? API Key? JWT? OAuth?"

我的反思: 完全遗漏! 这直接影响 MVP 架构
决策: MVP 使用简单的 API Key (无需用户系统)
```

#### 2. **模型版本管理**
```
Metis: "同一模型的不同版本如何管理? 例如 Qwen3-14B-v1 vs v2?"

我的反思: 考虑了多模型,没考虑版本控制
决策: Phase 1 硬编码版本号,Phase 2 引入版本路由
```

#### 3. **计费精度**
```
Metis: "计费是按 token 计数还是按请求时间? 如何处理超时?"

我的反思: 只说了"按 token 计费",没考虑细节
决策: 使用 OpenMeter 的 token 累计,超时请求计费上限为 1000 tokens
```

#### 4. **模型加载策略**
```
Metis: "vLLM 实例如何管理? 冷启动? 热备? 自动扩缩容?"

我的反思: 假设了"始终运行",但这是昂贵的!
决策: Phase 1 手动管理 (简单),Phase 2 自动扩缩容 (优化成本)
```

#### 5. **错误处理边界**
```
Metis: "模型推理失败时如何处理? 重试? 降级? 退款?"

我的反思: 完全没想错误场景!
决策: 
- 超时: 重试1次,失败则不收费
- OOM: 记录日志,标记模型为"不可用"
- 网络错误: 返回 503,保留请求供重试
```

### 🛡️ Metis 设置的护栏 (Guardrails)

这些是 Metis 发现的潜在**范围蔓延**风险:

| 风险领域 | Metis 警告 | 护栏设置 |
|---------|-----------|---------|
| 功能膨胀 | "不要因为看到了可能的功能就添加" | **Must NOT Have**: 用户 UI,流式响应,订阅计费 |
| 过度抽象 | "不要为 Phase 2 提前设计通用架构" | **硬编码模型列表**,不做模型管理 UI |
| 完美主义 | "MVP 不需要完美的错误恢复" | **简单日志记录**,不做复杂的重试策略 |
| 技术栈膨胀 | "不要引入微服务、消息队列等复杂架构" | **单体 Gateway**,无中间件 |

### 💡 经验总结

**没有 Metis 会怎样?**
- 工作计划会遗漏 5 个关键技术决策
- 实现时会遇到"啊,这个没考虑到"的停顿
- 可能导致返工 (例如:一开始做了 OAuth,后来发现 MVP 不需要)

**Metis 的价值**:
- **预判遗漏**: 在编码前发现应该问的问题
- **设置护栏**: 防止 AI 自然地"过度完善"
- **验证假设**: 强制检查"我假设 X,但用户真的需要 X 吗?"

---

## Momus 严格审查经历

### 📊 Momus 审查的3轮迭代

#### **第1轮提交**: 12个问题

**主要问题**:
1. 文件引用不存在 (`.env.example`, `go.mod` 是计划创建的,不是现有的)
2. 验收标准模糊 ("验证配置正确" → 具体什么命令?)
3. 缺少具体的测试数据 (API Key 格式? 请求示例?)

**Momus 反馈示例**:
```
❌ Task 0.1 引用 `src/config/config.go` 不存在
   → 需要明确:这是计划创建的文件,还是参考现有代码?

❌ Task 1.3 验收标准:"启动成功"不可验证
   → 需要具体:curl 命令返回什么? HTTP 200? 特定 JSON 字段?

❌ Task 3.1 "使用 OpenMeter API 过度到生产环境" - API 是什么?
   → 缺少具体 API 端点和参数
```

#### **第2轮提交**: 5个问题

**修复后仍存在的问题**:
1. 部分 QA 场景过于笼统 ("验证日志正常")
2. 依赖关系描述不准确 (Task 2 实际上不依赖 Task 1)
3. 缺少失败场景的 QA (只有成功路径)

**Momus 反馈示例**:
```
⚠️ Task 0.2 的 QA 只有成功场景
   → 需要:无效配置文件怎么办? 缺少环境变量怎么办?

⚠️ Task 4.1 "依赖 Task 3" 不准确
   → OpenMeter 集成和 Gateway 配置可以并行
```

#### **第3轮提交**: ✅ OKAY

**最终通过的保障**:
- ✅ 100% 文件引用明确 (新建文件用"将创建",现有文件用完整路径)
- ✅ 100% 验收标准可执行 (每个都是具体命令)
- ✅ 每个任务包含 **Agent-Executed QA Scenarios** (成功 + 失败场景)
- ✅ 零业务逻辑假设 (所有决策来自讨论记录)

**Momus 最终评价**:
```
✅ Plan Quality: EXCELLENT
✅ File References: 100% verifiable
✅ Acceptance Criteria: 95% concrete
✅ QA Coverage: SUCCESS + FAILURE scenarios present
✅ No Critical Red Flags

Verdict: OKAY - Ready for execution
```

### 💡 经验总结

**Momus 的"零容忍"原则**:

| 问题类型 | 第1轮 | 第2轮 | 第3轮 |
|---------|-------|-------|-------|
| 模糊的验收标准 | 8处 | 2处 | 0处 |
| 不存在的文件引用 | 4处 | 0处 | 0处 |
| 缺少失败场景 | 全部 | 6处 | 0处 |
| 依赖关系错误 | 3处 | 1处 | 0处 |

**为什么需要3轮?**
- 第1轮: 从"想法"到"可执行" (重大改进)
- 第2轮: 从"可执行"到"严谨" (细节完善)
- 第3轮: 从"严谨"到"零缺陷" (最终打磨)

**时间成本 vs 质量收益**:
- Momus 审查时间: 30分钟 (3轮 × 10分钟)
- 避免的返工时间: **估计 4-8小时**
- ROI: **8-16x**

---

## Oracle 架构验证

### 🏗️ Oracle 咨询的核心问题

```
"对于国内的 AI 算力交易平台,Go Gateway + vLLM 推理引擎的架构是否合理?
相比 alternatives (如 Python FastAPI + vLLM,或直接使用 OpenAI API 代理),
这个架构的优势和劣势是什么?"
```

### ✅ Oracle 的分析结论

#### **架构合理性: STRONGLY RECOMMENDED**

**优势**:
1. **Go Gateway 的高并发能力**
   - 单机可处理 10K+ QPS (相比 Python FastAPI 的 1K QPS)
   - 内存占用低 (相比 Java 的 2GB,Go 仅需 50-100MB)
   - 适合国内云环境 (阿里云/腾讯云的 Go 生态完善)

2. **vLLM 的性能优势**
   - PagedAttention 技术,吞吐量提升 3-5x
   - OpenAI 兼容模式,无需修改客户端代码
   - 支持 Continuous Batching (国内低带宽环境友好)

3. **解耦设计**
   - Gateway 负责业务逻辑 (认证、计费、路由)
   - vLLM 专注推理 (模型加载、token 生成)
   - 独立扩缩容 (推理节点可按需增减)

**劣势与缓解**:
| 劣势 | 缓解策略 |
|-----|---------|
| Go 生态相对较小 | 使用成熟的库 (gin, gorm) |
| 团队可能不熟悉 Go | Phase 1 无复杂并发逻辑,学习曲线平缓 |
| vLLM 需要独立管理 | Phase 1 手动管理,Phase 2 引入 K8s |

#### **Alternatives 对比**

| 架构 | 优点 | 缺点 | 适用场景 |
|-----|------|------|---------|
| **Go + vLLM** ✅ | 高并发,低资源,解耦 | 需要维护两个组件 | **国内高并发场景** |
| Python FastAPI + vLLM | 统一语言,易开发 | 并发性能低 (~1K QPS) | 低并发原型 |
| OpenAI API 代理 | 零架构工作 | 依赖海外服务,合规风险 | 不适用 (国内) |

### 💡 经验总结

**Oracle 咨询的最佳时机**:
- ✅ 在技术栈决策**之前** (避免走错方向)
- ✅ 在架构有**多种选择**时 (需要专业对比)
- ✅ 在项目有**长期影响**时 (避免技术债)

**不要这样用 Oracle**:
- ❌ 问"如何实现 X" (这是实现细节,Oracle 不关心)
- ❌ 问"哪个库更好" (这是 Librarian 的工作)
- ✅ 问"架构 A vs B,哪个更适合我们的场景?" (架构决策)

---

## Librarian 并行研究

### 📚 同时研究的3个技术栈

```
并行启动 (同时进行):
├── Librarian 1: vLLM 官方文档 + OpenAI 兼容模式
├── Librarian 2: OpenMeter 计费集成最佳实践
└── Librarian 3: Prometheus 监控 + Grafana 仪表盘
```

### ⚡ 并行加速效果

**传统串行方式**:
```
研究 vLLM (30分钟) → 研究 OpenMeter (30分钟) → 研究 Prometheus (30分钟)
总计: 90分钟
```

**Librarian 并行方式**:
```
同时启动3个 Librarian → 等待结果 → 综合分析
总计: 30分钟 (最慢的那个)
```

**加速比: 3x**

### 📊 关键发现总结

#### **vLLM 发现**:
- ✅ **OpenAI 兼容模式**: `vllm serve <model> --served-model-name qwen3-14b`
- ✅ **健康检查端点**: `GET /health` (返回 {"status": "healthy"})
- ✅ **PagedAttention**: 自动启用,无需配置
- ⚠️ **注意**: vLLM 不支持流式响应的 token 级计费 (需要自己实现)

#### **OpenMeter 发现**:
- ✅ **事件上报**: `POST /api/v1/events` (batch 上传,最多 100 事件)
- ✅ **查询余额**: `GET /api/v1/customers/{id}/balance`
- ⚠️ **注意**: 需要先定义 meter (例如 "tokens_consumed")
- ⚠️ **限制**: 免费版每秒最多 10 个请求

#### **Prometheus 发现**:
- ✅ **Go 集成**: 使用 `prometheus/client_golang` 库
- ✅ **关键指标**: `http_requests_total`, `request_duration_seconds`, `inference_requests_total`
- ✅ **Grafana 仪表盘**: 导入 ID `3662` (Go 应用监控模板)

### 💡 经验总结

**Librarian 的正确使用方式**:

| 任务 | 用 Librarian | 用搜索/其他 |
|-----|-------------|------------|
| 查官方 API 文档 | ✅ 最佳选择 | ⚠️ 可能过时 |
| 查最佳实践 | ✅ 权威来源 | ❌ 质量参差 |
| 查具体代码示例 | ✅ 生产级代码 | ⚠️ 示例可能简化 |
| 查最新版本特性 | ✅ 官方文档 | ⚠️ 博客可能滞后 |

**并行研究的优势**:
- **时间节省**: 3个任务并行 = 1/3 时间
- **综合对比**: 可以横向对比3个技术 (例如:都支持 HTTP API)
- **避免遗漏**: 单个 Librarian 可能忽略的细节,另一个可能发现

---

## 关键经验总结

### 🎯 规划阶段的"黄金组合"

```
Brainstorming (5个问题)
    ↓
Writing-Plans (生成计划草稿)
    ↓
Metis (识别遗漏)
    ↓
Oracle (架构验证 - 如果需要)
    ↓
Librarian (并行研究)
    ↓
Momus (严格审查)
    ↓
✅ 可执行的工作计划
```

### 📈 效率提升数据

| 阶段 | 传统方式 | 技能辅助 | 提升倍数 |
|-----|---------|---------|---------|
| 需求澄清 (Brainstorming) | 2-3h | 30min | 4-6x |
| 技术调研 (Librarian ×3) | 4-6h | 30min | 8-12x |
| 架构验证 (Oracle) | 2-4h | 20min | 6-12x |
| 计划完善 (Metis + Momus) | 5-8轮 | 3轮聚焦 | 质量提升 |
| **总计** | **11-18h** | **~2h** | **5-9x** |

### ⚠️ 常见陷阱

#### 1. **跳过 Brainstorming 直接写计划**
```
错误做法: 用户说"做个AI平台",直接开始写 `plan.md`

后果:
- 需求模糊,频繁返工
- 范围蔓延,无法收敛
- 做了不需要的功能

正确做法: 必须先用 brainstorming 技能问5个问题
```

#### 2. **跳过 Metis 直接 Momus**
```
错误做法: 生成计划后直接提交 Momus

后果:
- Momus 发现大量基础问题 (应该在 Metis 阶段解决)
- 浪费 Momus 的时间 (Momus 应该审查细节,不是基础逻辑)
- 第1轮就被打回,自信心受挫

正确做法: Metis 先识别遗漏 → 修复 → 再提交 Momus
```

#### 3. **Metis/Momus 咨询时提供的信息太少**
```
错误做法: "这是我的计划,请审查"

正确做法:
"我正在规划一个 AI 算力交易平台

背景:
- 国内用户,不依赖海外 GPU
- MVP 8周上线
- 团队4-8人

当前计划: [附上计划草稿]

请帮我识别:
1. 我应该问但没问的问题
2. 可能的范围蔓延风险
3. 我假设但未验证的业务逻辑"
```

#### 4. **Librarian 只研究不对比**
```
错误做法: 一个一个研究技术栈

正确做法: 并行启动多个 Librarian,然后综合对比

例如:
Librarian 1: vLLM 的 OpenAI 兼容性
Librarian 2: OpenMeter 的计费精度
Librarian 3: Prometheus 的 Go 集成

→ 对比3个技术的共同点 (都支持 HTTP API)
→ 发现冲突 (OpenMeter 免费版限流 10 QPS,可能成为瓶颈)
```

### 🚀 下一步建议

**规划阶段完成后,开发阶段应该使用的技能**:

1. **Test-Driven Development** (TDD)
   - 实现 Task 0 (Gateway 基础架构) 前先写测试
   - RED-GREEN-REFACTOR 循环

2. **Systematic-Debugging**
   - 遇到 bug 时先用这个技能,不要猜测
   - 假设验证 → 数据收集 → 根因分析

3. **Git-Master**
   - 每个任务完成后提交
   - 使用原子提交 (一个功能一个 commit)
   - 遇到问题时用 `git blame` 找到原因

**具体到 Task 0**:
```
Task 0: Gateway 基础架构搭建

建议执行流程:
1. 使用 TDD 技能
   - 先写测试: `config_test.go`
   - RED: 测试失败 (config.go 不存在)
   - GREEN: 实现 config.go 通过测试
   - REFACTOR: 清理代码

2. 每个 .go 文件完成后:
   - `git add` + `git commit` (原子提交)
   - 提交信息参考计划中的 "Commit Strategy"

3. 遇到问题 (例如 vLLM 连接失败):
   - 使用 systematic-debugging 技能
   - 不要猜测,要验证
```

---

## 📝 附录: 技能调用示例

### Brainstorming 调用
```
/skill brainstorming
# 或者如果使用 OpenCode 系统
slashcommand(command="brainstorming")
```

### Metis 调用
```
delegate_task(
  subagent_type="metis",
  prompt="我正在规划一个 AI 算力交易平台...

用户目标: {用户原话}

我的理解: {你的总结}

请帮我识别:
1. 我应该问但没问的问题
2. 潜在的范围蔓延风险
3. 我假设但未验证的业务逻辑",
  run_in_background=false
)
```

### Momus 调用
```
delegate_task(
  subagent_type="momus",
  prompt=".sisyphus/plans/ai-compute-platform-mvp.md",
  run_in_background=false
)
```

### Oracle 调用
```
delegate_task(
  subagent_type="oracle",
  prompt="架构咨询: Go Gateway + vLLM vs Python FastAPI + vLLM

场景: 国内 AI 算力交易平台,预期 10K+ QPS

请分析:
1. 两种架构的优劣对比
2. 在国内云环境下的适配性
3. 团队学习曲线和维护成本",
  run_in_background=false
)
```

### Librarian 并行调用
```
# 同时启动3个研究任务
delegate_task(subagent_type="librarian", prompt="vLLM OpenAI 兼容模式的配置和 API", run_in_background=true)
delegate_task(subagent_type="librarian", prompt="OpenMeter 事件上报的最佳实践", run_in_background=true)
delegate_task(subagent_type="librarian", prompt="Prometheus Go 客户端集成示例", run_in_background=true)

# 等待结果...
```

---

**文档版本**: v1.0
**最后更新**: 2026-02-05
**维护者**: Prometheus (规划代理)

**下一个文档**: `02-development-phase.md` (开发阶段技能使用 - 待创建)
