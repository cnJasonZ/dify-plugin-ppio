# Phase 3: Verification & Documentation

---

## Step 1: Logo Handling

Check if the project has a directory for provider logos or icons:

```bash
find . -type d -name "assets" -o -type d -name "icons" -o -type d -name "logos" -o -type d -name "images" | grep -v node_modules | grep -v .git
ls src/assets/providers/  # or similar paths
```

If the project uses provider logos:

1. Identify the required format and naming convention by looking at existing provider logo files.
2. Copy the appropriate SVG from this skill's `assets/{provider}/` directory to the project's logo directory.
3. Rename to match the project's naming convention (e.g., `ppio.svg`, `ppio-logo.svg`, `provider-ppio.svg`).

Available logos (relative to skill root):
- PPIO: `assets/ppio/icon_s_en.svg` (small), `assets/ppio/icon_l_en.svg` (large EN), `assets/ppio/icon_l_zh.svg` (large ZH)
- Novita AI: `assets/novita/icon_s_en.svg` (small), `assets/novita/icon_l_en.svg` (large)
- Jiekou.AI: `assets/jiekou/icon_s_en.svg` (small), `assets/jiekou/icon_l_en.svg` (large EN), `assets/jiekou/icon_l_zh.svg` (large ZH)

If the project requires a format other than SVG (PNG, WebP), inform the user that manual conversion is needed.

---

## Step 2: Update README.md

Find the section in README.md where providers are listed. Add the new provider following the exact same format as existing entries.

**UTM Rule:** Append `?utm_source=github_{project_name}` to ALL provider URLs in the README. The `{project_name}` is the GitHub repository name (e.g., for `https://github.com/user/my-agent-project`, the project name is `my-agent-project`).

Example entry (adapt to match project's existing format):

```markdown
- [PPIO](https://ppio.com?utm_source=github_my-agent-project) — PPIO supports stable and cost-efficient open-source LLM APIs, such as DeepSeek, Llama, Qwen etc.
```

Or if the project uses a table format:

```markdown
| Provider | Description | API Key |
|----------|-------------|---------|
| [PPIO](https://ppio.com?utm_source=github_my-agent-project) | PPIO supports stable and cost-efficient open-source LLM APIs | [Get API Key](https://ppio.com/settings/key-management?utm_source=github_my-agent-project) |
```

Use provider intro text from `references/providers.md`. Choose English or Chinese based on the project's language (most open-source projects use English).

If the project has a dedicated section per provider (setup instructions, code example), add one following the existing format. Include:
- Link to provider homepage (with UTM)
- Link to API key page (with UTM)
- Link to documentation (with UTM)
- ENV variable name
- Brief setup instructions

---

## Step 3: Build Verification

Install dependencies and build the project:

```bash
# JavaScript/TypeScript
npm install && npm run build

# Python
pip install -r requirements.txt

# Go
go build ./...

# Rust
cargo build
```

Check for:
- Dependency installation errors
- Build/compilation errors
- TypeScript type errors
- Any warnings that might indicate problems

If build fails, fix errors before continuing.

---

## Step 4: Run Tests

```bash
# JavaScript/TypeScript
npm test

# Python
pytest

# Go
go test ./...

# With coverage
npm test -- --coverage
pytest --cov
```

Verify:
- All existing tests still pass (no regressions)
- New provider tests pass
- No test failures or errors

If tests fail, debug and fix before proceeding.

---

## Step 5: Code Quality Checks

Run project-specific quality tools if they exist:

```bash
# Linter
npm run lint
pylint src/
golangci-lint run

# Type checker
npx tsc --noEmit

# Formatter check
npm run format:check
black --check .
```

Fix any linting errors or type errors. Formatting issues can sometimes be auto-fixed:
```bash
npm run format
black .
```

---

## Step 6: Manual Testing (Optional, if API key provided)

If the user has provided an API key for the provider:

```bash
# Set environment variable
export PPIO_API_KEY=your-key-here  # or NOVITA_API_KEY, JIEKOU_API_KEY

# Run the project
npm run dev  # or python main.py, etc.
```

Test the following scenarios:
1. **Initialization:** Provider initializes correctly with the API key
2. **Model listing:** Can retrieve available models from the API
3. **Chat completion:** Can send a request and receive a valid response
4. **Streaming:** Streaming works correctly (if the provider supports it)
5. **Error handling:** Proper error message when using an invalid API key

---

## Phase 3 Checkpoint

Before proceeding to Phase 4, verify:

- [ ] Logo file copied to correct location (if project uses logos)
- [ ] Logo renamed to match project's naming convention
- [ ] README.md updated with new provider
- [ ] All provider URLs in README include `?utm_source=github_{project_name}`
- [ ] Provider intro text matches project's language and format
- [ ] Dependencies install without errors
- [ ] Project builds without errors
- [ ] All tests pass (existing + new)
- [ ] No linting or type errors
- [ ] Manual testing completed (if API key provided)
- [ ] Provider works end-to-end
