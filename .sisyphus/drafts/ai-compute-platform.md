# Draft: AI 算力交易平台

## 原始需求
用户目标：构建一个算力交易平台系统
- **核心功能**：部署和管理开源大模型矩阵
- **服务模式**：向用户提供算力服务（类似 Hugging Face Inference API）

## 已确认的需求

### 目标用户
**混合模式** - 同时服务多种用户类型
- 企业客户：需要稳定、私有化部署、SLA 保证
- 开发者/创业者：API 调用、灵活计费、快速接入
- 个人用户：简单易用、低门槛、按需付费
- 需要分层定价体系

### 模型矩阵
**主流全家桶** - 热门模型全覆盖
- Llama 系列（Meta）
- Qwen 系列（阿里）
- Mistral 系列
- DeepSeek 系列
- Gemma 系列（Google）
- 架构需要支持灵活添加新模型

### 部署策略
**分阶段扩展**
- 前期：小规模部署（验证可行性）
- 后期：随着用户增长扩展到大规模
- 需要可扩展的架构设计

## 需求澄清进度
- [x] 目标用户群体和使用场景
- [x] 模型选择策略
- [x] 部署规模规划
- [ ] 现有项目状态和基础设施
- [ ] 技术栈偏好
- [ ] MVP 范围定义

## 技术调研进度
- [x] 完成：现有项目基础设施探索
- [x] 完成：开源模型部署方案研究
- [x] 完成：大模型算力平台架构研究

## 研究结论总结

### 项目现状
**全新项目** - 从零开始，无现有代码基础

### 核心技术栈建议

#### 1. 推理引擎
- **主力**: vLLM (PagedAttention, 2-24x吞吐量提升)
- **长上下文**: SGLang (百万token支持)
- **边缘设备**: llama.cpp (CPU推理, GGUF格式)

#### 2. 编排层
- **小规模(<4 GPU)**: Docker Compose / Systemd
- **中规模(4-32 GPU)**: Kubernetes + Ray Serve
- **大规模(32+ GPU)**: Multi-cluster Kueue + DRA

#### 3. API层
- **网关**: Kong 或 Traefik (认证、限流、路由)
- **路由**: 智能LLM Router (基于查询复杂度)

#### 4. 监控与计费
- **监控**: Langfuse (开源) 或 Helicone (SaaS)
- **计费**: OpenMeter + Stripe集成

### 模型选择建议
- **推理密集**: DeepSeek V3.2 (MIT许可, AIME25 92%)
- **通用任务**: Qwen 3 (1T+ MoE, Apache 2.0)
- **生态成熟**: Llama 4 (Meta官方)
- **资源受限**: Qwen3-14B-AWQ (4-bit量化)

### GPU优化策略
- **量化**: AWQ 4-bit (75%显存节省, 精度损失最小)
- **调度**: Time-Slicing (<10 GPU) / MIG (10-100 GPU) / Kueue (100+)
- **性能**: PagedAttention + 批处理优化 + 多GPU并行

## 待记录的关键决策
- [x] MVP 功能范围：最小验证（单模型+基础API+简单计费）
- [x] 技术栈：Go Gateway + Python vLLM
- [x] 计费模式：Token计数 + OpenMeter集成
- [x] 部署环境：需要架构支持云/自有硬件切换
- [x] 开发团队：中团队（4-8人），需要模块化设计
- [x] 测试策略：Agent-Executed QA（Playwright/Bash验证，不写单元测试）
- [x] 时间约束：质量优先，6-8周可接受

## 团队协作策略
**中团队（4-8人）模块化分工**：
- **后端组（2-3人）**: Go Gateway开发
- **AI组（1-2人）**: vLLM部署和优化
- **运维组（1-2人）**: 基础设施、监控、计费
- **QA**: 自动化测试场景设计

## 质量优先策略
- 额外时间用于：
  - 完整的错误处理
  - 生产级日志和监控
  - 安全性（认证、限流、输入验证）
  - 负载测试和性能优化

## 测试策略
**Agent-Executed QA（零人工干预）**：
- **前端/Web UI**: Playwright（浏览器自动化）
- **API/Backend**: Bash（curl验证响应）
- **CLI/TUI**: interactive_bash（tmux自动化）
- **验证重点**: 端到端场景、错误处理、性能

**不写单元测试**，通过详细的QA场景验证所有功能。

## Oracle验证结论

### ✅ 架构决策验证
**Go Gateway + vLLM 是正确选择**：
- Go的Goroutine更适合LLM网关的IO密集型场景
- 启动快（<50ms）、内存小（10-30MB）、云原生生态好
- 生产案例：Together AI（10K+并发）、OpenAI（Go路由层）

### ✅ MVP时间线可行性
**4-6周可行，但需要严格控制范围**：

**Week 1-2**: vLLM单模型部署（DeepSeek-V3或Qwen3）
**Week 3**: Go网关（OpenAI兼容API）
**Week 4**: 认证+限流（JWT + 内存限流）
**Week 5**: Token计数（tiktoken，异步非阻塞）
**Week 6**: 基础监控（Prometheus + Grafana）

### ⚠️ 关键风险
1. **vLLM稳定性**: 版本演进快，需要锁定版本（v0.6.4.post1）
2. **Token计数准确性**: 必须服务端计数，避免客户端差异
3. **计费边缘情况**: 失败请求不收费、部分响应按实际token计费
4. **Go-Python集成**: HTTP开销可忽略（~200μs vs 推理时间秒级）

### 🎯 功能优先级
1. **核心推理路径**（端到端可用）
2. **OpenAI兼容API**（/v1/chat/completions）
3. **基础认证/限流**（JWT + 10 req/min）
4. **Token计数**（异步、不阻塞）

**Phase 2延后**: 用户管理UI、复杂定价、智能路由、自动伸缩

### 🏗️ 接口预留设计
即使MVP是单模型，也需要预留多模型接口：
```go
type ModelBackend interface {
    StreamComplete(ctx, req) (<-chan Token, error)
    GetModelInfo() ModelInfo
}
```

### 🔄 Phase演进策略
**Phase 1 (MVP)**: 单模型 + Docker Compose
**Phase 2**: 多模型 + Kubernetes + Ray Serve
**Phase 3**: 智能路由 + 自动伸缩
