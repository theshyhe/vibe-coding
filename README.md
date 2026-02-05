# Vibe Coding

AI算力交易平台 - 部署开源大模型，提供OpenAI兼容的API服务。

## 🎯 项目概述

Vibe Coding是一个企业级AI算力交易平台，通过部署高性能开源大模型，为开发者、企业客户和个人用户提供稳定的推理服务。

### 核心特性

- **🤖 多模型支持**: Qwen3-14B（通义千问）+ GLM-4（智谱）
- **⚡ 高性能推理**: 基于vLLM引擎，PagedAttention技术
- **🔒 安全认证**: JWT认证 + 速率限制
- **💰 灵活计费**: Token级别计量，支持预付费和订阅制
- **📊 完整监控**: Prometheus + Grafana实时监控

## 🏗️ 技术架构

```
┌─────────────────────────────────────────┐
│         Go Gateway (Port 8080)          │
│  • JWT认证、限流、模型路由                │
│  • OpenAI兼容API                        │
└────────────────┬────────────────────────┘
                 │
        ┌────────┴────────┐
        ▼                 ▼
┌──────────────┐  ┌──────────────┐
│  vLLM Qwen   │  │  vLLM GLM    │
│  :8000       │  │  :8001       │
│  GPU 0       │  │  GPU 1       │
└──────────────┘  └──────────────┘
```

### 技术栈

| 组件 | 技术 | 说明 |
|------|------|------|
| **API网关** | Go 1.21+ | 高并发、轻量级 |
| **推理引擎** | vLLM (Python) | PagedAttention、2-24x吞吐量 |
| **监控** | Prometheus + Grafana | 云原生标准 |
| **计费** | OpenMeter + Stripe | 实时Token计量 |

## 📋 实施计划

### Phase 1: MVP (6-8周)

- ✅ 双模型部署（Qwen3-14B + GLM-4）
- ✅ OpenAI兼容API
- ✅ JWT认证 + 内存限流
- ✅ 预付费钱包（Stripe Checkout）
- ✅ 基础监控

### Phase 2: 增强 (+4-6周)

- ➕ 扩展模型（MiniMax + DeepSeek + Llama 4）
- ➕ 订阅制计费
- ➕ 自定义Python API
- ➕ 自动化用户注册

## 🚀 快速开始

```bash
# 克隆仓库
git clone git@github.com:theshyhe/vibe-coding.git
cd vibe-coding

# 启动所有服务
docker-compose up -d

# 健康检查
curl http://localhost:8080/health

# API测试（需要JWT token）
curl -X POST http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3-14b",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## 📚 文档

- [工作计划](.sisyphus/plans/ai-compute-platform-mvp.md) - 详细的实施计划
- [API文档](docs/API.md) - OpenAI兼容API参考（待创建）
- [部署指南](docs/DEPLOYMENT.md) - 生产环境部署指南（待创建）

## 🛠️ 开发

### 环境要求

- Go 1.21+
- Python 3.10+
- Docker & Docker Compose
- NVIDIA GPU + nvidia-docker2
- 8+ GB VRAM (每个模型)

### 本地开发

```bash
# 启动开发环境
docker-compose -f docker-compose.dev.yml up

# 运行测试
go test ./...

# 代码检查
golangci-lint run
```

## 📊 项目状态

- **当前阶段**: 规划完成
- **MVP目标**: 2026年3月
- **团队规模**: 4-8人

## 📄 许可证

MIT License

## 👥 联系方式

- GitHub: https://github.com/theshyhe/vibe-coding
- Issues: https://github.com/theshyhe/vibe-coding/issues
