# Dify Plugin: Model List Update

PPIO, Novita AI, and Jiekou.AI each have an existing Dify plugin. This workflow updates the model list in those plugins and submits the updated package to the `dify-plugins` marketplace repo.

---

## Marketplace Branch & Package Paths

Each provider has a dedicated branch and package path in the `dify-plugins` marketplace repo:

| Provider | Branch | Package Path | Remote URL |
|----------|--------|-------------|------------|
| PPIO | `feat/ppio` | `./ppio/ppio/ppio.difypkg` | `git@github.com:cnJasonZ/dify-plugins.git` |
| Novita AI | `feat/novita` | `./novita/novita/novita.difypkg` | `git@github.com:jasonhp/dify-plugins.git` |
| Jiekou.AI | `feat/jiekouAI` | `./JiekouAI/JiekouAI/jiekouai.difypkg` | `git@github.com:captain1379/dify-plugins.git` |

---

## Dify Plugin Repo URLs

Each provider has a dedicated dify plugin repo:

| Provider | Repo URL |
|----------|----------|
| PPIO | `git@github.com:cnJasonZ/dify-plugin-ppio.git` |
| Novita AI | `git@github.com:novitalabs/dify-plugin-novita.git` |
| Jiekou.AI | `git@github.com:captain1379/dify-plugin-jiekou.git` |

---

## Step 0: Collect Git Identity and Repo URLs

**CRITICAL: Each provider MUST use its own dedicated git identity. Cross-provider identity usage is strictly forbidden.**

Before starting, ask the user to provide the following for the specified provider:

> "For updating the Dify plugin for **{Provider Name}**, I need:
> 1. `git user.name` for {Provider Name}
> 2. `git user.email` for {Provider Name}
>
> **Important:** This must be the identity exclusively assigned to {Provider Name}."

If handling multiple providers in one session, collect identity + URLs for **each** provider separately. Confirm each one with the user before proceeding.

---

## Workflow

### Step 1: Set Up Plugin Repo

Ask the user if they already have the plugin repo cloned locally. If not, clone it:

```bash
git clone {user_provided_plugin_repo_url}
cd {plugin_repo_dir}
```

Set the provider's git identity (from user input in Step 0):

```bash
git config user.name "{user_provided_name}"
git config user.email "{user_provided_email}"
```

### Step 2: Sync Models

Detect and activate the Python virtual environment:

```bash
# Detect venv location — check common paths in order
if [ -d ".venv" ]; then
  source .venv/bin/activate
elif [ -d "venv" ]; then
  source venv/bin/activate
elif [ -d "env" ]; then
  source env/bin/activate
else
  echo "No venv found. Check with user."
fi
```

If none of the common paths exist, ask the user for the correct venv location before proceeding.

Run the sync script:

```bash
python models/llm/sync_models.py
```

Wait for completion. This creates or updates YAML files under `models/llm/` — one YAML file per model.

**Check if there are actual changes** after the sync:

```bash
git status --short models/llm/
```

If there are **no new, modified, or deleted files** under `models/llm/`, the model list is already up-to-date. **Stop the entire workflow** and inform the user:

> "The model list for {Provider Name} is already up-to-date. No changes needed."

Do NOT proceed to Step 3 or any subsequent steps if there are no changes.

### Step 3: Bump Version

Open `manifest.yaml` and increment **both** `version` fields by one patch version:

```
1.0.0 → 1.0.1
1.0.1 → 1.0.2
```

### Step 4: Commit and Push Plugin Repo

Stage all changes, commit, and push:

```bash
git add .
git commit -m "chore: sync model list"
git push
```

**Verify** the commit author matches the provider's git identity before pushing:
```bash
git log -1 --format="%an <%ae>"
```

### Step 5: Build Plugin Package

**Prerequisite check:** Verify `dify` CLI is installed before building:

```bash
if ! command -v dify >/dev/null 2>&1; then
  echo "ERROR: dify CLI is not installed."
  echo "Install it with: pip install dify-plugin-daemon"
  echo "See: https://github.com/langgenius/dify-plugin-daemon"
  exit 1
fi
```

If `dify` CLI is not installed, ask the user to install it before proceeding.

Navigate to the **parent directory** of the plugin repo and run:

```bash
cd ..
dify plugin package {plugin_repo_dir} -o ./{plugin_repo_dir}/{provider_name}.difypkg
```

This generates a `.difypkg` file inside the plugin repo directory.

### Step 6: Set Up Marketplace Repo

Ask the user if they already have the `dify-plugins` marketplace repo cloned locally. If not, clone it.

The marketplace repo needs a **dedicated remote** for this provider. Check with `git remote -v`. If the remote for this provider is missing, add it using the URL collected in Step 0:

```bash
git remote add {provider_id} {user_provided_marketplace_remote_url}
```

If handling multiple providers, each provider should have its own named remote.

### Step 7: Switch Branch and Update Package

Switch to the provider's branch and set the correct git identity:

```bash
git checkout {provider_branch}
git config user.name "{user_provided_name}"
git config user.email "{user_provided_email}"
```

Fetch and rebase from the provider's remote to stay up-to-date:

```bash
git fetch {provider_id}
git rebase {provider_id}/{provider_branch}
```

**Clean up old packages** in the target directory before copying. There may be leftover `.difypkg` files from previous versions:

```bash
# Remove old .difypkg files in the package directory
rm -f {marketplace_package_dir}/*.difypkg
```

Copy the generated `.difypkg` file to the correct path:

```bash
cp {plugin_repo_dir}/{provider_name}.difypkg {marketplace_package_path}
```

### Step 8: Commit and Push Marketplace Repo

```bash
git add .
git commit -m "chore: update {provider_name} model list"
git push {provider_id} {provider_branch}
```

**Verify** the commit author matches the provider's git identity:
```bash
git log -1 --format="%an <%ae>"
```

### Step 9: Create PR

**Switch `gh` CLI to the correct GitHub account** before creating the PR. The `gh` CLI must be authenticated as the provider's GitHub account, not any other account:

```bash
# Check current gh auth status
gh auth status

# If the wrong account is active, switch:
gh auth switch --user {provider_github_username}
```

If the provider's account is not listed, ask the user to run `gh auth login` to add it.

**Read the PR template** from the repo (do NOT use a cached copy — the template may change over time):

```bash
cat .github/pull_request_template.md 2>/dev/null || cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null
```

**Fill in the template according to these rules:**

1. **Metadata section** — Extract from the plugin's `manifest.yaml`:
   - `Plugin Author`: from `manifest.yaml` author field
   - `Plugin Name`: from `manifest.yaml` name field
   - `Repository URL`: the plugin repo's **GitHub HTTPS URL** (e.g., `https://github.com/username/dify-plugin-ppio`), NOT the SSH clone URL
2. **Submission Type** — Check `Version update for existing plugin`
3. **Description** — Write: `Update model list`
4. **Checklist** — Check all items
5. **Documentation Checklist** — Check all items
6. **Privacy Protection Information**:
   - Data Collection: `No`
   - Privacy Policy checkbox: Check it
7. **Unknown sections** — If the template contains new sections or fields not listed above, ask the user how to fill them before creating the PR. Do NOT guess or leave them blank.

Create the PR:

- **Title:** `Update {Provider Name} model list`

```bash
gh pr create --title "Update {Provider Name} model list" --body "$(cat <<'EOF'
{filled PR template content with all sections above}
EOF
)"
```

After creating the PR, present the PR URL to the user.

---

## Checkpoint

Before marking complete, verify:

- [ ] Git identity collected from user and confirmed for this provider
- [ ] Python venv detected and activated (not assumed to be `.venv`)
- [ ] `models/llm/` YAML files updated via sync script
- [ ] `manifest.yaml` version bumped (both fields)
- [ ] Plugin repo committed and pushed with **correct provider identity** (verify with `git log`)
- [ ] `dify` CLI is installed and `.difypkg` file generated successfully
- [ ] Marketplace repo has the provider's remote configured
- [ ] Old `.difypkg` files cleaned up before copying new one
- [ ] Package copied to correct path in marketplace repo
- [ ] Marketplace repo committed and pushed with **correct provider identity** (verify with `git log`)
- [ ] `gh auth status` confirms correct GitHub account for PR creation
- [ ] PR body follows the repo's PR template (from `.github/pull_request_template.md`)
- [ ] PR created and URL presented to user
