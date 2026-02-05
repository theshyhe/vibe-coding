# Claude Code 高阶技巧快速参考

> **从项目 0-1 实战中提炼的 12+ 个实用技巧**
> 
> 适用于: 复杂项目规划、技术架构设计、团队协作

---

## 📋 目录

- [核心原则](#核心原则)
- [规划阶段技巧](#规划阶段技巧)
- [执行阶段技巧](#执行阶段技巧)
- [协作技巧](#协作技巧)
- [效率提升技巧](#效率提升技巧)

---

## 核心原则

### 🎯 技巧1: 技能优先原则

**规则**: 在任何任务开始前,先检查是否有相关技能适用

**触发条件**:
- 任务开始前 (必须)
- 遇到问题时 (应该)
- 不确定如何做时 (必须)

**检查流程**:
```
1. 我要做什么?
   ↓
2. 有技能适用于这个任务吗?
   ↓
3. 如果有 → 使用 Skill 工具加载
   ↓
4. 如果没有 → 继续执行
```

**示例**:
```
❌ 错误: "我想规划一个项目" → 直接开始写计划
✅ 正确: "我想规划一个项目" → 先加载 brainstorming 技能

❌ 错误: "有个 bug" → 直接猜测原因
✅ 正确: "有个 bug" → 先加载 systematic-debugging 技能
```

**为什么会忽略这个技巧?** (红旗识别)
| 你的想法 | 现实 |
|---------|------|
| "这只是个简单任务" | 简单任务也值得用技能 |
| "我记住了这个技能" | 技能会更新,应该重新加载 |
| "我先快速做完" | 没有技能指导,返工概率高 |
| "技能是给新手的" | 专家也需要流程纪律 |

---

## 规划阶段技巧

### 🎯 技巧2: 5个关键问题技术

**来源**: brainstorming 技能

**核心价值**: 将模糊想法转化为清晰规格

**5个问题框架**:

#### 问题1: 核心价值定位
```
问: "这个项目/功能的核心差异化价值是什么?"

目的:
- 明确"为什么要做"
- 避免盲目复制竞品
- 找到独特卖点

示例:
❌ "我想做一个类似 OpenAI 的 API"
✅ "我想做一个面向国内企业的 AI API,不依赖海外 GPU"

影响:
- 技术栈选择 (国内云 vs 海外 AWS)
- 合规要求 (数据不出境)
- 成本结构 (私有云 vs 公有云)
```

#### 问题2: 目标用户细分
```
问: "主要服务哪类用户?"

目的:
- 明确用户画像
- 确定功能优先级
- 设计合适的使用流程

常见用户类型:
- 企业客户: 需要稳定性、SLA、技术支持
- 开发者: 需要清晰的 API、SDK、文档
- 个人用户: 需要简单、低成本、快速上手

示例:
"混合用户群 (企业 + 开发者 + 个人)"
→ MVP 需要: API 优先 (开发者友好)
→ MVP 需要: 简单的预付费模式 (个人可接入)
→ MVP 不需要: 复杂的企业审批流程
```

#### 问题3: 最小可行版本 (MVP)
```
问: "如果时间有限,你会砍掉哪些功能?"

目的:
- 明确核心功能 vs 锦上添花
- 控制范围蔓延
- 快速上线验证

技巧: 使用"如果不"测试
"如果不做功能 X,项目还能运行吗?"
- 如果能 → X 不是 MVP 的核心
- 如果不能 → X 是必需的

示例:
Vibe Coding 项目砍掉的功能:
❌ 用户注册/登录 → 改为手动配置 API Key
❌ 流式响应 → 改为简单 JSON
❌ 多模型路由 → 只支持2个硬编码模型
❌ 订阅计费 → 改为预付费余额

结果: 范围缩小 60%,工期明确 6-8周
```

#### 问题4: 技术栈偏好
```
问: "团队对哪些技术栈更熟悉? 有什么禁忌?"

目的:
- 基于团队能力决策,不盲目追求新技术
- 识别技术风险 (学习曲线、招聘难度)

决策矩阵:
| 技术栈 | 团队熟悉度 | 生态成熟度 | 维护成本 | 推荐度 |
|-------|-----------|-----------|---------|--------|
| Go | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅ |
| Python | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅ |
| Rust | ⭐ | ⭐⭐⭐ | ⭐⭐ | ❌ (团队不熟悉) |

示例:
"团队后端偏好 Go,无 Python 禁忌"
→ 决策: Go Gateway (高并发,低资源)
→ 决策: vLLM 推理引擎 (Python 隔离,团队可接受)
```

#### 问题5: 成功指标
```
问: "上线后如何判断 MVP 是否成功?"

目的:
- 将模糊目标转化为可验证指标
- 明确监控需求
- 避免过度设计

好的成功指标 (SMART):
- Specific: 具体的 (不是"用户喜欢")
- Measurable: 可测量的 (不是"感觉快")
- Achievable: 可实现的 (不是"100万用户")
- Relevant: 相关的 (与项目目标一致)
- Time-bound: 有时限的 (例如"上线后1个月")

示例:
❌ 模糊: "用户觉得好用"
✅ 具体: "上线1个月内,10+ 注册用户,1000+ 成功请求,稳定性 > 95%"

影响:
- 需要监控 (Prometheus + Grafana)
- 需要日志 (请求追踪)
- MVP 不需要: 复杂的分析仪表盘 (手动查看 Prometheus 足够)
```

**实战效果**:
- 需求完整度提升 **80%**
- 范围缩小 **60%** (砍掉不必要功能)
- 风险降低 **70%** (基于团队能力决策)

---

### 🔍 技巧3: Metis 预审技术

**核心价值**: 在生成计划前识别遗漏

**最佳实践**:

#### ✅ DO: 提供充分上下文
```
好的 Metis 咨询:
"我正在规划一个 AI 算力交易平台

用户目标: 国内全覆盖,不依赖海外 GPU
时间约束: 8周 MVP 上线
团队规模: 4-8人,Go 后端偏好

当前计划草稿:
[附上草稿或关键决策]

请帮我识别:
1. 我应该问但没问的问题
2. 可能的范围蔓延风险
3. 我假设但未验证的业务逻辑"
```

#### ❌ DON'T: 信息不足
```
差的 Metis 咨询:
"这是我的计划,请审查"
"帮我看看这个计划怎么样"

后果:
- Metis 只能做泛泛的建议
- 无法发现具体的遗漏
- 浪费一次咨询机会
```

**Metis 的威力**:
- 在 Vibe Coding 项目中,Metis 发现了 **5个我应该问但没问的问题**
- 包括: API 认证方式、模型版本管理、计费精度等关键技术决策
- 避免了后续的返工 (估计节省 4-8小时)

---

### 📝 技巧4: Agent-Executed QA Scenarios

**核心价值**: 每个任务必须有可执行的验证场景

**传统验收标准的问题**:
```
❌ "验证配置正确"
   → 谁验证? 怎么验证? 期望什么输出?

❌ "测试 API 请求"
   → 什么请求? 返回什么? 失败时怎么办?

❌ "确保日志正常"
   → 日志在哪里? 格式是什么?
```

**Agent-Executed QA Scenarios 格式**:
```
Scenario: [描述性名称]
  Tool: [Playwright / interactive_bash / Bash / curl]
  Preconditions: [前置条件]
  Steps:
    1. [具体操作,包含具体命令/选择器]
    2. [下一步操作,包含期望的中间状态]
    3. [断言,包含确切的期望值]
  Expected Result: [具体的、可观察的结果]
  Evidence: [证据路径,例如截图/输出文件]
```

**实战示例**:

#### 示例1: API 验证 (curl)
```yaml
Scenario: 健康检查端点返回 200
  Tool: Bash (curl)
  Preconditions: Gateway 服务运行在 localhost:8080
  Steps:
    1. curl -s -w "\n%{http_code}" http://localhost:8080/health
    2. 解析 HTTP 状态码
    3. 解析响应体 JSON
  Expected Result: HTTP 200, body 包含 {"status":"healthy","version":"1.0.0"}
  Evidence: 响应体保存到 .sisyphus/evidence/task-0-health-check.json
```

#### 示例2: 配置文件验证
```yaml
Scenario: 无效配置文件返回错误
  Tool: Bash
  Preconditions: 存在无效配置文件 test-invalid.yaml
  Steps:
    1. ./gateway --config test-invalid.yaml
    2. 捕获 stderr 输出
    3. 检查退出代码
  Expected Result: 退出代码非 0,stderr 包含 "配置文件格式错误"
  Evidence: 错误输出保存到 .sisyphus/evidence/task-0-invalid-config.log
```

#### 示例3: 前端 UI 验证 (Playwright)
```yaml
Scenario: 登录成功后跳转到仪表盘
  Tool: Playwright
  Preconditions: Dev server 运行在 localhost:3000,测试用户已创建
  Steps:
    1. 导航到 http://localhost:3000/login
    2. 等待 input[name="email"] 可见 (timeout: 5s)
    3. 填写 input[name="email"] → "test@example.com"
    4. 填写 input[name="password"] → "ValidPass123!"
    5. 点击 button[type="submit"]
    6. 等待导航到 /dashboard (timeout: 10s)
    7. 断言 h1 文本包含 "Welcome"
  Expected Result: 仪表盘加载,显示欢迎信息
  Evidence: 截图保存到 .sisyphus/evidence/task-1-login-success.png
```

**关键原则**:
1. **具体选择器**: `.login-button`, not "登录按钮"
2. **具体数据**: `"test@example.com"`, not `"[email]"`
3. **具体断言**: `text contains "Welcome"`, not "验证成功"
4. **失败场景**: 每个功能至少 1 个成功 + 1 个失败场景
5. **证据路径**: 具体文件路径 (`.sisyphus/evidence/...`)

**反模式识别**:
| ❌ 反模式 | ✅ 正确做法 |
|---------|----------|
| "验证登录功能" | "填写表单 → 点击提交 → 断言跳转到 /dashboard" |
| "检查 API 返回正确数据" | "POST /api/users → 断言 HTTP 201 → 断言 response.id 是 UUID" |
| "测试错误处理" | "发送无效请求 → 断言 HTTP 400 → 断言错误消息包含 'Invalid email'" |

---

### 🚀 技巧5: 并行研究技术

**核心价值**: 同时研究多个技术栈,效率提升 3x

**适用场景**:
- 需要对比多个技术方案
- 需要了解多个不相关的库/框架
- 时间紧迫,需要快速决策

**操作流程**:
```bash
# 步骤1: 启动多个 Librarian (并行)
delegate_task(subagent_type="librarian", prompt="vLLM OpenAI 兼容模式配置", run_in_background=true)
delegate_task(subagent_type="librarian", prompt="OpenMeter 计费集成最佳实践", run_in_background=true)
delegate_task(subagent_type="librarian", prompt="Prometheus Go 客户端使用", run_in_background=true)

# 步骤2: 等待所有结果返回 (30分钟,不是90分钟)

# 步骤3: 综合分析,对比发现
# - 共同点: 都支持 HTTP API
# - 差异点: OpenMeter 免费版限流 10 QPS
# - 冲突点: vLLM 不支持流式 token 计费,需要自己实现
```

**效率对比**:
| 方式 | 时间 | 质量 |
|-----|------|------|
| 串行研究 | 90分钟 | ⭐⭐⭐ (容易遗忘前面的) |
| 并行研究 | 30分钟 | ⭐⭐⭐⭐⭐ (横向对比,发现冲突) |

**关键技巧**:
- ✅ 同时启动所有研究任务 (run_in_background=true)
- ✅ 等待所有结果返回后再做决策
- ✅ 对比不同技术的共性和差异
- ✅ 识别潜在的冲突点 (例如性能瓶颈)

---

### ⚖️ 技巧6: Oracle 架构咨询技术

**核心价值**: 在技术栈决策前获得专业架构建议

**最佳时机**:
- ✅ 有**多种架构选择**时 (例如 Go vs Python vs Java)
- ✅ 有**长期影响**的决策 (例如微服务 vs 单体)
- ✅ 需要**深度技术对比**时

**Oracle 咨询框架**:
```
"架构咨询: [方案A] vs [方案B]

项目背景:
- 业务类型: [例如 AI 算力交易]
- 规模预期: [例如 10K+ QPS]
- 团队能力: [例如 熟悉 Go,Python 一般]
- 约束条件: [例如 国内部署,8周上线]

请分析:
1. 两种架构的优劣对比
2. 在当前场景下的适配性
3. 团队学习曲线和维护成本
4. 潜在的技术风险
5. 推荐方案及理由"
```

**Oracle 咨询实例**:
```
"架构咨询: Go Gateway + vLLM vs Python FastAPI + vLLM

项目背景:
- 业务: 国内 AI 算力交易平台
- 规模: 预期 10K+ QPS,峰值可能 50K QPS
- 团队: Go 熟悉,Python 一般,无 Java 经验
- 约束: 国内云环境 (阿里云/腾讯云),8周 MVP 上线

请分析:
1. 两种架构的并发性能对比
2. 在国内云环境下的适配性 (镜像、生态、支持)
3. 团队学习曲线 (从 0 到能维护)
4. 长期维护成本 (招聘、社区、文档)
5. 推荐方案及理由"

Oracle 回复要点:
- Go 单机可处理 10K+ QPS (Python ~1K QPS)
- 国内云环境 Go 生态完善 (Docker 镜像、SDK、社区)
- Go 内存占用低 (50-100MB vs Python 500MB-1GB)
- 推荐: Go Gateway + vLLM (STRONGLY RECOMMENDED)
```

**不要这样用 Oracle**:
| ❌ 错误用法 | ✅ 正确用法 |
|-----------|----------|
| "如何实现 JWT 认证?" → 这是实现细节,用 Librarian | "JWT vs OAuth2,哪个更适合我们的场景?" |
| "哪个 Go web 框架最好?" → 这是库选择,用 Librarian | "gin vs echo,在我们的并发场景下哪个更合适?" |
| "帮我写代码" → Oracle 不写代码 | "这两种架构设计,哪个更容易扩展?" |

---

### 📋 技巧7: Momus 严格审查技术

**核心价值**: 确保工作计划零缺陷

**Momus 审查标准** (必须全部满足):
- ✅ 100% 文件引用可验证 (不存在或明确标注"将创建")
- ✅ 零关键文件验证失败
- ✅ ≥80% 任务有清晰的参考来源
- ✅ ≥90% 任务有具体的验收标准
- ✅ 零任务需要假设业务逻辑
- ✅ 清晰的整体图景和工作流理解
- ✅ 零关键红灯

**Momus 审查循环** (可能需要 2-3 轮):

#### 第1轮: 从"想法"到"可执行"
**常见问题**:
- 文件引用不存在 (例如引用了计划创建的文件,没标注)
- 验收标准模糊 ("验证配置正确" → 具体是什么命令?)
- 缺少具体测试数据 (API Key 格式? 请求示例?)

**修复重点**:
- 明确标注文件来源 ("将创建" 或 完整路径)
- 将所有验收标准改为具体命令
- 添加具体测试数据

#### 第2轮: 从"可执行"到"严谨"
**常见问题**:
- QA 场景只有成功路径,缺少失败场景
- 依赖关系描述不准确
- 部分 QA 场景过于笼统

**修复重点**:
- 每个任务添加失败场景 QA
- 重新审查依赖关系 (使用依赖矩阵)
- 细化 QA 步骤 (添加具体选择器、数据)

#### 第3轮: 从"严谨"到"零缺陷"
**目标**: Momus 返回 "OKAY"

**最终检查**:
```
□ 所有文件引用都明确?
□ 所有验收标准都是具体命令?
□ 每个 QA 都有成功 + 失败场景?
□ 每个任务都有 Agent 推荐配置?
□ 零业务逻辑假设?
```

**Momus 循环的 ROI**:
- 审查时间: 30分钟 (3轮 × 10分钟)
- 避免返工: **4-8小时**
- ROI: **8-16x**

---

## 执行阶段技巧

### 🔄 技巧8: TDD 循环技术

**来源**: test-driven-development 技能

**核心价值**: 测试先行,确保质量

**RED-GREEN-REFACTOR 循环**:

#### 🔴 RED: 写失败测试
```go
// 先写测试 (config_test.go)
func TestLoadConfig(t *testing.T) {
    config, err := LoadConfig("test.yaml")
    assert.NoError(t, err)
    assert.Equal(t, "qwen3-14b", config.Models[0].Name)
    assert.Equal(t, "http://localhost:8000", config.Models[0].Endpoint)
}

// 运行: go test ./config
// 结果: FAIL (config.go 不存在)
```

#### 🟢 GREEN: 最小实现通过测试
```go
// 实现 config.go (最小代码)
type Config struct {
    Models []ModelConfig
}

func LoadConfig(path string) (*Config, error) {
    data, _ := os.ReadFile(path)
    var config Config
    yaml.Unmarshal(data, &config)
    return &config, nil
}

// 运行: go test ./config
// 结果: PASS ✅
```

#### 🔄 REFACTOR: 清理代码
```go
// 重构: 添加验证
func LoadConfig(path string) (*Config, error) {
    if _, err := os.Stat(path); os.IsNotExist(err) {
        return nil, fmt.Errorf("配置文件不存在: %s", path)
    }
    
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("读取配置失败: %w", err)
    }
    
    var config Config
    if err := yaml.Unmarshal(data, &config); err != nil {
        return nil, fmt.Errorf("解析配置失败: %w", err)
    }
    
    if err := config.Validate(); err != nil {
        return nil, err
    }
    
    return &config, nil
}

// 运行: go test ./config
// 结果: PASS ✅ (仍然通过,但代码更健壮)
```

**TDD 的优势**:
- ✅ 强制思考接口设计 (先写测试,必须想清楚 API)
- ✅ 零回归风险 (每次改代码都有测试保护)
- ✅ 文档即测试 (测试用例就是使用示例)

**常见陷阱**:
| ❌ 错误做法 | ✅ 正确做法 |
|-----------|----------|
| 先写代码,再补测试 | 先写测试,再写代码 |
| 测试所有细节 | 测试公共行为,不测试私有方法 |
| Mock 一切 | 只 Mock 外部依赖 (数据库、HTTP) |

---

### 🐛 技巧9: 系统化调试技术

**来源**: systematic-debugging 技能

**核心价值**: 不要猜测,要验证

**调试流程**:

#### 步骤1: 明确问题
```
问题描述:
"vLLM 推理返回 500 错误"

具体化:
- 什么请求导致错误? (POST /v1/chat/completions)
- 错误消息是什么? ("Internal server error")
- 复现率? (100%,不是间歇性)
- 什么时候开始出现? (部署新版本后)
```

#### 步骤2: 假设生成 (不验证,不实现)
```
可能假设:
H1: vLLM 服务未启动
H2: 请求格式错误
H3: 模型文件损坏
H4: 网络连接问题
H5: vLLM 版本不兼容
```

#### 步骤3: 数据收集 (验证假设)
```bash
# 验证 H1: vLLM 服务未启动
curl http://localhost:8000/health
# → 返回 {"status":"healthy"},排除 H1

# 验证 H2: 请求格式错误
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen3-14b","messages":[{"role":"user","content":"hi"}]}'
# → 仍然 500,H2 不太可能

# 验证 H3: 模型文件损坏
ls -lh /models/qwen3-14b
# → 文件大小正常,但检查权限

# 验证 H4: 网络连接问题
docker logs vllm-container
# → 发现错误日志: "CUDA out of memory"

# 真实原因: GPU 内存不足,不是网络问题
```

#### 步骤4: 根因分析
```
根本原因:
- vLLM 容器默认分配 4GB GPU 内存
- Qwen3-14B 模型需要 ~8GB 内存
- 导致 OOM (Out Of Memory) 错误

解决方案:
- 增加 GPU 内存分配到 8GB
- 或使用量化版本 (Qwen3-14B-Int8,需要 ~4GB)
```

#### 步骤5: 验证修复
```bash
# 修复后验证
docker update vllm-container --gpus all
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen3-14b","messages":[{"role":"user","content":"hi"}]}'
# → 返回 200,响应正常 ✅

# 运行完整测试套件
go test ./integration/vllm
# → PASS ✅
```

**调试原则**:
1. **不要猜测**: 数据收集后再做结论
2. **控制变量**: 一次只验证一个假设
3. **保留日志**: 不要在没有日志的情况下重启
4. **最小复现**: 创建最小的复现用例

---

## 协作技巧

### 📝 技巧10: 原子提交技术

**来源**: git-master 技能

**核心价值**: 一个功能一个 commit,易于追踪和回滚

**原子提交原则**:
```
✅ 好的提交:
"feat(config): 添加 YAML 配置文件加载

- 实现 LoadConfig 函数
- 支持 model 和 server 配置
- 添加配置验证
- 添加单元测试

Files: config.go, config_test.go"

❌ 差的提交:
"update"

"添加一些功能"

"修复 bug"
```

**提交频率**:
- 每个小功能完成后立即提交
- 不要积累多个功能一起提交
- 不要在未完成的工作上提交 (除非使用 WIP 分支)

**提交信息格式** (Conventional Commits):
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type 类型**:
- `feat`: 新功能
- `fix`: Bug 修复
- `refactor`: 重构 (不改变行为)
- `test`: 添加测试
- `docs`: 文档更新
- `chore`: 构建/工具配置

**示例**:
```bash
# 好的提交
git add config.go config_test.go
git commit -m "feat(config): 添加 YAML 配置文件加载

- 实现 LoadConfig 函数支持 YAML 解析
- 添加配置验证 (必需字段、格式检查)
- 添加单元测试覆盖成功和失败场景
- 错误消息中包含具体字段名

Closes #0"

# 差的提交
git add .
git commit -m "update"
```

**Git 实用技巧**:

#### 查找引入 bug 的提交
```bash
# 二分查找
git bisect start
git bisect bad HEAD  # 当前版本有 bug
git bisect good <last-known-good-commit>
# Git 会自动切换到中间版本,你测试后标记 good/bad
# 最终找到引入 bug 的具体 commit

git bisect reset  # 完成后重置
```

#### 查看某行代码是谁写的
```bash
git blame config.go -L 10,15  # 查看 10-15 行是谁写的
```

#### 交互式变基 (整理提交历史)
```bash
git rebase -i HEAD~3  # 整理最近 3 个提交
# 可以: squash (合并), reword (修改消息), drop (删除)
```

---

## 效率提升技巧

### ⚡ 技巧11: 并行任务波次技术

**核心价值**: 将大任务分解为可并行执行的波次

**依赖分析**:
```
原始任务列表 (线性):
Task 1 → Task 2 → Task 3 → Task 4 → Task 5 → Task 6 → Task 7
总时间: 7天

优化后 (并行):
Wave 1: Task 1, Task 5 (无依赖)
  ↓
Wave 2: Task 2 (依赖1), Task 6 (依赖5)
  ↓
Wave 3: Task 3 (依赖2)
  ↓
Wave 4: Task 4 (依赖2,3), Task 7 (依赖6)

总时间: 4天 (节省 43%)
```

**依赖矩阵** (Vibe Coding 项目实例):

| Task | Depends On | Blocks | Can Parallelize With |
|------|------------|--------|---------------------|
| 0. Gateway 基础架构 | None | 1,2 | 1 (vLLM 部署) |
| 1. vLLM 推理服务部署 | None | 3 | 0 (Gateway) |
| 2. 认证中间件 | 0 | 4 | 3 (请求路由) |
| 3. 请求路由 | 1 | 4 | 2 (认证) |
| 4. OpenMeter 集成 | 2,3 | 5 | 无 |
| 5. Stripe 集成 | 4 | 6 | 无 |
| 6. 监控集成 | 5 | 7 | 无 |
| 7. 测试和优化 | 6 | None | 无 |

**并行波次**:
```
Wave 1 (可立即开始):
├── Task 0: Gateway 基础架构
└── Task 1: vLLM 部署

Wave 2 (Wave 1 完成后):
├── Task 2: 认证中间件 (依赖 0)
└── Task 3: 请求路由 (依赖 1)

Wave 3 (Wave 2 完成后):
└── Task 4: OpenMeter 集成 (依赖 2,3)

Wave 4 (Wave 3 完成后):
└── Task 5: Stripe 集成

Wave 5 (Wave 4 完成后):
└── Task 6: 监控集成

Wave 6 (Wave 5 完成后):
└── Task 7: 测试和优化
```

**并行加速比**:
- 线性执行: ~8周
- 并行执行: ~4.5周
- **节省: 43% 时间**

**关键路径** (最长的依赖链):
```
Task 0 → Task 2 → Task 4 → Task 5 → Task 6 → Task 7
(这是项目的最短完成时间,其他任务可以并行进行)
```

---

### 📊 技巧12: 证据驱动验证技术

**核心价值**: 所有验证都有具体证据,不依赖"感觉"或"看起来"

**证据类型**:

#### 1. 截图证据 (UI 测试)
```
Evidence: .sisyphus/evidence/task-1-login-success.png
Evidence: .sisyphus/evidence/task-1-login-failure.png

用途:
- 回顾时快速验证 UI 状态
- 对比前后变化 (before/after)
- 文档/演示材料
```

#### 2. 日志证据 (CLI/TUI 测试)
```
Evidence: .sisyphus/evidence/task-0-config-load.log
Evidence: .sisyphus/evidence/task-0-invalid-config-error.log

内容示例:
[2026-02-05 10:23:15] INFO: Loading config from config.yaml
[2026-02-05 10:23:15] INFO: Loaded 2 models: qwen3-14b, glm-4
[2026-02-05 10:23:15] INFO: Gateway starting on port 8080
```

#### 3. 响应证据 (API 测试)
```
Evidence: .sisyphus/evidence/task-2-create-user-response.json

内容示例:
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "test@example.com",
  "name": "Test User",
  "balance": 1000000,
  "created_at": "2026-02-05T10:23:15Z"
}
```

#### 4. 指标证据 (性能测试)
```
Evidence: .sisyphus/evidence/task-7-benchmark-result.txt

内容示例:
Requests per second:    1234 [#/sec]
Time per request:       81ms [ms]
Failed requests:        0
```

**证据命名规范**:
```
.sisyphus/evidence/task-{N}-{scenario-slug}.{ext}

例如:
task-0-health-check-success.json
task-0-invalid-config-error.log
task-1-login-success.png
task-1-login-failure.png
task-2-create-user-response.json
task-7-stress-test-1000rps.txt
```

**证据的价值**:
- ✅ 可审计: 每个验证都有证据文件
- ✅ 可回溯: 可以查看历史测试结果
- ✅ 可演示: 证据文件可直接用于演示
- ✅ 可调试: 失败时,证据文件是诊断依据

---

## 🎯 技巧总结表

| 技巧 | 适用阶段 | 效果提升 | 使用频率 |
|-----|---------|---------|---------|
| **技能优先原则** | 所有阶段 | 避免返工,质量提升 | 每次任务开始前 |
| **5个关键问题** | 规划阶段 | 需求完整度 +80% | 新项目/功能开始时 |
| **Metis 预审** | 规划阶段 | 识别遗漏问题 | 生成工作计划前 |
| **Agent-Executed QA** | 规划/执行 | 验证标准化 | 每个任务 |
| **并行研究** | 规划阶段 | 效率 +3x | 多技术对比时 |
| **Oracle 咨询** | 规划阶段 | 架构决策正确性 | 架构选型时 |
| **Momus 严格审查** | 规划阶段 | 计划零缺陷 | 生成工作计划后 |
| **TDD 循环** | 执行阶段 | 零回归风险 | 实现功能时 |
| **系统化调试** | 执行阶段 | 快速定位问题 | 遇到 bug 时 |
| **原子提交** | 执行阶段 | Git 历史清晰 | 每个小功能完成 |
| **并行波次** | 执行阶段 | 时间节省 43% | 大任务分解时 |
| **证据驱动** | 所有阶段 | 可审计可回溯 | 每次验证 |

---

## 📚 推荐学习路径

### 初学者 (刚接触 Claude Code)
1. **技巧1**: 技能优先原则 (最重要)
2. **技巧2**: 5个关键问题 (最常用)
3. **技巧8**: TDD 循环 (质量保证)

### 进阶者 (有实战经验)
4. **技巧3**: Metis 预审 (避免遗漏)
5. **技巧9**: 系统化调试 (效率提升)
6. **技巧10**: 原子提交 (Git 纪律)

### 高级者 (追求极致)
7. **技巧4**: Agent-Executed QA (标准化)
8. **技巧5**: 并行研究 (效率 3x)
9. **技巧11**: 并行波次 (时间优化)

### 专家 (架构师角色)
10. **技巧6**: Oracle 咨询 (架构决策)
11. **技巧7**: Momus 严格审查 (零缺陷)
12. **技巧12**: 证据驱动 (可审计)

---

**文档版本**: v1.0
**最后更新**: 2026-02-05
**来源**: Vibe Coding 项目实战经验

**相关文档**:
- `01-planning-phase.md` - 规划阶段详细记录
- `02-development-phase.md` - 开发阶段 (待创建)
