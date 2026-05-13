# Phase 2: Implementation

---

## Step 1: Copy Reference Provider

Copy the reference provider's implementation file(s) and rename for the new provider:

```bash
# TypeScript/JavaScript example
cp src/providers/{reference_provider}.ts src/providers/{new_provider_id}.ts

# Python example
cp src/providers/{reference_provider}.py src/providers/{new_provider_id}.py
```

---

## Step 2: Update All Identifiers

Modify these key elements in the copied file(s):

**Provider identifier:**
```typescript
// Change from reference provider's ID to new provider's ID
export const PROVIDER_ID = 'ppio';  // or 'novita', 'jiekou'
```

**Provider display name:**
```typescript
export const PROVIDER_NAME = 'PPIO';  // or 'Novita AI', 'Jiekou.AI'
```

**Base URL:**
```typescript
const BASE_URL = 'https://api.ppinfra.com/v3/openai';  // per provider
```

**API key environment variable:**
```typescript
const API_KEY = process.env.PPIO_API_KEY;  // or NOVITA_API_KEY, JIEKOU_API_KEY
```

**Class/function names:** Rename all classes, functions, and exported symbols to use the new provider's name.

**Code comments and documentation strings:** Update any comments that reference the old provider's name, URLs, or documentation.

---

## Step 3: Verify Complete Reference Replacement

Search the entire codebase for the reference provider name to find every location that needs to be updated. The new provider must appear everywhere the reference does:

```bash
# Find all files referencing the reference provider
grep -ri "{reference_provider}" \
  --include="*.ts" --include="*.js" --include="*.py" \
  --include="*.go" --include="*.rs" --include="*.rb" \
  --include="*.json" --include="*.yaml" --include="*.toml" \
  --include="*.md" --include="*.env*"
```

Check each result and add the new provider to every location.

---

## Step 4: Handle Model Information

### Option A: Project fetches models dynamically

If the project calls a `/models` endpoint at runtime, configure the URL:
```typescript
const MODELS_URL = 'https://api.ppinfra.com/v3/openai/models';
```

Refer to `references/model-processing.md` for how to handle the API response, including price conversion, feature mapping, and modality mapping.

### Option B: Project uses a static model list

Fetch live data from the provider's API and transform to the project's schema:

```bash
# Fetch the current model list
curl -s -H "Authorization: Bearer $API_KEY" "{BASE_URL}/models" | jq '.data'
```

Transform each model according to `references/model-processing.md`:
- Convert prices: `actual_price = raw_price / 10000`
- Map features: `function-calling` → project's function calling field, etc.
- Map modalities from `input_modalities` / `output_modalities`
- Determine protocol support from `endpoints`
- Filter: only include models where `status === 1`

Use `display_name` for user-facing names, `title` for technical names, `id` as the API call parameter.

---

## Step 5: Handle Protocol Support (if applicable)

If the project distinguishes between OpenAI Compatible and Anthropic API:

- **OpenAI Compatible:** model's `endpoints` contains `chat/completions` or `completions`
- **Anthropic API:** model's `endpoints` contains `anthropic`

Configure the provider to support both protocols if the project allows it. The base URL is the same for both protocols on all three providers.

---

## Step 6: Update Provider Registry

If the project has a central registry or index file that exports all providers, add the new provider:

```typescript
// src/providers/index.ts example
import { PPIOProvider } from './ppio';

export const providers = {
  openrouter: new OpenRouterProvider(),
  together: new TogetherProvider(),
  ppio: new PPIOProvider(),       // Add new provider
};

// Update type union if TypeScript
export type ProviderName = 'openrouter' | 'together' | 'ppio';
```

Follow the exact same pattern as existing providers in the registry file.

---

## Step 7: Add Tests

Find the reference provider's test file:
```bash
find . -path "*/test*" -name "*{reference_provider}*"
find . -path "*/spec*" -name "*{reference_provider}*"
```

Copy and adapt:
```bash
cp tests/providers/{reference_provider}.test.ts tests/providers/{new_provider_id}.test.ts
```

Update in the test file:
1. Import statements (use new provider class)
2. Test suite name (`describe('{NewProvider}', ...)`)
3. Provider-specific values (base URL, env var name, expected model IDs)
4. Mock setup if provider-specific

Ensure tests cover:
- Provider initialization with correct config
- Model listing (if applicable)
- Chat/completion request formatting
- Error handling (invalid key, network error)
- Streaming (if provider supports it)

Run tests before continuing:
```bash
npm test  # or pytest, go test, etc.
```

---

## Step 8: Update Configuration Files

Find and update all example config files:

```bash
find . -name "*.example.*"
find . -name ".env.example"
find . -name "config.example.*"
```

Add the new provider's API key to `.env.example`:
```env
# PPIO (派欧云)
PPIO_API_KEY=your-ppio-api-key-here

# Novita AI
NOVITA_API_KEY=your-novita-api-key-here

# Jiekou.AI
JIEKOU_API_KEY=your-jiekou-api-key-here
```

Follow the existing format. Add a brief comment with the provider name above the key.

Update any other config examples (`.toml`, `.yaml`, `.json`) following the exact same format as existing providers.

---

## Phase 2 Checkpoint

Before proceeding to Phase 3, verify:

- [ ] Implementation file created and all identifiers updated
- [ ] No remaining references to the old provider name in the new file
- [ ] New provider appears everywhere the reference provider appears
- [ ] Model data handled correctly (live query or static list with correct transformations)
- [ ] Provider registered in index/registry (if applicable)
- [ ] Type definitions updated (if TypeScript)
- [ ] Test file created and passing
- [ ] `.env.example` and other config examples updated
- [ ] Project builds without errors
- [ ] All tests pass
