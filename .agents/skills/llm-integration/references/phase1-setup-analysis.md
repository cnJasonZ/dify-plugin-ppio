# Phase 1: Setup & Analysis

---

## Step 1: Load Provider Config

Read `references/providers.md` and load the block for the specified provider. Note the provider's:
- ID, name, base URL, ENV variable
- All URLs (will be needed for README updates)

---

## Step 2: Resolve Git Identity

**CRITICAL: Each provider MUST have its own dedicated git identity. Using the wrong identity for a provider is a serious error — e.g., submitting a PR for PPIO with Novita's git account is strictly forbidden.**

**Resolution order:**

1. **Check for a local identity config file** in the user's workspace. Look for files like `IDENTITIES.md`, `TOOLS.md`, `.claude/identities.json`, or similar files that may map provider IDs to git identities. If found, extract the identity for the specified provider.

2. **If no local config exists**, ask the user to provide the identity:

> "To submit a PR for **{Provider Name}**, I need the git identity dedicated to this provider. Please provide:
> 1. `git user.name` for {Provider Name}
> 2. `git user.email` for {Provider Name}
> 3. GitHub username for {Provider Name} (for fork URL and `gh auth switch`)
>
> **Important:** This identity must be the one exclusively assigned to {Provider Name}. Do NOT provide an identity belonging to another provider."

3. **In either case, confirm the identity back to the user before proceeding:**

> "I will use the following git identity for all {Provider Name} commits:
> - `user.name`: {name}
> - `user.email`: {email}
> - GitHub username: {username}
>
> Is this correct?"

DO NOT proceed until the user confirms.

Set per-repo git identity (never set globally):

```bash
# After cloning the repo:
git config user.name "{user_provided_name}"
git config user.email "{user_provided_email}"

# Verify:
git config user.name
git config user.email
```

If the remote URL uses a custom SSH host alias (e.g., `github.com-custom`), ensure the user has the corresponding SSH key configured in `~/.ssh/config`. If unsure, ask the user to verify.

**Switch `gh` CLI to the correct account** (needed later for PR creation):

```bash
# Check current gh auth status
gh auth status

# Switch to the provider's GitHub account if needed
gh auth switch --user {provider_github_username}
```

If the provider's account is not listed in `gh auth status`, ask the user to run `gh auth login` to add it before proceeding.

---

## Step 3: Fork & Clone

**If fork is needed** (contributing to a repo under the provider's dedicated account):

Use the fork remote URL provided by the user in Step 2.

Fork the target repo on GitHub using the provider's dedicated account, then clone the fork:

```bash
git clone {user_provided_fork_url}
cd {repo_name}
git config user.name "{user_provided_name}"
git config user.email "{user_provided_email}"

# Add upstream remote for staying in sync:
git remote add upstream {original_repo_url}
git fetch upstream
```

**If contributing directly** (repo accepts PRs from any fork):

```bash
git clone {github_url}
cd {repo_name}
git config user.name "{user_provided_name}"
git config user.email "{user_provided_email}"
```

**Create feature branch:**

```bash
git checkout -b feat/{provider-id}
```

**Verify identity before any commits:**
```bash
git config user.name && git config user.email
```

After the first commit, double-check the author:
```bash
git log --oneline -1 --format="%an <%ae>"
```

---

## Step 4: Read Project Guidelines

Read these files if they exist (check in order):
- `README.md`
- `CONTRIBUTING.md`
- `.github/CONTRIBUTING.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `DEVELOPMENT.md`
- `docs/` directory

Document:
- How to install dependencies and build
- How to run tests
- Code style (indentation, naming, formatting)
- Commit message format
- Where LLM providers are implemented
- Any explicit instructions for adding providers

---

## Step 5: Locate Existing Providers

Search for existing LLM provider implementations. Use multiple search terms to cast a wide net:

```bash
# Search for known provider names in source files
grep -r "openrouter\|together\|siliconflow\|fireworks\|groq\|deepseek\|anthropic\|cohere\|mistral" \
  --include="*.ts" --include="*.js" --include="*.py" --include="*.go" --include="*.rs" --include="*.rb" \
  -l

# Search in config files too
grep -r "openrouter\|together\|siliconflow" \
  --include="*.json" --include="*.yaml" --include="*.toml" --include="*.env*" \
  -l
```

Document your findings:

```
Provider files found:
- src/providers/openrouter.ts
- src/providers/together.ts
- ...

Pattern: each provider is a [describe pattern]

Registry: src/providers/index.ts (exports all providers)

Tests: tests/providers/*.test.ts

Config: .env.example
```

---

## Step 6: Analyze the Pattern

Read at least 2 existing provider implementations in full. Document:

1. **File structure:** One file per provider? Multiple files? Directory per provider?
2. **Class/function shape:** Interface implemented, methods required, constructor signature
3. **Model handling:** Hardcoded model list or dynamic API fetch?
4. **Configuration:** How is base URL and API key passed?
5. **Registry:** Is there an index file that exports/registers providers?
6. **Test structure:** Framework, naming convention, what is mocked, what is tested

---

## Step 7: Choose Reference Provider

If the project has multiple existing providers, ask the user which one to use as the reference:

> "I found the following existing providers: [list]. Which one should I copy as the reference for the new [Provider Name] integration?"

If only one exists, use it without asking.

---

## Phase 1 Checkpoint

Before proceeding to Phase 2, verify:

- [ ] Provider config loaded from `references/providers.md`
- [ ] Git identity set correctly (per-repo, not global)
- [ ] Repo cloned, branch `feat/{provider-id}` created
- [ ] Project build/test commands understood
- [ ] Provider implementation pattern clearly identified
- [ ] Reference provider selected
- [ ] File naming convention understood
- [ ] Registry/index pattern understood (if applicable)
- [ ] Test pattern understood (if applicable)
