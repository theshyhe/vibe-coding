# Vibe Coding MVP - 工作计划

## TL;DR

> **Quick Summary**: 构建Vibe Coding平台MVP，部署开源大模型（Qwen/GLM）并通过Go Gateway提供OpenAI兼容API，实现基础认证、限流、Token计费和监控
> 
> **Project**: Vibe Coding (AI算力交易平台)
> **Repository**: git@github.com:theshyhe/vibe-coding.git
> 
> **Deliverables**:
> - Go Gateway API（OpenAI兼容 /v1/chat/completions）
> - vLLM推理引擎（双模型部署：Qwen3-14B + GLM-4）
> - JWT认证和内存限流
> - Token计数和计费集成（OpenMeter + Stripe）
> - 基础监控（Prometheus + Grafana）
> 
> **Estimated Effort**: Medium (6-8 weeks, 质量优先)
> **Parallel Execution**: YES - 3 waves
> **Critical Path**: 基础设施 → vLLM部署 → Gateway开发 → 计费集成

---

## Context

### Original Request
项目名称：Vibe Coding
用户目标：构建AI算力交易平台，部署开源大模型矩阵，向用户提供算力服务（类似Hugging Face Inference API）
GitHub仓库：git@github.com:theshyhe/vibe-coding.git

### Interview Summary
**Key Discussions**:
- **目标用户**: 混合模式（企业客户 + 开发者 + 个人用户）
- **模型矩阵**: 主流全家桶（DeepSeek, Qwen, Llama, Mistral, Gemma）
- **部署策略**: 分阶段扩展（前期小规模4-8 GPU → 后期大规模）
- **技术栈**: Go Gateway + Python vLLM（经Oracle验证为正确选择）
- **团队规模**: 中团队（4-8人），需要模块化设计
- **测试策略**: Agent-Executed QA（不写单元测试）
- **时间约束**: 质量优先，6-8周可接受

**Research Findings**:
- **vLLM**: 生产级推理引擎，PagedAttention技术，2-24x吞吐量提升，已被Together AI、Fireworks AI验证
- **Go网关**: 适合IO密集型LLM网关，Goroutine支持万级并发，内存小（10-30MB），云原生生态成熟
- **OpenMeter**: 专为LLM设计的计费系统，支持实时Token追踪和Stripe集成
- **监控方案**: Langfuse（开源详细追踪）或Prometheus + Grafana（云原生标准）

### Metis Review
**Identified Gaps** (已整合):
- **关键决策需要明确**（见"待决策事项"部分）
- **MVP范围锁定**（见"Must NOT Have"部分）
- **验收标准缺失**（已补充详细的curl断言）
- **边缘情况处理**（已添加失败模式测试）

---

## Work Objectives

### Core Objective
构建Vibe Coding平台MVP：部署两大核心模型（Qwen3-14B + GLM-4），通过Go Gateway提供OpenAI兼容API，实现基础认证、限流、Token计费和监控，验证商业模式和技术架构。

### Concrete Deliverables
- **基础设施**: Docker Compose配置（vLLM + Gateway + OpenMeter + Prometheus）
- **推理引擎**: vLLM单模型部署（OpenAI兼容服务器模式）
- **API网关**: Go Gateway（/v1/chat/completions、JWT认证、内存限流）
- **计费系统**: Token计数 + OpenMeter集成 + Stripe预付费钱包
- **监控系统**: Prometheus metrics + Grafana dashboard
- **文档**: API文档、部署文档、运维手册

### Definition of Done
- [ ] `curl http://localhost:8080/v1/chat/completions` 返回有效响应
- [ ] 无效JWT返回401，超过限流返回429
- [ ] Token计数准确（±1%误差）
- [ ] Stripe checkout创建API key并扣费
- [ ] Prometheus metrics可查询（请求率、延迟、错误率）
- [ ] 负载测试通过（100并发请求，P95延迟<5s）
- [ ] 所有失败模式有处理（vLLM宕机、GPU OOM、网络超时）

### Must Have
- ✅ Go Gateway提供OpenAI兼容的/v1/chat/completions端点
- ✅ JWT认证（API key → JWT token）
- ✅ 内存限流（10 req/min per API key）
- ✅ Token计数（vLLM reported count）
- ✅ OpenMeter集成（记录Token使用）
- ✅ Stripe预付费钱包（用户充值$10 → 获得Token额度）
- ✅ Prometheus监控（请求率、延迟、错误率）
- ✅ 健康检查端点（/health）
- ✅ 错误处理（4xx/5xx with proper error messages）

### Must NOT Have (Guardrails - 防止AI过度工程化)

**Phase 1 MVP 排除项**:
- ❌ **用户管理UI**（API-only操作，无Web界面）
- ❌ **流式响应**（仅非流式，简化Token计数）
- ❌ **聊天历史存储**（无状态，不持久化对话）
- ❌ **多模型路由**（仅2个hardcode模型：Qwen3-14B + GLM-4，不支持动态切换）
- ❌ **智能路由**（无复杂路由逻辑，按模型参数直接转发）
- ❌ **自动伸缩**（静态GPU分配，2个模型=2个GPU）
- ❌ **请求队列**（vLLM满载时fail-fast，返回429）
- ❌ **订阅制计费**（Phase 1仅预付费钱包）
- ❌ **复杂定价**（单一费率，无动态定价）
- ❌ **实时使用Dashboard**（无前端可视化）
- ❌ **SSO/企业集成**（仅API key认证）
- ❌ **用户自助注册**（手动配置API key）

**Phase 2 再考虑**:
- ➕ MiniMax, DeepSeek, Llama 4等模型
- ➕ 订阅制 + 超额计费
- ➕ 自定义Python API
- ➕ 自动化注册页
- ➕ 智能路由、自动伸缩

---

## ✅ 用户确认的决策（已锁定）

#### 1. 实施策略: **分阶段实现** ⭐
- **Phase 1 (MVP, 6-8周)**: 简化版，快速验证核心功能
- **Phase 2 (迭代, +4-6周)**: 增强功能，扩展到国内全覆盖

**Phase 1 范围**:
- ✅ 2个核心模型: Qwen3-14B + GLM-4
- ✅ 单一计费模式: 预付费钱包
- ✅ 手动配置API key（无注册页）
- ✅ OpenAI兼容vLLM服务器

**Phase 2 增强**:
- ➕ 扩展模型: MiniMax + DeepSeek + 其他开源模型
- ➕ 增加计费: 订阅制 + 超额计费
- ➕ 自定义Python API（如需要）
- ➕ 自动化用户注册

#### 2. 模型策略: **国内全覆盖**
- **Phase 1 MVP**: Qwen3-14B (主力) + GLM-4 (中文优化)
- **Phase 2 迭代**: 增加 MiniMax + DeepSeek + Llama 4 + Mistral

#### 3. 计费模式: **分阶段实现**
- **Phase 1 MVP**: 仅预付费钱包（Stripe Checkout，简单集成）
- **Phase 2 迭代**: 增加订阅制（Stripe Billing）+ 超额计费逻辑

#### 4. 用户注册: **手动配置** ✅
- 无需前端注册页，节省开发时间
- 开发者通过管理API手动创建API key
- Phase 2可添加自动化注册

#### 5. vLLM服务模式: **OpenAI兼容服务器** ✅
- 使用 `vllm serve --engine openai`
- Gateway只需代理，无需自定义API
- Phase 2可迁移到自定义Python API

---

## Verification Strategy (MANDATORY)

> **UNIVERSAL RULE: ZERO HUMAN INTERVENTION**
>
> ALL tasks in this plan MUST be verifiable WITHOUT any human action.
> 所有验证由Agent通过工具执行（curl、Playwright、interactive_bash等）。

### Test Decision
- **Infrastructure exists**: NO (全新项目)
- **Automated tests**: NO (Agent-Executed QA only)
- **Framework**: None
- **Agent-Executed QA**: YES - 所有功能通过curl/Bash/Playwright验证

### Agent-Executed QA Scenarios (MANDATORY)

**每个任务必须包含以下类型的QA场景**:

#### 类型1: API端点验证（curl）
```bash
# 健康检查
curl -s http://localhost:8080/health | jq '.status'
# Assert: Output equals "ok"

# 聊天完成
curl -s -X POST http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $VALID_JWT" \
  -d '{
    "model": "qwen3-14b",
    "messages": [{"role": "user", "content": "Say hello"}],
    "max_tokens": 50
  }' | jq '.choices[0].message.content'
# Assert: Contains "hello" or "Hello"
```

#### 类型2: 认证和授权验证
```bash
# 无效token应返回401
curl -s -X POST http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer invalid_token" \
  -d '{"model": "qwen3-14b", "messages": [...]}' | jq '.error'
# Assert: HTTP 401, error.message contains "Unauthorized"

# 超过限流应返回429
for i in {1..15}; do
  curl -s -w "%{http_code}\n" -o /dev/null \
    -H "Authorization: Bearer $VALID_JWT" \
    -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}' \
    http://localhost:8080/v1/chat/completions
done | grep -c "429"
# Assert: At least 5 requests return 429 (assuming 10 RPM limit)
```

#### 类型3: Token计数验证
```bash
# Token计数在响应中
RESPONSE=$(curl -s -X POST http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer $VALID_JWT" \
  -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Count these tokens"}]}')
echo $RESPONSE | jq '.usage.total_tokens'
# Assert: total_tokens > 0
```

#### 类型4: 失败模式验证
```bash
# vLLM宕机应返回503
docker stop vllm-container
curl -s -w "%{http_code}" -o /dev/null \
  -H "Authorization: Bearer $VALID_JWT" \
  -d '{"model": "qwen3-14b", "messages": [...]}' \
  http://localhost:8080/v1/chat/completions
# Assert: Returns 503
docker start vllm-container
```

---

## Execution Strategy

### Parallel Execution Waves

```
Wave 1 (Start Immediately):
├── Task 1: 基础设施准备（Docker Compose、GPU验证）
└── Task 2: vLLM部署和验证

Wave 2 (After Wave 1):
├── Task 3: Go Gateway开发（核心代理+认证）
├── Task 4: JWT认证和限流实现
└── Task 5: OpenMeter集成和Token计数

Wave 3 (After Wave 2):
├── Task 6: Stripe预付费钱包集成
├── Task 7: Prometheus监控和Grafana Dashboard
└── Task 8: 最小化注册页（静态HTML）

Critical Path: Task 1 → Task 2 → Task 3 → Task 6
Parallel Speedup: ~50% faster than sequential (适合4-8人团队)
```

### Dependency Matrix

| Task | Depends On | Blocks | Can Parallelize With |
|------|------------|--------|---------------------|
| 1 | None | 2 | None (基础设施优先) |
| 2 | 1 | 3, 4 | None |
| 3 | 2 | 6 | 4, 5 |
| 4 | 2 | 6 | 3, 5 |
| 5 | 2 | None | 3, 4 |
| 6 | 3, 4 | None | 7, 8 |
| 7 | 2 | None | 6, 8 |
| 8 | 6 | None | 7 |

### Agent Dispatch Summary

| Wave | Tasks | Recommended Agents |
|------|-------|-------------------|
| 1 | 1, 2 | delegate_task(category="unspecified-high", load_skills=[]) |
| 2 | 3, 4, 5 | delegate_task parallel (3 agents) |
| 3 | 6, 7, 8 | delegate_task parallel (3 agents) |

---

## TODOs

> Implementation + Agent-Executed QA = ONE Task. Never separate.
> 每个任务包含: 推荐Agent Profile + 并行化信息 + 详细的QA场景

---

### [ ] 0. [PRE-REQ] 项目初始化和环境准备

**What to do**:
- 克隆GitHub仓库：`git clone git@github.com:theshyhe/vibe-coding.git`
- 初始化Go模块：`go mod init github.com/theshyhe/vibe-coding/gateway`
- 安装Go 1.21+、Python 3.10+、Docker、Docker Compose
- 配置NVIDIA GPU驱动（nvidia-docker2）
- 验证GPU可用性（`nvidia-smi`）
- 创建项目目录结构

**Must NOT do**:
- 不要跳过GPU驱动验证（vLLM需要CUDA）
- 不要使用Windows WSL2部署（已知兼容性问题）

**Recommended Agent Profile**:
- **Category**: `unspecified-low`
  - Reason: 基础环境设置， straightforward
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: NO (必须首先完成)
- **Parallel Group**: Sequential (Wave 0)
- **Blocks**: Task 1, 2
- **Blocked By**: None

**References**:
- **外部**: NVIDIA Docker安装指南 - https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html
- **外部**: vLLM环境要求 - https://docs.vllm.ai/en/latest/getting_started/installation.html
- **外部**: Go安装指南 - https://go.dev/doc/install

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] Go version >= 1.21: `go version` → contains "go1.21"
- [ ] Python version >= 3.10: `python --version` → contains "3.10"
- [ ] Docker installed: `docker --version` → contains version number
- [ ] nvidia-docker2 working: `docker run --rm --gpus all nvidia/cuda:11.8.0-base-ubuntu22.04 nvidia-smi` → shows GPU info
- [ ] Git repository initialized: `cd vibe-coding && git status` → shows clean repo
- [ ] Go module created: `cat go.mod` → contains "module github.com/theshyhe/vibe-coding/gateway"
- [ ] Project structure created: `ls -la` → shows dirs (cmd/, pkg/, deployments/, configs/)

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Verify GPU is accessible from Docker
  Tool: Bash (docker)
  Preconditions: NVIDIA drivers installed, nvidia-docker2 configured
  Steps:
    1. docker run --rm --gpus all nvidia/cuda:11.8.0-base-ubuntu22.04 nvidia-smi
    2. Assert: Command output contains "GPU Name"
    3. Assert: Command output contains "CUDA Version: 11.8"
    4. Assert: Exit code is 0
  Expected Result: Docker can access NVIDIA GPU
  Evidence: Command output saved to .sisyphus/evidence/task-0-gpu-check.txt
\`\`\`

---

### [ ] 1. 基础设施准备（Docker Compose配置）

**What to do**:
- 创建Docker Compose配置文件（vLLM + Gateway + OpenMeter + Prometheus + Grafana）
- 配置网络（vLLM和Gateway在同一网络）
- 配置volume（持久化OpenMeter数据、Prometheus metrics）
- 创建环境变量文件（.env.template）

**Must NOT do**:
- 不要硬编码模型路径（使用环境变量MODEL_NAME）
- 不要暴露vLLM端口到公网（仅内部网络）
- 不要在生产环境使用默认密码

**Recommended Agent Profile**:
- **Category**: `unspecified-low`
  - Reason: 配置文件编写，模式化任务
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: NO
- **Parallel Group**: Wave 1 (with Task 2)
- **Blocks**: Task 3, 4, 5
- **Blocked By**: Task 0

**References**:
- **外部**: Docker Compose规范 - https://docs.docker.com/compose/compose-file/compose-file-v3/
- **外部**: vLLM Docker部署 - https://docs.vllm.ai/en/latest/serving/deploying_with_docker.html
- **外部**: OpenMeter Docker部署 - https://openmeter.io/docs/integration/self-hosted
- **外部**: Prometheus Docker配置 - https://prometheus.io/docs/prometheus/latest/installation/

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] docker-compose.yml created: `test -f docker-compose.yml` → exit code 0
- [ ] .env.template created: `test -f .env.template` → exit code 0
- [ ] Config validates: `docker-compose config` → no errors, shows service configuration
- [ ] Network defined: `docker-compose config | grep networks` → shows "llm-network"
- [ ] Volume defined for OpenMeter: `docker-compose config | grep openmeter_data` → shows volume mount

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Docker Compose configuration is valid
  Tool: Bash (docker-compose)
  Preconditions: docker-compose.yml exists
  Steps:
    1. docker-compose config
    2. Assert: Output contains "services:" (at least 5 services)
    3. Assert: Output contains "vllm:" (vLLM service defined)
    4. Assert: Output contains "gateway:" (Gateway service defined)
    5. Assert: Output contains "openmeter:" (OpenMeter service defined)
    6. Assert: Output contains "networks:" (network defined)
    7. Assert: Exit code is 0
  Expected Result: Docker Compose configuration is valid and parseable
  Evidence: docker-compose config output saved to .sisyphus/evidence/task-1-compose-config.txt
\`\`\`

---

### [ ] 2. vLLM双模型部署和验证

**What to do**:
- **Model 1**: 部署Qwen3-14B（port 8000, GPU 0）
- **Model 2**: 部署GLM-4（port 8001, GPU 1）
- 编写vLLM Dockerfile或使用官方镜像
- 配置vLLM启动参数（--model、--port、--gpu-memory-utilization）
- 部署2个vLLM容器
- 验证vLLM OpenAI兼容API（/v1/chat/completions、/health）
- 性能基准测试（单请求延迟、吞吐量）

**Must NOT do**:
- 不要使用`--latest`标签（vLLM版本演进快，锁定版本如v0.6.4.post1）
- 不要忽略健康检查端点（Gateway需要依赖检查）
- 不要跳过性能基准（需要知道单GPU吞吐量上限）
- **不要让2个模型竞争GPU资源**（每个模型绑定独立GPU）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: AI/ML组件部署，需要GPU专业知识
- **Skills**: `[]`
  - No special skills needed, but requires GPU understanding

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 1 in Wave 1)
- **Parallel Group**: Wave 1
- **Blocks**: Task 3, 4
- **Blocked By**: Task 0

**References**:
- **外部**: vLLM官方文档 - https://docs.vllm.ai/en/latest/
- **外部**: vLLM OpenAI兼容服务器 - https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html
- **外部**: Qwen3模型卡片 - https://huggingface.co/Qwen/Qwen3-14B (如选择Qwen)
- **Pattern**: vLLM启动命令格式（根据文档确认）

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] vLLM Qwen container running: `docker ps | grep vllm-qwen` → shows container, status "Up"
- [ ] vLLM GLM container running: `docker ps | grep vllm-glm` → shows container, status "Up"
- [ ] Qwen health check passes: `curl -s http://localhost:8000/health | jq '.status'` → equals "ok"
- [ ] GLM health check passes: `curl -s http://localhost:8001/health | jq '.status'` → equals "ok"
- [ ] Qwen chat completion works: `curl -s -X POST http://localhost:8000/v1/chat/completions -H "Content-Type: application/json" -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Hello"}], "max_tokens": 10}' | jq '.choices[0].message.content'` → contains "hello" or "Hello"
- [ ] GLM chat completion works: `curl -s -X POST http://localhost:8001/v1/chat/completions -H "Content-Type: application/json" -d '{"model": "glm-4", "messages": [{"role": "user", "content": "你好"}], "max_tokens": 10}' | jq '.choices[0].message.content'` → contains Chinese greeting
- [ ] Response time acceptable: `time curl ...` → first request <5s (cold start), subsequent <1s
- [ ] Token count returned: `curl ... | jq '.usage.total_tokens'` → integer > 0

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: vLLM responds to health check
  Tool: Bash (curl)
  Preconditions: vLLM container running on port 8000
  Steps:
    1. curl -s http://localhost:8000/health
    2. Assert: HTTP status is 200
    3. Assert: Response JSON is valid
    4. Assert: response.status equals "ok"
  Expected Result: vLLM is healthy and ready
  Evidence: .sisyphus/evidence/task-2-health-check.txt

Scenario: vLLM generates chat completion
  Tool: Bash (curl)
  Preconditions: vLLM container running, model loaded
  Steps:
    1. curl -s -X POST http://localhost:8000/v1/chat/completions \
         -H "Content-Type: application/json" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "What is 2+2?"}], "max_tokens": 50}'
    2. Assert: HTTP status is 200
    3. Assert: response.choices[0].message.content contains "4" or "four"
    4. Assert: response.usage.total_tokens > 0
    5. Assert: response.model equals "qwen3-14b"
  Expected Result: vLLM returns valid chat completion
  Evidence: .sisyphus/evidence/task-2-chat-completion.json

Scenario: vLLM handles concurrent requests
  Tool: Bash (curl + background processes)
  Preconditions: vLLM container running
  Steps:
    1. for i in {1..10}; do
         curl -s -X POST http://localhost:8000/v1/chat/completions \
           -H "Content-Type: application/json" \
           -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}], "max_tokens": 10}' \
           -o /tmp/response_$i.json &
       done
    2. wait  # Wait for all background jobs
    3. Assert: All 10 requests returned 200 status
    4. Assert: All responses have valid usage.total_tokens
  Expected Result: vLLM handles concurrent requests without errors
  Evidence: .sisyphus/evidence/task-2-concurrent-requests.txt

Scenario: vLLM returns error for invalid request
  Tool: Bash (curl)
  Preconditions: vLLM container running
  Steps:
    1. curl -s -X POST http://localhost:8000/v1/chat/completions \
         -H "Content-Type: application/json" \
         -d '{"model": "qwen3-14b", "messages": []}'
    2. Assert: HTTP status is 400
    3. Assert: response.error contains "messages" or "empty"
  Expected Result: vLLM validates input and returns proper error
  Evidence: .sisyphus/evidence/task-2-validation-error.txt
\`\`\`

---

### [ ] 3. Go Gateway核心开发（代理+认证）

**What to do**:
- 初始化Go模块（`go mod init ai-compute-platform/gateway`）
- 实现HTTP服务器（端口8080）
- 实现/v1/chat/completions端点（代理到vLLM）
- 实现JWT中间件（验证Bearer token）
- 实现/health端点（健康检查）
- 错误处理（4xx/5xx with proper JSON error responses）

**Must NOT do**:
- 不要在Gateway实现流式响应（MVP仅非流式）
- 不要硬编码vLLM URL（使用环境变量VLLM_ENDPOINT）
- 不要忽略错误传播（vLLM错误应透传给客户端）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: Go后端开发，核心业务逻辑
- **Skills**: `[]`
  - No special skills, but requires Go knowledge

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 4, 5 in Wave 2)
- **Parallel Group**: Wave 2
- **Blocks**: Task 6
- **Blocked By**: Task 2

**References**:
- **外部**: OpenAI API规范 - https://platform.openai.com/docs/api-reference/chat
- **外部**: Go JWT库（golang-jwt/jwt） - https://github.com/golang-jwt/jwt
- **外部**: Go HTTP服务器最佳实践 - https://golang.org/pkg/net/http/
- **外部**: vLLM API格式（参考Task 2的验证）

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] Gateway binary builds: `go build -o gateway cmd/main.go` → exit code 0, binary exists
- [ ] Gateway starts without errors: `./gateway` → listens on :8080
- [ ] Health check works: `curl -s http://localhost:8080/health | jq '.status'` → equals "ok"
- [ ] Proxies to vLLM: `curl -s -X POST http://localhost:8080/v1/chat/completions -H "Authorization: Bearer $VALID_JWT" -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'` → returns chat completion
- [ ] Rejects invalid token: `curl -s ... -H "Authorization: Bearer invalid" | jq '.error'` → contains "Unauthorized" and HTTP 401

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Gateway health check
  Tool: Bash (curl)
  Preconditions: Gateway running on port 8080
  Steps:
    1. curl -s http://localhost:8080/health
    2. Assert: HTTP status is 200
    3. Assert: response.status equals "ok"
  Expected Result: Gateway is healthy
  Evidence: .sisyphus/evidence/task-3-gateway-health.txt

Scenario: Gateway proxies valid request to vLLM
  Tool: Bash (curl)
  Preconditions: Gateway and vLLM running, VALID_JWT set
  Steps:
    1. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Content-Type: application/json" \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "What is 3+3?"}], "max_tokens": 50}'
    2. Assert: HTTP status is 200
    3. Assert: response.choices[0].message.content contains "6" or "six"
    4. Assert: response.usage.total_tokens > 0
  Expected Result: Gateway successfully proxies to vLLM
  Evidence: .sisyphus/evidence/task-3-proxy-success.txt

Scenario: Gateway rejects request without auth
  Tool: Bash (curl)
  Preconditions: Gateway running
  Steps:
    1. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Content-Type: application/json" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'
    2. Assert: HTTP status is 401
    3. Assert: response.error.message contains "Unauthorized" or "missing"
  Expected Result: Gateway enforces authentication
  Evidence: .sisyphus/evidence/task-3-auth-required.txt

Scenario: Gateway rejects invalid JWT
  Tool: Bash (curl)
  Preconditions: Gateway running
  Steps:
    1. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.invalid" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'
    2. Assert: HTTP status is 401
    3. Assert: response.error.message contains "invalid" or "Unauthorized"
  Expected Result: Gateway validates JWT signature
  Evidence: .sisyphus/evidence/task-3-invalid-jwt.txt

Scenario: Gateway returns 503 when vLLM is down
  Tool: Bash (curl, docker)
  Preconditions: Gateway and vLLM running
  Steps:
    1. docker stop vllm-container
    2. sleep 2
    3. curl -s -w "%{http_code}" -o /dev/null \
         -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'
    4. docker start vllm-container
    5. Assert: HTTP status is 503
    6. Assert: Response body contains "unavailable" or "vLLM"
  Expected Result: Gateway handles vLLM unavailability gracefully
  Evidence: .sisyphus/evidence/task-3-vllm-down.txt
\`\`\`

---

### [ ] 4. JWT认证和限流实现

**What to do**:
- 实现JWT生成逻辑（用于测试和初始API key创建）
- 实现JWT验证中间件（已在Task 3框架中，此处完善）
- 实现内存限流器（Token bucket算法，10 req/min per API key）
- 返回429状态码当超过限流
- 添加RateLimit-* headers to response

**Must NOT do**:
- 不要使用数据库存储限流状态（MVP用内存）
- 不要实现持久化限流（服务重启后重置）
- 不要忽略并发竞态条件（使用sync.Map或atomic）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: 限流逻辑，并发安全
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 3, 5 in Wave 2)
- **Parallel Group**: Wave 2
- **Blocks**: Task 6
- **Blocked By**: Task 2

**References**:
- **外部**: Token bucket算法 - https://en.wikipedia.org/wiki/Token_bucket
- **外部**: Go rate limiting库（uber-go/rate） - https://github.com/uber-go/rate
- **外部**: HTTP 429规范 - https://datatracker.ietf.org/doc/html/rfc6585#section-4

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] JWT can be generated: `go run cmd/jwtgen/main.go` → outputs valid JWT
- [ ] JWT can be verified: `go run cmd/jwtverify/main.go $TOKEN` → outputs claims
- [ ] Rate limiting works: 15 requests → first 10 return 200, last 5 return 429
- [ ] RateLimit headers present: `curl -I ... | grep RateLimit` → shows RateLimit-Limit, RateLimit-Remaining, RateLimit-Reset

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Generate and verify JWT
  Tool: Bash (go run)
  Preconditions: Go installed, JWT code written
  Steps:
    1. TOKEN=$(go run cmd/jwtgen/main.go --user-id test-user --api-key test-key)
    2. echo $TOKEN | grep -q "eyJ"  # Base64 encoded
    3. go run cmd/jwtverify/main.go $TOKEN
    4. Assert: Output contains "user_id: test-user"
    5. Assert: Output contains "api_key: test-key"
    6. Assert: Exit code is 0
  Expected Result: JWT generation and verification works
  Evidence: .sisyphus/evidence/task-4-jwt-flow.txt

Scenario: Rate limiting enforces 10 RPM
  Tool: Bash (curl loop)
  Preconditions: Gateway running with rate limiting, VALID_JWT set
  Steps:
    1. SUCCESS_COUNT=0
    2. RATE_LIMIT_COUNT=0
    3. for i in {1..15}; do
         STATUS=$(curl -s -w "%{http_code}" -o /dev/null \
           -X POST http://localhost:8080/v1/chat/completions \
           -H "Authorization: Bearer $VALID_JWT" \
           -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}], "max_tokens": 5}')
         if [ "$STATUS" = "200" ]; then
           SUCCESS_COUNT=$((SUCCESS_COUNT + 1))
         elif [ "$STATUS" = "429" ]; then
           RATE_LIMIT_COUNT=$((RATE_LIMIT_COUNT + 1))
         fi
         sleep 0.1  # Small delay between requests
       done
    4. Assert: $SUCCESS_COUNT -le 10  # First 10 should succeed
    5. Assert: $RATE_LIMIT_COUNT -ge 5  # Last 5 should be rate limited
  Expected Result: Rate limiting allows 10 requests/min, rejects excess
  Evidence: .sisyphus/evidence/task-4-rate-limit.txt

Scenario: Rate limit resets after 1 minute
  Tool: Bash (curl, sleep)
  Preconditions: Gateway running, VALID_JWT set, rate limit reached
  Steps:
    1. # Exhaust rate limit (10 requests)
    2. for i in {1..10}; do
         curl -s -o /dev/null -X POST http://localhost:8080/v1/chat/completions \
           -H "Authorization: Bearer $VALID_JWT" \
           -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'
       done
    3. # Next request should be 429
    4. STATUS=$(curl -s -w "%{http_code}" -o /dev/null \
         -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}')
    5. Assert: $STATUS = "429"
    6. # Wait 61 seconds for reset
    7. sleep 61
    8. # Request should now succeed
    9. STATUS=$(curl -s -w "%{http_code}" -o /dev/null \
         -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}')
    10. Assert: $STATUS = "200"
  Expected Result: Rate limit resets after 1 minute
  Evidence: .sisyphus/evidence/task-4-rate-limit-reset.txt

Scenario: RateLimit headers are present
  Tool: Bash (curl -I)
  Preconditions: Gateway running, VALID_JWT set
  Steps:
    1. curl -I -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}' \
         | grep -i "ratelimit"
    2. Assert: Output contains "RateLimit-Limit: 10"
    3. Assert: Output contains "RateLimit-Remaining" (value <= 10)
    4. Assert: Output contains "RateLimit-Reset" (Unix timestamp)
  Expected Result: RateLimit headers inform client of quota
  Evidence: .sisyphus/evidence/task-4-ratelimit-headers.txt
\`\`\`

---

### [ ] 5. OpenMeter集成和Token计数

**What to do**:
- 部署OpenMeter容器（Docker Compose已配置）
- 实现Gateway → OpenMeter的HTTP调用（记录Token使用）
- 提取vLLM响应中的usage字段（prompt_tokens, completion_tokens, total_tokens）
- 发送事件到OpenMeter（/v1/events endpoint）
- 验证OpenMeter中记录了Token使用

**Must NOT do**:
- 不要阻塞用户响应等待OpenMeter确认（异步发送）
- 不要忽略OpenMeter错误（记录日志但不影响请求）
- 不要重复计数（确保vLLM和Gateway计数一致）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: 第三方集成，异步逻辑
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 3, 4 in Wave 2)
- **Parallel Group**: Wave 2
- **Blocks**: Task 6
- **Blocked By**: Task 2

**References**:
- **外部**: OpenMeter HTTP API - https://openmeter.io/docs/reference/api
- **外部**: OpenMeter事件规范 - https://openmeter.io/docs/concepts/events
- **外部**: vLLM响应格式（usage字段） - https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] OpenMeter running: `curl -s http://localhost:8888/health | jq '.status'` → equals "ok"
- [ ] Event sent successfully: Gateway logs show "OpenMeter event sent: 200"
- [ ] Usage recorded in OpenMeter: `curl -s http://localhost:8888/v1/users/$USER_ID/events | jq '.events | length'` → > 0
- [ ] Token count matches vLLM: OpenMeter total_tokens equals vLLM response.usage.total_tokens

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: OpenMeter is accessible
  Tool: Bash (curl)
  Preconditions: OpenMeter container running on port 8888
  Steps:
    1. curl -s http://localhost:8888/health
    2. Assert: HTTP status is 200
    3. Assert: response.status equals "ok"
  Expected Result: OpenMeter is healthy
  Evidence: .sisyphus/evidence/task-5-openmeter-health.txt

Scenario: Gateway sends usage event to OpenMeter
  Tool: Bash (curl, grep logs)
  Preconditions: Gateway, vLLM, OpenMeter running, VALID_JWT set
  Steps:
    1. # Make a chat completion request
    2. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Count tokens"}]}' \
         -o /dev/null
    3. # Check Gateway logs for OpenMeter confirmation
    4. docker logs gateway-container 2>&1 | grep -i "openmeter" | tail -1
    5. Assert: Log contains "event sent" or "200"
    6. Assert: Log contains "tokens" or "usage"
  Expected Result: Gateway successfully sends usage event to OpenMeter
  Evidence: .sisyphus/evidence/task-5-event-sent.txt

Scenario: Usage is recorded in OpenMeter
  Tool: Bash (curl)
  Preconditions: Gateway, vLLM, OpenMeter running, VALID_JWT set, USER_ID extracted from JWT
  Steps:
    1. # Make a chat completion request
    2. RESPONSE=$(curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}')
    3. TOKENS=$(echo $RESPONSE | jq -r '.usage.total_tokens')
    4. sleep 2  # Wait for async event processing
    5. # Query OpenMeter for user events
    6. curl -s "http://localhost:8888/v1/users/$USER_ID/events?limit=1" | jq '.events[0]'
    7. Assert: Response contains event with type "llm_tokens" or similar
    8. Assert: Event properties contain total_tokens or similar field
  Expected Result: OpenMeter records token usage
  Evidence: .sisyphus/evidence/task-5-usage-recorded.json

Scenario: OpenMeter handles concurrent events
  Tool: Bash (curl loop)
  Preconditions: Gateway, vLLM, OpenMeter running, VALID_JWT set
  Steps:
    1. # Send 10 concurrent requests
    2. for i in {1..10}; do
         curl -s -X POST http://localhost:8080/v1/chat/completions \
           -H "Authorization: Bearer $VALID_JWT" \
           -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}], "max_tokens": 10}' \
           -o /dev/null &
       done
    3. wait
    4. sleep 3  # Wait for async processing
    5. # Query OpenMeter for user event count
    6. EVENT_COUNT=$(curl -s "http://localhost:8888/v1/users/$USER_ID/events" | jq '.events | length')
    7. Assert: $EVENT_COUNT = 10
  Expected Result: OpenMeter records all concurrent events
  Evidence: .sisyphus/evidence/task-5-concurrent-events.txt
\`\`\`

---

### [ ] 6. Stripe预付费钱包集成

**What to do**:
- [DECISION NEEDED: 确认计费模式] 假设选择"A. 预付费钱包"
- 创建Stripe产品（"1M Tokens for $10"）
- 实现Stripe Checkout会话创建
- 实现Stripe webhook处理（checkout.session.completed）
- Webhook handler创建API key并充值用户余额
- 实现余额查询端点（GET /v1/balance）
- 在Gateway中检查余额（拒绝余额不足的请求）

**Must NOT do**:
- 不要在客户端处理Stripe webhook（服务端验证签名）
- 不要忽略webhook签名验证（防止伪造请求）
- 不要允许负余额（余额不足时返回402 Payment Required）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: 支付集成，安全性关键
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 7, 8 in Wave 3)
- **Parallel Group**: Wave 3
- **Blocks**: None (integration task)
- **Blocked By**: Task 3, 4

**References**:
- **外部**: Stripe Checkout API - https://stripe.com/docs/api/checkout/sessions
- **外部**: Stripe Webhooks签名验证 - https://stripe.com/docs/webhooks/signatures
- **外部**: Stripe测试卡号 - https://stripe.com/docs/testing

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] Stripe checkout creates session: `curl -s -X POST http://localhost:8080/v1/checkout -d '{"amount": 1000}' | jq '.checkout_url'` → valid URL
- [ ] Webhook handler creates API key: After Stripe test webhook, API key exists in system
- [ ] Balance endpoint works: `curl -s http://localhost:8080/v1/balance -H "Authorization: Bearer $JWT" | jq '.balance'` → shows token balance
- [ ] Request rejected when balance low: Set balance to 0, make request → returns 402

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Create Stripe checkout session
  Tool: Bash (curl)
  Preconditions: Gateway running, Stripe API key configured
  Steps:
    1. curl -s -X POST http://localhost:8080/v1/checkout \
         -H "Content-Type: application/json" \
         -d '{"amount": 1000, "currency": "usd", "product_id": "prod_1Mtokens"}'
    2. Assert: HTTP status is 200
    3. Assert: response.checkout_url contains "https://checkout.stripe.com"
    4. Assert: response.session_id is not null
  Expected Result: Stripe checkout session created
  Evidence: .sisyphus/evidence/task-6-checkout-created.txt

Scenario: Stripe webhook creates API key and credits balance
  Tool: Bash (stripe CLI or curl with test webhook)
  Preconditions: Gateway running, webhook endpoint /webhooks/stripe configured
  Steps:
    1. # Simulate Stripe webhook (use stripe CLI or manually craft request)
    2. STRIPE_PAYLOAD='{
         "event": "checkout.session.completed",
         "data": {
           "object": {
             "id": "cs_test_123",
             "metadata": {"user_id": "test-user"},
             "payment_status": "paid"
           }
         }
       }'
    3. curl -s -X POST http://localhost:8080/webhooks/stripe \
         -H "Content-Type: application/json" \
         -H "Stripe-Signature: $VALID_SIGNATURE" \
         -d "$STRIPE_PAYLOAD"
    4. Assert: HTTP status is 200
    5. # Verify API key created (query internal store or DB)
    6. # Verify balance credited (call /v1/balance endpoint)
  Expected Result: Webhook processing creates API key and credits user
  Evidence: .sisyphus/evidence/task-6-webhook-processed.txt

Scenario: Balance endpoint returns current balance
  Tool: Bash (curl)
  Preconditions: Gateway running, user with API key exists, VALID_JWT set
  Steps:
    1. curl -s http://localhost:8080/v1/balance \
         -H "Authorization: Bearer $VALID_JWT"
    2. Assert: HTTP status is 200
    3. Assert: response.balance_tokens is integer >= 0
    4. Assert: response.balance_usd is decimal >= 0
  Expected Result: Balance endpoint shows user's token and USD balance
  Evidence: .sisyphus/evidence/task-6-balance-response.txt

Scenario: Request rejected when balance is zero
  Tool: Bash (curl)
  Preconditions: Gateway running, user with zero balance, VALID_JWT set
  Steps:
    1. # Set balance to 0 (via admin API or direct DB update)
    2. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}'
    3. Assert: HTTP status is 402 (Payment Required)
    4. Assert: response.error.message contains "balance" or "insufficient"
  Expected Result: Gateway rejects requests when balance is insufficient
  Evidence: .sisyphus/evidence/task-6-insufficient-balance.txt

Scenario: Request succeeds and decrements balance
  Tool: Bash (curl)
  Preconditions: Gateway running, user with balance > 1000 tokens, VALID_JWT set
  Steps:
    1. # Get initial balance
    2. BALANCE_BEFORE=$(curl -s http://localhost:8080/v1/balance \
         -H "Authorization: Bearer $VALID_JWT" | jq '.balance_tokens')
    3. # Make a request (expect ~100 tokens)
    4. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $VALID_JWT" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Generate 100 tokens"}], "max_tokens": 100}' \
         -o /dev/null
    5. sleep 1
    6. # Get new balance
    7. BALANCE_AFTER=$(curl -s http://localhost:8080/v1/balance \
         -H "Authorization: Bearer $VALID_JWT" | jq '.balance_tokens')
    8. # Calculate difference
    9. DIFF=$((BALANCE_BEFORE - BALANCE_AFTER))
    10. Assert: $DIFF > 0  # Balance decreased
    11. Assert: $DIFF <= 200  # But not by more than 200 tokens (allowing some margin)
  Expected Result: Balance decreases after request
  Evidence: .sisyphus/evidence/task-6-balance-decreased.txt
\`\`\`

---

### [ ] 7. Prometheus监控和Grafana Dashboard

**What to do**:
- 在Gateway中添加Prometheus metrics endpoint（/metrics）
- 实现关键metrics（request_rate, request_latency, error_rate, token_throughput, active_requests）
- 配置Prometheus scraping（已配置在docker-compose.yml）
- 创建Grafana Dashboard（可视化metrics）
- 配置告警规则（可选：error_rate > 5%, latency_p95 > 5s）

**Must NOT do**:
- 不要暴露敏感信息在metrics标签中（如API key、JWT内容）
- 不要创建高基数metrics（如user_id作为label会导致内存爆炸）
- 不要忽略metrics类型选择（Counter、Histogram、Gauge的正确使用）

**Recommended Agent Profile**:
- **Category**: `unspecified-high`
  - Reason: 可观测性，需要Prometheus知识
- **Skills**: `[]`
  - No special skills needed

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 6, 8 in Wave 3)
- **Parallel Group**: Wave 3
- **Blocks**: None
- **Blocked By**: Task 2

**References**:
- **外部**: Go Prometheus client库 - https://github.com/prometheus/client_golang
- **外部**: Prometheus metrics最佳实践 - https://prometheus.io/docs/practices/naming/
- **外部**: Grafana Dashboard创建 - https://grafana.com/docs/grafana/latest/dashboards/

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] /metrics endpoint accessible: `curl -s http://localhost:8080/metrics | grep llm_` → shows metrics
- [ ] Prometheus scrapes Gateway: `curl -s http://localhost:9090/api/v1/targets | jq '.data.activeTargets[] | select(.labels.job=="gateway")'` → health "up"
- [ ] Grafana dashboard exists: `curl -s http://localhost:3000/api/search | jq '.[] | select(.title=="LLM Gateway")'` → shows dashboard
- [ ] Dashboard shows data: Grafana UI displays request rate, latency, error rate

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: /metrics endpoint exposes Prometheus metrics
  Tool: Bash (curl)
  Preconditions: Gateway running with Prometheus integration
  Steps:
    1. curl -s http://localhost:8080/metrics
    2. Assert: Output contains "llm_requests_total" (Counter)
    3. Assert: Output contains "llm_request_duration_seconds" (Histogram)
    4. Assert: Output contains "llm_active_requests" (Gauge)
    5. Assert: Output contains "llm_tokens_total" (Counter)
  Expected Result: Gateway exposes all key metrics
  Evidence: .sisyphus/evidence/task-7-metrics-exposed.txt

Scenario: Prometheus successfully scrapes Gateway
  Tool: Bash (curl, jq)
  Preconditions: Prometheus running, Gateway exposing /metrics
  Steps:
    1. curl -s http://localhost:9090/api/v1/targets | jq '.data.activeTargets[] | select(.labels.job=="gateway")'
    2. Assert: At least one target returned
    3. Assert: target.health equals "up"
    4. Assert: target.lastError is null or empty
  Expected Result: Prometheus is scraping Gateway metrics
  Evidence: .sisyphus/evidence/task-7-prometheus-targets.json

Scenario: Grafana dashboard displays metrics
  Tool: Playwright (web-browser automation)
  Preconditions: Grafana running, dashboard created, metrics flowing
  Steps:
    1. Navigate to: http://localhost:3000
    2. Login with admin/admin (or configured credentials)
    3. Click on "Dashboards" → "LLM Gateway"
    4. Wait for: Dashboard loads (timeout: 5s)
    5. Assert: Panel "Request Rate" shows data (not "No Data")
    6. Assert: Panel "Request Latency (P95)" shows data
    7. Assert: Panel "Error Rate" shows data
    8. Screenshot: .sisyphus/evidence/task-7-grafana-dashboard.png
  Expected Result: Grafana dashboard visualizes all key metrics
  Evidence: Screenshot saved

Scenario: Metrics increment after requests
  Tool: Bash (curl)
  Preconditions: Gateway running, Prometheus scraping, VALID_JWT set
  Steps:
    1. # Get initial request count
    2. REQUESTS_BEFORE=$(curl -s http://localhost:8080/metrics | grep "^llm_requests_total" | awk '{print $2}')
    3. # Make 5 requests
    4. for i in {1..5}; do
         curl -s -X POST http://localhost:8080/v1/chat/completions \
           -H "Authorization: Bearer $VALID_JWT" \
           -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}], "max_tokens": 5}' \
           -o /dev/null
       done
    5. sleep 2  # Wait for metrics update
    6. # Get new request count
    7. REQUESTS_AFTER=$(curl -s http://localhost:8080/metrics | grep "^llm_requests_total" | awk '{print $2}')
    8. # Calculate difference
    9. DIFF=$(echo "$REQUESTS_AFTER - $REQUESTS_BEFORE" | bc)
    10. Assert: $DIFF = 5
  Expected Result: Metrics increment correctly after each request
  Evidence: .sisyphus/evidence/task-7-metrics-increment.txt
\`\`\`

---

### [ ] 8. 最小化注册页（静态HTML）

**What to do**:
- [DECISION NEEDED: 确认需要注册页] 假设选择"B. 最小化注册页"
- 创建单页HTML表单（姓名+邮箱）
- 实现前端JS（调用/v1/register API）
- 实现/v1/register后端端点（创建用户、生成API key、返回welcome页面）
- 添加基础样式（简单CSS，无需框架）
- 部署静态文件（Nginx或Go内置文件服务）

**Must NOT do**:
- 不要使用前端框架（React/Vue，增加复杂度）
- 不要实现邮箱验证（MVP跳过，直接生成API key）
- 不要实现密码重置（API key是唯一的认证凭据）
- 不要添加用户管理界面（仅注册）

**Recommended Agent Profile**:
- **Category**: `visual-engineering`
  - Reason: 前端UI，需要HTML/CSS
- **Skills**: `["frontend-ui-ux"]`
  - `frontend-ui-ux`: UI/UX设计和实现

**Parallelization**:
- **Can Run In Parallel**: YES (with Task 6, 7 in Wave 3)
- **Parallel Group**: Wave 3
- **Blocks**: None
- **Blocked By**: Task 6

**References**:
- **外部**: HTML5表单验证 - https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation
- **外部**: Fetch API - https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API

**Acceptance Criteria**:

> **AGENT-EXECUTABLE VERIFICATION ONLY**

- [ ] Registration page loads: `curl -s http://localhost:8080/register | grep "<form>"` → contains form
- [ ] Form submission creates user: Submit form → returns API key
- [ ] API key works: Use returned API key in /v1/chat/completions → returns chat completion
- [ ] Page is responsive: Open in browser → displays correctly on mobile

**Agent-Executed QA Scenarios**:

\`\`\`
Scenario: Registration page loads and displays form
  Tool: Playwright (web-browser)
  Preconditions: Gateway serving /register page
  Steps:
    1. Navigate to: http://localhost:8080/register
    2. Wait for: page content (timeout: 3s)
    3. Assert: Page contains "Name" or "姓名" input field
    4. Assert: Page contains "Email" or "邮箱" input field
    5. Assert: Page contains "Submit" or "提交" button
    6. Screenshot: .sisyphus/evidence/task-8-register-page.png
  Expected Result: Registration page displays with form fields
  Evidence: Screenshot saved

Scenario: Form submission creates user and returns API key
  Tool: Playwright (web-browser)
  Preconditions: Registration page loaded
  Steps:
    1. Fill input[name="name"] with "Test User"
    2. Fill input[name="email"] with "test@example.com"
    3. Click button[type="submit"]
    4. Wait for: response or redirect (timeout: 5s)
    5. Assert: Page contains "sk-" (API key prefix) or "success"
    6. Assert: Page displays API key in visible element
    7. Screenshot: .sisyphus/evidence/task-8-register-success.png
  Expected Result: Form submission creates user and shows API key
  Evidence: Screenshot saved

Scenario: Returned API key works for chat completion
  Tool: Bash (curl)
  Preconditions: Registration successful, API_KEY returned
  Steps:
    1. # Extract API key from registration (or copy from page)
    2. API_KEY="sk-test-..."
    3. # Use API key to make chat completion request
    4. curl -s -X POST http://localhost:8080/v1/chat/completions \
         -H "Authorization: Bearer $API_KEY" \
         -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Hello from registered user"}], "max_tokens": 50}'
    5. Assert: HTTP status is 200
    6. Assert: response.choices[0].message.content contains "hello" or "Hello"
  Expected Result: Newly created API key works for API calls
  Evidence: .sisyphus/evidence/task-8-api-key-works.txt

Scenario: Form validation rejects invalid email
  Tool: Playwright (web-browser)
  Preconditions: Registration page loaded
  Steps:
    1. Fill input[name="name"] with "Test"
    2. Fill input[name="email"] with "invalid-email" (missing @)
    3. Click button[type="submit"]
    4. Wait for: error message (timeout: 2s)
    5. Assert: Page contains "invalid" or "格式错误" or similar error
    6. Screenshot: .sisyphus/evidence/task-8-validation-error.png
  Expected Result: Form validates email format
  Evidence: Screenshot saved

Scenario: Page is responsive on mobile viewport
  Tool: Playwright (web-browser with viewport emulation)
  Preconditions: Registration page loaded
  Steps:
    1. Set viewport to 375x667 (iPhone SE)
    2. Navigate to: http://localhost:8080/register
    3. Wait for: page content (timeout: 3s)
    4. Assert: Form is visible without horizontal scrolling
    5. Assert: Input fields are large enough to tap (min 44px height)
    6. Assert: Submit button is fully visible
    7. Screenshot: .sisyphus/evidence/task-8-mobile-responsive.png
  Expected Result: Page is usable on mobile devices
  Evidence: Screenshot saved
\`\`\`

---

## Commit Strategy

| After Task | Message | Files | Verification |
|------------|---------|-------|--------------|
| 0 | `chore: initialize project and github repository` | README.md, go.mod, .gitignore | `git remote -v` |
| 1 | `feat(infrastructure): add docker compose configuration` | docker-compose.yml, .env.template | `docker-compose config` |
| 2 | `feat(inference): deploy vllm with qwen3-14b and glm-4 models` | deployments/vllm/Dockerfile, configs/models.yaml | `curl http://localhost:8000/health && curl http://localhost:8001/health` |
| 3 | `feat(gateway): implement chat completions proxy with jwt auth and model routing` | cmd/main.go, pkg/proxy/*.go, pkg/auth/*.go | `curl -H "Authorization: Bearer $JWT" -d '{"model":"qwen3-14b",...}' http://localhost:8080/v1/chat/completions` |
| 4 | `feat(gateway): add jwt generation and rate limiting` | cmd/jwtgen/main.go, pkg/ratelimit/*.go | `for i in {1..15}; do curl ...; done` |
| 5 | `feat(integration): add openmeter token usage tracking` | pkg/openmeter/*.go | `curl http://localhost:8888/v1/users/$USER_ID/events` |
| 6 | `feat(payments): integrate stripe checkout and wallet system` | pkg/stripe/*.go, cmd/webhook/main.go | `curl -X POST /v1/checkout` |
| 7 | `feat(monitoring): add prometheus metrics and grafana dashboard` | pkg/metrics/*.go, dashboards/*.json | `curl http://localhost:8080/metrics` |

---

## Success Criteria

### Verification Commands

```bash
# 基础设施健康检查
docker-compose ps  # Expected: All services "Up"
curl -s http://localhost:8080/health | jq '.status'  # Expected: "ok"

# vLLM验证
curl -s -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Test"}]}' | jq '.choices[0].message.content'
# Expected: Valid response

# Gateway验证（需要先创建JWT）
JWT=$(go run cmd/jwtgen/main.go --user-id test --api-key test)
curl -s -X POST http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer $JWT" \
  -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "Hello"}]}' | jq '.choices[0].message.content'
# Expected: "hello" or "Hello"

# 限流验证
for i in {1..15}; do
  curl -s -w "%{http_code}" -o /dev/null \
    -H "Authorization: Bearer $JWT" \
    -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}' \
    http://localhost:8080/v1/chat/completions
done | grep -c "429"
# Expected: At least 5 requests return 429

# Token计数验证
curl -s -X POST http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer $JWT" \
  -d '{"model": "qwen3-14b", "messages": [{"role": "user", "content": "test"}]}' | jq '.usage.total_tokens'
# Expected: Integer > 0

# OpenMeter验证
curl -s "http://localhost:8888/v1/users/test/events" | jq '.events | length'
# Expected: > 0

# Prometheus验证
curl -s http://localhost:8080/metrics | grep "llm_requests_total"
# Expected: Shows metric count
```

### Final Checklist

- [ ] 所有"Must Have"功能已实现
- [ ] 所有"Must NOT Have"功能未实现（防止scope creep）
- [ ] 所有任务通过Agent-Executed QA验证
- [ ] 负载测试通过（100并发，P95延迟<5s）
- [ ] 失败模式已测试（vLLM宕机、GPU OOM、网络超时）
- [ ] 文档完整（API文档、部署文档、运维手册）
- [ ] 代码已提交到Git仓库

---

## Appendix: Implementation Notes

### A. Go Gateway架构建议

```
cmd/
  main.go              # Entry point
  jwtgen/              # JWT generation tool
  jwtverify/           # JWT verification tool
pkg/
  auth/                # JWT middleware
    jwt.go
    middleware.go
  proxy/               # vLLM proxy logic
    client.go
    handler.go
  ratelimit/           # Rate limiting
    limiter.go
    middleware.go
  openmeter/           # OpenMeter integration
    client.go
    events.go
  stripe/              # Stripe integration
    checkout.go
    webhook.go
  metrics/             # Prometheus metrics
    collector.go
    middleware.go
web/
  register.html        # Registration page
  static/
    style.css
```

### B. 环境变量清单

```bash
# Gateway
VLLM_ENDPOINT=http://vllm:8000
JWT_SECRET=your-secret-key-here
RATE_LIMIT_RPM=10

# OpenMeter
OPENMETER_ENDPOINT=http://openmeter:8888

# Stripe
STRIPE_API_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Prometheus
PROMETHEUS_ENDPOINT=http://prometheus:9090

# Model
MODEL_NAME=qwen3-14b
```

### C. Docker Compose服务清单

```yaml
services:
  vllm:        # Port 8000
  gateway:     # Port 8080
  openmeter:   # Port 8888
  prometheus:  # Port 9090
  grafana:     # Port 3000
  nginx:       # Port 80 (serves static files)
```

### D. 性能基准参考

| Metric | Target | How to Measure |
|--------|--------|----------------|
| **Cold Start** | <5s | Time from `docker-compose up` to first 200 response |
| **TTFT** | <1s | Time from request to first token (non-streaming) |
| **Throughput** | >10 req/s | Concurrent requests / total time |
| **P95 Latency** | <5s | `curl -w "@curl-format.txt` histogram |
| **Memory (Gateway)** | <100MB | `docker stats gateway-container` |

### E. 故障排查指南

| Symptom | Possible Cause | Diagnostic Command |
|---------|----------------|-------------------|
| Gateway returns 503 | vLLM down | `docker ps | grep vllm` |
| Gateway returns 401 | Invalid JWT | `go run cmd/jwtverify/main.go $JWT` |
| Requests return 429 | Rate limit exceeded | Wait 60s or check Gateway logs |
| OpenMeter no events | Async queue issue | `docker logs openmeter` |
| Prometheus no data | Scrape failure | `curl http://localhost:9090/api/v1/targets` |

---

**End of Work Plan**
