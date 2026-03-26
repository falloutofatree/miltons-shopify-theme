# shopify-theme-init

Pragmatic workflow for developing and maintaining stores built on off-the-shelf Shopify themes:

* Keep theme code in version control
* Pull in Shopify Admin code/template changes (including Theme Store updates)
* Exclude content and settings from Git

**v1.0.0**

---

## 🧹 Install

### 1. Install a theme via Shopify Admin

Choose a theme from the Shopify Theme Store.
Note its numeric ID (visible in the URL when previewing or editing).

### 2. Pull in the workflow scaffolding

```bash
git init
git remote add workflow https://github.com/falloutofatree/shopify-theme-init.git
git pull workflow main
git remote remove workflow
```
This pulls in `.gitignore`, `.shopifyignore`, `shopify-theme.toml`, and this README from the workflow repo, then detaches from it.

### 3. Configure your local environment

Edit `shopify.theme.toml`:

```toml
[environments.default]
store = "*yourstore*.myshopify.com"
theme = "*your-theme-id*"
```

### 4. Pull theme files

Install [Shopify CLI](https://shopify.dev/docs/themes/tools/cli) if needed.

Then pull down your theme.

```bash
shopify auth login
shopify theme pull
```

### 5. Commit to your repo

```bash
git add . && git commit -m "feat: initial commit"
git remote add origin git@github.com:[your-org]/[theme-repo].git
git push -u origin main
```

You're ready to develop.

---

## 🧑‍💻 Usage

```bash
shopify theme pull && git diff  # Pull and inspect any admin-side changes
# Make your changes
shopify theme dev               # Preview locally
git add . && git commit -m "feat: initial commit"
git push origin main
shopify theme push              # Deploy (skips content/settings)
```

---

## 🔄 Shopify Admin Updates

Here's how to deal with theme edits that happen directly in the Shopify Admin:

* **Admin-side template edits**
  To check for edits to templates, code, or settings in the Shopify Admin affecting the codebase:
  Run `shopify theme pull && git diff` and inspect what changed.
  Review, commit, and push.

* **Admin-side theme version updates (applies only to themes installed from the Theme Store)**
  When a new version is available for a theme in the Shopify Admin and someone clicks Update,
  Shopify duplicates and applies changes within the duplicated theme; the original remains untouched.

  Important: The updated theme has a new ID. Make sure to update the theme ID in `shopify.theme.toml`.

  ```bash
  shopify theme list                          # Find the new theme ID
  vi shopify.theme.toml                       # Update theme ID
  shopify theme pull && git diff              # Inspect everything the update changed
  git add . && git commit -m "chore: theme update vX.X.X"
  ```

  ⚠️ Commit the raw update before making any fixes.

  Even if Shopify's update tool reports success, assume things broke. Check for:
  - visual regressions
  - broken sections/blocks
  - missing or reset theme settings
  - custom code that was overwritten or refactored out
  
  Resolve any issues in separate commits. When ready, set the theme as live!

  💡 If a theme update turns out to be too destructive, you can always republish the previous theme and apply the update manually.

---

## ⚠️ (Optional) Track settings

By default, `config/settings_data.json` is ignored.
If you want to version control site settings:

Comment out `config/settings_data.json` in `.gitignore`

**Warning:** We *strongly* recommend keeping the settings file ignored.
