# Model Processing

All three providers (PPIO, Novita AI, Jiekou.AI) share an identical model list API format.

---

## Model List API

| Type | Endpoint |
|------|----------|
| LLM models | `{BASE_URL}/models` |
| Embedding models | `{BASE_URL}/models?model_type=embedding` |
| Reranker models | `{BASE_URL}/models?model_type=reranker` |

Authentication: `Authorization: Bearer {API_KEY}`

Fetch live model data:
```bash
curl -s -H "Authorization: Bearer $API_KEY" "{BASE_URL}/models" | jq '.data'
```

---

## Response Schema

```typescript
type ModelFeature = 'function-calling' | 'structured-outputs' | 'reasoning' | 'anthropic-api';
type ModelModality = 'text' | 'image' | 'video' | 'audio';
type ModelEndpoint = 'chat/completions' | 'completions' | 'anthropic' | 'batch-api';

interface Model {
  id: string;                           // Use as unique identifier and API call parameter
  title: string;                        // Technical name (use when no UI display needed)
  display_name: string;                 // User-facing name (use in UI if project has one)
  description: string;
  context_size: number;
  max_output_tokens: number;
  status: number;                       // 1 = available, 0 = unavailable
  input_token_price_per_m: number;      // See price conversion below
  output_token_price_per_m: number;     // See price conversion below
  features: ModelFeature[];
  input_modalities: ModelModality[];
  output_modalities: ModelModality[];
  endpoints: ModelEndpoint[];
}

interface ModelListResponse {
  data: Model[];
}
```

---

## Price Conversion

Raw price values are in units of **0.0001** (one ten-thousandth of the currency unit). Divide by 10000 to get the actual price per million tokens.

```
actual_price_per_million = raw_price / 10000
```

Currency by provider:
- **PPIO:** Chinese Yuan (RMB, ¥)
- **Novita AI:** USD ($)
- **Jiekou.AI:** USD ($)

Example: If `input_token_price_per_m = 14`, the price is ¥0.0014 per million tokens (for PPIO) or $0.0014 per million tokens (for Novita/Jiekou).

---

## Features

Valid values in `features` array:

| Feature value | Meaning |
|---------------|---------|
| `function-calling` | Supports tool/function calling |
| `structured-outputs` | Supports structured JSON output |
| `reasoning` | Has reasoning/thinking capability |
| `anthropic-api` | Supports Anthropic API protocol |
| `serverless` | **Ignore this value** |

**Rule:** Skip any unknown feature values not listed above.

---

## Modalities

`input_modalities` and `output_modalities` can contain:

| Value | Meaning |
|-------|---------|
| `text` | Text input/output |
| `image` | Image input/output |
| `video` | Video input/output |
| `audio` | Audio input/output |

---

## Endpoint Protocols

The `endpoints` field indicates which API protocols the model supports:

| Endpoint value | Protocol |
|----------------|----------|
| `chat/completions` | OpenAI Compatible |
| `completions` | OpenAI Compatible |
| `anthropic` | Anthropic API |
| `batch-api` | **Ignore** |

A model supports **OpenAI Compatible** if its `endpoints` contains `chat/completions` or `completions`.
A model supports **Anthropic API** if its `endpoints` contains `anthropic`.

---

## Mapping to Project-Specific Schemas

### If project separates features and modalities

Map directly:
- `features` → project's capability/feature field
- `input_modalities` / `output_modalities` → project's modality field

### If project uses a combined capability list (does NOT separate features from modalities)

Combine `features` + modality-derived capabilities. Examples:
- Project has a `vision` boolean: check if `features` contains `vision` OR if `input_modalities` contains `image`
- Project has a `video` capability: check `input_modalities` or `output_modalities` for `video` — **do NOT look in `features`**
- Project has a `function_calling` flag: map from `features` value `function-calling`

**Key rule:** Video, image, and audio capabilities come from `input_modalities`/`output_modalities`. They will NOT appear in `features`. Never look for `video`, `image`, or `audio` inside the `features` array.

### If project uses a protocol/API-type field

Map `endpoints` array:
- `chat/completions` or `completions` present → mark as OpenAI Compatible
- `anthropic` present → mark as Anthropic API compatible

---

## Model Status

Only include models where `status === 1`. Filter out unavailable models (`status === 0`).
