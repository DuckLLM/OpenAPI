# OpenAPI
OpenAI API from DuckLLM | OpenAI API 中转站开源项目
# OpenAI API from DuckLLM | OpenAI API 中转站开源项目

> 一个**轻量级、可私有化部署**的 OpenAI API Relay，让你在中国大陆 / 香港也能畅用 GPT-4o、GPT-4 Turbo、Whisper 等模型

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/duckllm/openai-relay?logo=docker)](https://hub.docker.com/r/duckllm/openai-relay)
[![CI](https://github.com/DuckLLM/openai-relay/actions/workflows/ci.yml/badge.svg)](https://github.com/DuckLLM/openai-relay/actions)

---

## ✨ 主要特性

| 功能              | 描述                                          |
| --------------- | ------------------------------------------- |
| **一行部署**        | 支持 Docker / Docker-Compose / Helm，一分钟启动     |
| **稳定可用**        | DuckLLM 全球加速通道，智能路由绕过阻断                     |
| **100% API 兼容** | 完全复刻 OpenAI 官方路径和参数，现有代码零改动                 |
| **安全合规**        | 支持自托管，所有请求 & 日志可自行落地                        |
| **灵活计费**        | 内置多租户 + 额度管理，可对接 Stripe / Paddle / 微信 / 支付宝 |
| **速率控制**        | 全局与用户级限流，防止滥用                               |
| **缓存加速**        | 可选向量/内容缓存，大幅降低延迟与费用                         |
| **可观测性**        | Prometheus & Grafana 监控模板开箱即用               |

---

## 📦 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/DuckLLM/openai-relay.git
cd openai-relay
```

### 2. 准备环境变量

> 将 `.env.example` 重命名为 `.env`，并填入你的 OpenAI Key
> （支持多个 Key，逗号分隔；也支持从 DuckLLM 控制台自动下发）

```env
OPENAI_KEYS=sk-xxxxx,sk-yyyyy
PORT=8080
CACHE_BACKEND=redis://redis:6379         # 可选
```

### 3. Docker 一键启动

```bash
docker compose up -d
# 或者
docker run -d \
  -p 8080:8080 \
  -e OPENAI_KEYS=sk-xxxxx \
  --name openai-relay \
  duckllm/openai-relay:latest
```

启动成功后，客户端只需把 `https://api.openai.com` 替换成
`http://<你的服务器 IP 或域名>:8080` 即可，无需改动其余代码。

---

## 🛠️ 典型用例

| 场景            | 带来的价值                                              |
| ------------- | -------------------------------------------------- |
| **企业私有化落地**   | 符合国密要求，敏感数据不出境                                     |
| **SaaS 二次分发** | 自带子账户 & 额度管理，轻松做“Key 分销”                           |
| **成本优化**      | 内置策略路由和分片缓存，均摊官方调用成本                               |
| **教育 / 研究**   | 统一接入 GPT-4o / GPT-4 Turbo / Claude 3 等多模型，教学演示简单直观 |

---

## 🖥️ API 兼容性

| 官方路径                   | Relay 路径               | 支持状态    |
| ---------------------- | ---------------------- | ------- |
| `/v1/chat/completions` | `/v1/chat/completions` | ✅       |
| `/v1/completions`      | `/v1/completions`      | ✅       |
| `/v1/audio/speech`     | `/v1/audio/speech`     | ✅       |
| `/v1/embeddings`       | `/v1/embeddings`       | ✅       |
| `/v1/files`            | `/v1/files`            | ✅       |
| ...                    | ...                    | 📈 持续更新 |

---

## 🔒 安全与合规

* **请求签名与限流**：支持 HMAC / JWT / IP 白名单
* **日志脱敏**：`--mask-log` 参数自动隐藏敏感字段
* **国密加密通道**：内置 SM2/TLS 国密补丁，可选开启

---

## 🚀 性能

| 模式        | QPS（单核） | 延迟       | 备注              |
| --------- | ------- | -------- | --------------- |
| 直连官方      | 35+     | 100 ms\* | 由 OpenAI RTT 决定 |
| 带缓存       | 180+    | 10 ms    | 命中率 40 % 时      |
| LoRA 本地推理 | 120+    | 35 ms    | 7B Q4\_0 Quant  |

> \*四川电信 ↔︎ OpenAI 直连平均延迟，仅供参考

---

## 🧩 进阶配置

<details>
<summary>自定义多租户鉴权</summary>

```yaml
tenants:
  - id: internal
    key_prefix: int_
    quota: 120000  # 月度 token
  - id: reseller_01
    key_prefix: rs1_
    quota: 500000
    webhook: https://your.billing/webhook
```

</details>

<details>
<summary>向量缓存 (Weaviate / Milvus / Qdrant)</summary>

```yaml
vector_cache:
  enabled: true
  provider: qdrant
  url: http://qdrant:6333
  embedding_endpoint: /v1/embeddings
```

</details>

---

## 🤝 贡献

1. Fork 本仓库并创建分支
2. 提交 PR 前请确保通过 `make lint && make test`
3. 每个新特性请附带文档与测试

欢迎提 Issue、Star、Pull Request！🎉

---

## 📜 协议

本项目采用 **Apache 2.0** 开源协议。商业使用请遵守条款，若有特别需求可联系 [Support@duckllm.com](mailto:Support@duckllm.com) 商议企业授权。

---

## 📬 联系我们

* 官网：[DuckLLM.com](https://DuckLLM.com)
* Telegram 讨论组：[@AIAPIService](https://t.me/AIAPIService)

> DuckLLM — 帮你把最好的大模型，带到离你最近的服务器。
