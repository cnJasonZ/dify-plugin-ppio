# Provider Reference Data

All provider configuration for PPIO, Novita AI, and Jiekou.AI. Load the relevant provider block at the start of Phase 1 based on the user-specified provider ID.

---

## ppio

**Basic Info**
- Provider ID: `ppio`
- Provider Name: `PPIO`
- Base URL: `https://api.ppinfra.com/v3/openai`
- ENV Variable: `PPIO_API_KEY`
- Auth: `Authorization: Bearer $PPIO_API_KEY`
- Brand Color: `#1161fe`
- Currency: RMB (Chinese Yuan, ¥)

**Model APIs**
- LLM models: `https://api.ppinfra.com/v3/openai/models`
- Embedding models: `https://api.ppinfra.com/v3/openai/models?model_type=embedding`
- Reranker models: `https://api.ppinfra.com/v3/openai/models?model_type=reranker`

**URLs** (append `?utm_source=github_{project_name}` to all ppio.com links)
- Homepage: `https://ppio.com`
- API Key page: `https://ppio.com/settings/key-management`
- LLM API / model list: `https://ppio.com/ai-computing/llm-api`
- Image/Video API: `https://ppio.com/ai-computing/image`
- Agent Sandbox: `https://ppio.com/ai-computing/sandbox`
- Docs homepage: `https://ppio.com/docs/support/quickstart`
- Model service docs: `https://ppio.com/docs/model/overview`
- Model API reference: `https://ppio.com/docs/models/reference-authentication`
- Agent Sandbox docs: `https://ppio.com/docs/sandbox/overview`

**Provider Introduction**
- English: `PPIO supports stable and cost-efficient open-source LLM APIs, such as DeepSeek, Llama, Qwen etc.`
- Chinese: `PPIO 派欧云提供稳定、高性价比的开源模型 API 服务，支持 DeepSeek 全系列、Llama、Qwen 等行业领先大模型。`

**Logo Assets** (relative to skill root)
- Small icon: `assets/ppio/icon_s_en.svg`
- Large icon (English): `assets/ppio/icon_l_en.svg`
- Large icon (Chinese): `assets/ppio/icon_l_zh.svg`

---

## novita

**Basic Info**
- Provider ID: `novita`
- Provider Name: `Novita AI`
- Base URL: `https://api.novita.ai/v3/openai`
- ENV Variable: `NOVITA_API_KEY`
- Auth: `Authorization: Bearer $NOVITA_API_KEY`
- Brand Color: `#23d57c`
- Currency: USD ($)

**Model APIs**
- LLM models: `https://api.novita.ai/v3/openai/models`
- Embedding models: `https://api.novita.ai/v3/openai/models?model_type=embedding`
- Reranker models: `https://api.novita.ai/v3/openai/models?model_type=reranker`

**URLs** (append `?utm_source=github_{project_name}` to all novita.ai links)
- Homepage: `https://novita.ai`
- API Key page: `https://novita.ai/settings/key-management`
- LLM models page: `https://novita.ai/models`
- Agent Sandbox: `https://novita.ai/sandbox`
- Docs homepage: `https://novita.ai/docs/guides/introduction`
- Model service docs: `https://novita.ai/docs/guides/model-apis-overview`
- Model API reference: `https://novita.ai/docs/api-reference/model-apis-introduction`
- Agent Sandbox docs: `https://novita.ai/docs/guides/sandbox-overview`

**Provider Introduction**
- English: `Novita AI is an AI cloud platform that helps developers easily deploy AI models through a simple API, backed by affordable and reliable GPU cloud infrastructure.`
- Chinese: `Novita AI 是一个 AI 云平台，帮助开发者通过简单的 API 部署 AI 模型，依托于性价比高、稳定的 GPU 云基础设施。`

**Logo Assets** (relative to skill root)
- Small icon: `assets/novita/icon_s_en.svg`
- Large icon (English): `assets/novita/icon_l_en.svg`

---

## jiekou

**Basic Info**
- Provider ID: `jiekou`
- Provider Name: `Jiekou.AI`
- Base URL: `https://api.jiekou.ai/openai`
- ENV Variable: `JIEKOU_API_KEY`
- Auth: `Authorization: Bearer $JIEKOU_API_KEY`
- Brand Color: `#000000`
- Currency: USD ($)

**Model APIs**
- LLM models: `https://api.jiekou.ai/openai/models`
- Embedding models: `https://api.jiekou.ai/openai/models?model_type=embedding`
- Reranker models: `https://api.jiekou.ai/openai/models?model_type=reranker`

**URLs** (append `?utm_source=github_{project_name}` to all jiekou.ai links)
- Homepage: `https://jiekou.ai`
- API Key page: `https://jiekou.ai/settings/key-management`
- LLM models page: `https://jiekou.ai/`
- Docs homepage: `https://docs.jiekou.ai/docs/support/quickstart`
- Model service docs: `https://docs.jiekou.ai/docs/model/overview`
- Model API reference: `https://docs.jiekou.ai/docs/models/reference-authentication`

**Provider Introduction**
- English: `Jiekou.AI is a one-stop LLM API platform, providing stable, efficient and economic enterprise-level API solutions.`
- Chinese: `接口 AI 是一个一站式的大模型 API 平台，提供稳定、高效且经济的企业级 API 解决方案。`

**Logo Assets** (relative to skill root)
- Small icon: `assets/jiekou/icon_s_en.svg`
- Large icon (English): `assets/jiekou/icon_l_en.svg`
- Large icon (Chinese): `assets/jiekou/icon_l_zh.svg`
