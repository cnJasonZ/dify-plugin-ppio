# Step 4: Generate Summary Report

After completing all integration steps, provide a comprehensive summary to the user. **Do NOT commit this report to the repository.**

---

## Report Template

```markdown
## Integration Summary Report

### Provider Information
- **Provider**: [Provider Name] ([Provider ID])
- **Base URL**: [base_url]
- **API Key Variable**: [ENV_VAR_NAME]
- **Git Identity**: [user.name] <[user.email]>
- **Branch**: feat/[provider-id]

### Changes Made

#### Files Created
1. `[path/to/provider/file]` — Provider implementation ([X] lines)
2. `[path/to/test/file]` — Test suite ([X] tests) [if applicable]

#### Files Modified
1. `[provider/index]` — Added provider registration [if applicable]
2. `.env.example` — Added [ENV_VAR_NAME] template
3. `README.md` — Added provider to supported list
4. `[other config files]` — Updated configuration examples [if applicable]

#### Assets Added
- `[path/to/logo/file]` — Provider logo [if applicable]

### Testing Results

#### Build
- Status: [✅ Success / ❌ Failed]
- Command: `[build command]`
- Notes: [any notes]

#### Tests
- Status: [✅ All pass / ❌ Failed / N/A]
- Command: `[test command]`
- Results: [X passed, Y failed]
- New tests added: [X tests for new provider]

#### Manual Testing [if API key was provided]
- [✅/❌] Provider initialization
- [✅/❌] Model listing ([X] models retrieved)
- [✅/❌] Chat completion
- [✅/❌] Streaming [if applicable]
- [✅/❌] Error handling

#### Code Quality
- Linter: [✅ Pass / ❌ Errors / N/A]
- Type checker: [✅ Pass / ❌ Errors / N/A]

### UTM Parameters
- All provider URLs include `?utm_source=github_[project_name]`: [✅ Yes / ❌ Missing]

### Logo
- Logo added to project: [✅ Yes / ❌ No / N/A — project doesn't use logos]

### Issues Encountered
[List any issues and their resolution, or "None"]

### Next Steps
1. [✅/❌] All implementation complete
2. [✅/❌] All tests passing
3. [✅/❌] Documentation updated
4. [✅/❌] Ready to create Pull Request

[If not ready, list remaining work:]
- [ ] [specific task]

---

### Suggested Commit Message

feat: add [Provider Name] provider support

- Add [ProviderClass] with OpenAI-compatible API
- Implement model listing, chat completion[, streaming]
- Add test suite with [X] test cases [if applicable]
- Update README and configuration examples
```

---

## PR Description Template

```markdown
## Description

Adds support for [Provider Name] ([native name if applicable]) as an LLM provider.

[Provider Name] is [one-line description from providers.md].

## Changes

- ✅ New provider implementation following existing patterns
- ✅ Tests added and passing [or "N/A — project has no tests"]
- ✅ README updated with provider information and links
- ✅ `.env.example` updated with API key template
- ✅ Provider logo added [if applicable]

## Setup

1. Get your API key from [[Provider Name] Dashboard]([api_key_url])
2. Add to `.env`: `[ENV_VAR_NAME]=your-api-key`
3. [Any provider-specific setup steps]

## Testing

- All existing tests pass
- [X] new tests added for [Provider Name] [if applicable]
- Manual testing completed: model listing, chat completion[, streaming]

## Links

- [[Provider Name] Homepage]([homepage_url])
- [API Documentation]([api_docs_url])
- [API Key Management]([api_key_url])

## Checklist

- [ ] Code follows project style guidelines
- [ ] Tests added and passing [or N/A]
- [ ] Documentation updated
- [ ] No linting errors
```

---

## Checklist Before Creating PR

Run through `references/checklist.md` before presenting this report and creating the PR.
