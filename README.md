# Feature-Development Marketplace (template)

This directory is a **template** for a standalone Claude Code plugin marketplace that distributes the `feature-development` plugin (and any future plugins you publish).

Lift the contents into a new GitHub repo — don't host the marketplace inside the plugin repo itself.

## Setup steps

1. **Create a new GitHub repo** for the marketplace, e.g. `<your-org>/claude-plugins`.

2. **Copy this directory's contents** into the root of that new repo:

   ```bash
   mkdir -p ~/path/to/new/marketplace-repo
   cp -r marketplace-template/. ~/path/to/new/marketplace-repo/
   cd ~/path/to/new/marketplace-repo
   git init
   git add .
   git commit -m "Initial marketplace"
   git remote add origin git@github.com:<your-org>/claude-plugins.git
   git push -u origin main
   ```

3. **Edit `.claude-plugin/marketplace.json`** and replace the placeholders:

   - `<your-org>` — your GitHub org or username.
   - `<plugin-repo>` — the repo where the `feature-development` plugin lives (typically a separate repo from this marketplace).
   - `<your-name>` — author name shown to installers.

4. **Make sure the plugin repo is also pushed to GitHub.** The marketplace points at it via `source: "github:<your-org>/<plugin-repo>"`. The plugin repo needs `.claude-plugin/plugin.json` at its root (already present in this codebase).

5. **Tag a release** (recommended): tag the plugin repo with `v0.1.0` matching the `version` in `marketplace.json`. Claude Code can resolve to a tag, branch, or commit — tags are the stablest.

## How engineers install the plugin from your marketplace

Once both repos are pushed and `marketplace.json` placeholders are filled in:

```
/plugin marketplace add <your-org>/claude-plugins
/plugin install feature-development
```

That's it. The marketplace is added once per machine; subsequent updates land via:

```
/plugin update feature-development
```

## Adding more plugins later

Append additional entries to the `plugins` array in `marketplace.json`. Each entry needs at minimum `name`, `source`, and `description`. After committing and pushing, engineers re-fetch with:

```
/plugin marketplace update <your-org>/claude-plugins
```

## Validating before pushing

Run the plugin validator on your plugin repo before publishing:

```
claude plugin validate
```

The validator catches manifest schema errors, missing required fields, and common structural issues. Don't push a marketplace pointing at a plugin that fails validation — installers will hit the same error.

## Versioning

Use semver in the `version` field. Bump on every plugin release. Engineers running `/plugin update` will pick up the new version when the marketplace catalog refreshes.

If you want pinned-by-commit instead of version-driven, set `source` to include a ref:

```json
"source": "github:<your-org>/<plugin-repo>@<commit-sha-or-tag>"
```

Stable for production rollouts; less convenient for fast iteration.

## Notes

- This marketplace is **independent** of Anthropic's official `claude-plugins-community` catalog. You can submit to both — they don't conflict.
- The marketplace repo can be private. Engineers installing from a private marketplace need GitHub auth configured for `git clone` to that repo.
- Only the `.claude-plugin/marketplace.json` file is load-bearing. Everything else (this README, any extra docs) is for humans.
