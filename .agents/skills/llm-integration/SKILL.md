---
name: llm-integration
description: >
  Use when integrating PPIO, Novita AI, or Jiekou.AI as LLM provider into an
  open-source AI Agent project, or when updating Dify plugin model lists.
  Triggers on "integrate provider", "add LLM provider", "PPIO integration",
  "Novita integration", "Jiekou integration", "update dify models",
  "dify plugin update", "sync dify", or when user provides a GitHub repo URL
  with a provider name.
---

# LLM Provider Integration

Integrate the company's LLM API providers (PPIO, Novita AI, Jiekou.AI) into open-source AI Agent projects as LLM Providers. Provider API configuration and brand assets are pre-loaded. Git identities are NOT stored in this skill — they must be collected from the user at runtime.

## Quick Reference

| Provider | ID | Base URL | ENV |
|----------|----|----------|-----|
| PPIO | `ppio` | `https://api.ppinfra.com/v3/openai` | `PPIO_API_KEY` |
| Novita AI | `novita` | `https://api.novita.ai/v3/openai` | `NOVITA_API_KEY` |
| Jiekou.AI | `jiekou` | `https://api.jiekou.ai/openai` | `JIEKOU_API_KEY` |

## Input

User provides:
- **Required:** `{provider}` — one of `ppio`, `novita`, `jiekou` (or multiple for batch integration)
- **Required:** `{github_url}` — target open-source project's GitHub URL
- **Optional:** reference provider to copy from (if project has multiple existing providers)
- **Optional:** API key for live testing

If user specifies multiple providers, run the full pipeline for each provider sequentially (separate branch + PR per provider).

---

## Git Identity — CRITICAL

**This skill does NOT hardcode any git identity data.** Each provider has its own dedicated git identity (user.name, user.email, GitHub account). These identities are strictly bound to their provider — **using the wrong identity for a provider is a serious error.**

**Identity resolution order:**
1. Check if the user's workspace has a local identity config file (e.g., `IDENTITIES.md`, `TOOLS.md`, `.claude/identities.json`, or similar). If found, load the identity for the specified provider from there.
2. If no local config exists, ask the user to provide the git identity for the specified provider.
3. In either case, **always confirm the identity back to the user** and wait for explicit confirmation before proceeding.

**Rules:**
1. Set identity per-repo (`git config user.name`), NEVER globally (`git config --global`).
2. After every commit, verify the author matches: `git log -1 --format="%an <%ae>"`
3. If handling multiple providers in one session, collect and confirm identity for EACH provider separately.
4. **NEVER reuse one provider's identity for another provider.** For example, using Novita's git account to submit a PPIO PR is strictly forbidden.
5. Before creating a PR with `gh`, verify `gh auth status` shows the correct GitHub account. If not, run `gh auth switch --user {username}`.

Read `references/phase1-setup-analysis.md` Step 2 for the detailed identity collection flow.

---

## Pipeline

### Phase 1: Setup & Analysis

**Goal:** Prepare the repo and understand the project's provider patterns.

Read `references/phase1-setup-analysis.md` for detailed setup steps.

1. Read `references/providers.md` for the specified provider's API configuration, URLs, and brand info.
2. **Collect git identity from user** — ask for user.name, user.email, and fork remote URL for this specific provider. Confirm before proceeding.
3. Fork the target repo under the provider's dedicated account if needed. Clone the repo. Set per-repo git identity. Create branch `feat/{provider-id}`.
4. Read project README.md and CONTRIBUTING.md for development workflow, code style, and provider structure.
5. Search for existing LLM provider implementations:
   ```bash
   grep -r "openrouter\|together\|siliconflow\|fireworks\|groq\|deepseek" --include="*.ts" --include="*.js" --include="*.py" --include="*.go" --include="*.rs" -l
   ```
6. Analyze the provider pattern: file locations, naming conventions, registry/index file, test structure, config format.
7. Ask user which existing provider to use as reference (if multiple exist).

**Checkpoint:** Provider pattern identified? Git identity collected, confirmed, and set? If unclear, read more files before proceeding.

### Phase 2: Implementation

**Goal:** Implement the new provider following existing project patterns.

Read `references/phase2-implementation.md` for detailed implementation steps.
Read `references/model-processing.md` for model data handling rules.

1. Copy the reference provider's implementation file(s). Rename for the new provider.
2. Update all identifiers: provider ID, display name, base URL, env var name, class/function names, comments.
3. Search the entire codebase for the reference provider name — the new provider must appear in every location the reference provider appears (and nowhere else):
   ```bash
   grep -r "{reference_provider}" --include="*.ts" --include="*.js" --include="*.py" --include="*.json" --include="*.yaml" --include="*.toml" --include="*.md" -l
   ```
4. Handle model information: if the project hardcodes model lists, fetch live data from the provider's model API and transform to the project's schema. If the project fetches dynamically, configure the endpoint URL.
5. Update the provider registry/index file (if the project has one).
6. Copy and adapt the reference provider's test file(s). Update all references.
7. Update configuration files (.env.example, config examples, etc.).

**Checkpoint:** Does the project build without errors? Do tests pass? Fix any issues before continuing.

### Phase 3: Verification & Documentation

**Goal:** Verify everything works and update documentation.

Read `references/phase3-verification-docs.md` for detailed steps.

1. **Logo handling:** If the project has a provider logo/icon directory, copy the appropriate SVG from this skill's `assets/{provider}/` directory. Rename to match the project's naming convention.
2. **Update README.md:** Add the new provider following the existing format. Use the provider's intro text. Append `?utm_source=github_{project_name}` to ALL provider URLs.
3. **Run full verification:**
   - Install dependencies
   - Build the project
   - Run linter / type checker (if applicable)
   - Run all tests (existing + new)
4. **Manual testing** (if API key provided): test initialization, model listing, chat completion, streaming, error handling.

**Checkpoint:** All tests pass? Documentation matches existing format? All URLs have UTM parameters?

### Phase 4: Report & PR Prep

**Goal:** Generate a summary and prepare for PR submission.

Read `references/reporting-template.md` for the report format.
Read `references/checklist.md` for final verification.

1. Run through the pre-submission checklist.
2. Generate a comprehensive summary report (do NOT commit this to the repo).
3. **Verify git identity one final time** before committing: `git config user.name && git config user.email`
4. Commit all changes. Suggested commit message:
   ```
   feat: add {Provider Name} provider support
   ```
5. Prepare PR title and description from the template.
6. Present the summary to the user for review before creating the PR.

---

## Dify Plugin: Model List Update

**Trigger:** User asks to "update dify models", "sync dify plugin", "dify model list update", or mentions Dify in the context of provider integration.

This is a separate workflow from the general integration pipeline above. Use this when updating the model list for an existing Dify plugin — NOT for first-time integration.

Read `references/dify.md` for the complete workflow, branch names, and package paths.

**Input:**
- **Required:** `{provider}` — one of `ppio`, `novita`, `jiekou` (or all three for batch update)

**High-level steps:**

1. **Collect git identity** — Load from local config or ask user. Confirm before proceeding.
2. **Plugin repo:** Clone or enter the provider's Dify plugin repo. Set git identity.
3. **Sync models:** Detect and activate Python venv, run `python models/llm/sync_models.py`.
4. **Bump version:** Increment both `version` fields in `manifest.yaml` by one patch version.
5. **Commit and push** the plugin repo. Verify commit author.
6. **Build package:** Check `dify` CLI is installed. From the parent directory, run `dify plugin package`.
7. **Marketplace repo:** Clone or enter the `dify-plugins` repo. Set up provider's remote. Switch to the provider's branch. **Clean up old `.difypkg` files** before copying new one.
8. **Replace package:** Copy the `.difypkg` file to the provider's package path in the marketplace repo.
9. **Commit and push.** Switch `gh auth` to the provider's account. Read the repo's PR template. Create PR with title `Update {Provider Name} model list`, body from PR template with metadata from `manifest.yaml`.
10. **Present the PR URL** to the user.

If updating multiple providers, repeat the full workflow for each provider sequentially — collecting and confirming a separate git identity for each.

---

## Important Rules

- **Git identity isolation (CRITICAL):** Each provider has its own dedicated git identity. NEVER use one provider's identity for another. Confirm the identity with the user, and verify after every commit. This is the single most important rule in this skill.
- **Identity source:** This skill does NOT hardcode git identities. Load from the user's local config file if available, otherwise ask the user. Always confirm before use.
- **`gh` auth:** Before creating any PR, verify `gh auth status` matches the provider's GitHub account. Switch with `gh auth switch` if needed.
- **Live model data:** Fetch model information from the provider's API at integration time. Never hardcode model lists unless the project explicitly requires static model definitions.
- **UTM parameters:** All provider URLs in README, docs, and code comments must include `?utm_source=github_{project_name}`.
- **Minimal diff:** Follow existing project patterns exactly. Do not refactor or "improve" existing code. Only add what's necessary for the new provider.
- **One provider per PR:** Each provider gets its own branch and PR.
- **No secrets:** Never commit API keys, .env files, or other sensitive data.
- **Ask before acting:** If the project structure is unusual or you're unsure about a decision, ask the user rather than guessing.

---

## Resources

- **[Provider Data](./references/providers.md)** — API configuration for all 3 providers (IDs, URLs, intros, brand info). Does NOT contain git identities.
- **[Model Processing](./references/model-processing.md)** — Model API format, price conversion, feature/modality mapping rules
- **[Phase 1: Setup & Analysis](./references/phase1-setup-analysis.md)** — Git identity collection, clone/fork workflow, project analysis
- **[Phase 2: Implementation](./references/phase2-implementation.md)** — Copy-reference pattern, registry updates, tests, config
- **[Phase 3: Verification & Docs](./references/phase3-verification-docs.md)** — Build/test verification, README updates, logo placement
- **[Report Template](./references/reporting-template.md)** — Summary report and PR description templates
- **[Pre-Submission Checklist](./references/checklist.md)** — Final verification before creating PR
- **[Brand Assets](./assets/)** — SVG logos for all 3 providers (ppio/, novita/, jiekou/)
- **[Dify Plugin Update](./references/dify.md)** — Dify plugin model list update workflow, branch/package paths
