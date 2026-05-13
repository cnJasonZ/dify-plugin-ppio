# Pre-Submission Checklist

Run through this checklist before creating the PR.

---

## Git Identity (CRITICAL)

- [ ] Git identity loaded from local config or collected from user for this specific provider
- [ ] Identity was confirmed by the user before any commits
- [ ] `git config user.name` matches the confirmed name for THIS provider
- [ ] `git config user.email` matches the confirmed email for THIS provider
- [ ] Identity is NOT shared with any other provider (cross-provider usage is forbidden)
- [ ] Branch name is `feat/{provider-id}`
- [ ] `git log -1 --format="%an <%ae>"` shows the correct provider identity
- [ ] `gh auth status` shows the correct GitHub account for this provider

## Code Implementation (Required)

- [ ] Provider file created following the reference provider's pattern
- [ ] Provider ID, name, base URL, and env var are correct for this provider
- [ ] All class/function names reference the new provider (no old provider names left)
- [ ] Provider appears everywhere the reference provider appears in the codebase
- [ ] No hardcoded API keys or sensitive values
- [ ] Code follows project's style guidelines

## Code Implementation (If Applicable)

- [ ] Provider registered in index/registry file
- [ ] TypeScript type unions updated (e.g., `ProviderName` type)
- [ ] Provider-specific error handling implemented (if needed)

## Model Data (Required)

- [ ] Model information fetched from live API (not hardcoded, unless project requires static list)
- [ ] Prices divided by 10000 (correct unit conversion)
- [ ] Correct currency labeled (RMB for PPIO, USD for Novita/Jiekou)
- [ ] Only `status === 1` models included
- [ ] `serverless` feature value ignored
- [ ] Video/image/audio capability derived from `input_modalities`/`output_modalities`, NOT `features`

## Tests (If Project Has Tests)

- [ ] Test file created following project's naming convention
- [ ] Tests cover: initialization, model listing, chat completion, error handling
- [ ] All new tests pass
- [ ] All existing tests still pass (no regressions)

## Documentation

- [ ] README.md updated with new provider entry
- [ ] Provider entry follows existing format exactly
- [ ] All provider URLs include `?utm_source=github_{project_name}`
- [ ] Correct intro text used (from `references/providers.md`)
- [ ] All links are valid and working

## Assets

- [ ] Logo file added to project's asset directory (if project uses logos)
- [ ] Logo file named following project's naming convention

## Configuration

- [ ] `.env.example` updated with new `{ENV_VAR_NAME}` entry
- [ ] Other config example files updated (if applicable)
- [ ] No actual API keys in any committed files

## Build & Quality

- [ ] Dependencies install successfully
- [ ] Project builds without errors
- [ ] No linting errors (if project uses linter)
- [ ] No type errors (if TypeScript project)
- [ ] Code is properly formatted

## Final Check

- [ ] Diff is minimal — only changes necessary for the new provider
- [ ] No unrelated files changed
- [ ] No `.env`, `node_modules`, `.DS_Store`, or other unwanted files staged
- [ ] Summary report prepared (not committed to repo)
- [ ] Ready to create Pull Request
