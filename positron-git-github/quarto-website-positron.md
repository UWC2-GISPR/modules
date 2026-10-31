# Quarto Website on GitHub Pages via Positron

Build a Quarto website in Positron and publish it free on GitHub Pages. The site is rendered into a `docs/` folder on your `main` branch, and GitHub Pages serves that folder. Everything lives on one branch, and you can see exactly what's published in the repo.

**No separate Quarto install needed.** Positron includes Quarto, so everything here uses Positron's own tools: the **R Console**, the **Preview** button, and the **Command Palette**. You don't need the `quarto` command in the terminal.

**Before you start:** work through Steps 1–5 of *Git & GitHub in Positron — Basic Reference* (install and configure Git, create a repo on GitHub, clone it). Use the settings below when you create the repo in its Step 3. You also need R installed and selected as Positron's interpreter.

---

## Step 1: Create and clone the repository

Follow the Basics guide Steps 3–5 with these settings:

- **Repository name:** e.g. `my-quarto-site` (this becomes part of the site URL)
- **Visibility:** **Public**. Free GitHub Pages needs a public repo on personal accounts.
- **Add a README file:** checked
- **Add .gitignore:** **R** template
- **Clone it** to a local folder that is **not** synced by OneDrive or Dropbox, and open the folder in Positron (**File → Open Folder…**)

---

## Step 2: Create the website files from the R Console

With the repo folder open in Positron, start an R session if one isn't running. Check that the Console is in the repo folder:

```r
getwd()   # should end in /my-quarto-site
```

If it doesn't, run `setwd("path/to/my-quarto-site")` first.

Then paste this block into the **Console** and press Enter. It creates the same starter files as Quarto's website template, sends the rendered site to `docs/`, adds `.nojekyll`, and updates `.gitignore`:

```r
# _quarto.yml: project settings. output-dir: docs is what makes GitHub Pages work
writeLines(c(
  "project:",
  "  type: website",
  "  output-dir: docs",
  "",
  "website:",
  "  title: \"My Quarto Site\"",
  "  navbar:",
  "    left:",
  "      - href: index.qmd",
  "        text: Home",
  "      - about.qmd",
  "",
  "format:",
  "  html:",
  "    theme: cosmo",
  "    css: styles.css",
  "    toc: true"
), "_quarto.yml")

# Home page
writeLines(c(
  "---",
  "title: \"My Quarto Site\"",
  "---",
  "",
  "This is a Quarto website built in Positron."
), "index.qmd")

# About page
writeLines(c(
  "---",
  "title: \"About\"",
  "---",
  "",
  "About this site."
), "about.qmd")

# Custom styles (empty for now)
writeLines("/* css styles */", "styles.css")

# .nojekyll: tells GitHub Pages to serve files as-is instead of running Jekyll
file.create(".nojekyll")

# Ignore Quarto's internal working folder (but NOT docs/, which is your published site)
gi <- if (file.exists(".gitignore")) readLines(".gitignore") else character()
if (!"/.quarto/" %in% gi) cat("\n/.quarto/\n", file = ".gitignore", append = TRUE)

list.files(all.files = TRUE, no.. = TRUE)
```

The last line lists the repo folder. You should see `_quarto.yml`, `index.qmd`, `about.qmd`, `styles.css`, `.nojekyll`, `.gitignore`, and `README.md`.

> **Prefer clicking?** You can also create each file with **File → New File…**, type the contents shown above, and save it in the repo folder. The R block just does this in one step.

> Don't use the Command Palette's **Quarto: Create Project** for this. It creates a *new subfolder*, which would put the site one level below your repo.

---

## Step 3: Preview and render

1. Open **`index.qmd`** and click **Preview** in the editor toolbar (or `Ctrl+Shift+K` / `Cmd+Shift+K`). Positron renders the page with its bundled Quarto and shows the site in the Viewer. It refreshes each time you save.
2. Edit `index.qmd` (change the title or add a paragraph), save, and watch the preview update.
3. When you're happy, render the **whole** site so `docs/` is complete: open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → **Quarto: Render Project**.

   A `docs/` folder appears containing `index.html`, `about.html`, and the rest of the site.

> **Why render the whole project?** Preview only rebuilds the page you're looking at. **Quarto: Render Project** rebuilds every page into `docs/`, so do it before each push.

> **R code in your pages?** If a `.qmd` page includes R code chunks, install the packages Quarto uses to run them (once): `install.packages(c("rmarkdown", "knitr"))`.

---

## Step 4: Commit and push

In **Source Control** (`Ctrl+Shift+G`):

1. Stage everything: `_quarto.yml`, the `.qmd` files, `styles.css`, `.gitignore`, `.nojekyll`, and the **`docs/`** folder.
2. Commit with a message like `Create Quarto website`.
3. Click **Sync Changes** to push.

On GitHub, refresh the repo page and check that `docs/` is there.

---

## Step 5: Turn on GitHub Pages

1. On GitHub, open the repo → **Settings** → **Pages** (left sidebar).
2. Under **Build and deployment**:
   - **Source:** Deploy from a branch
   - **Branch:** `main`
   - **Folder:** `/docs`
3. Click **Save**.

After a minute or two the page shows your site's address:

```
https://your-username.github.io/my-quarto-site/
```

You can watch the deployment under the repo's **Actions** tab.

---

## Updating the site later

The cycle is **Edit → Render → Commit → Push**, all inside Positron:

1. Edit your `.qmd` files (Preview as you go).
2. Command Palette → **Quarto: Render Project** to rebuild `docs/`.
3. **Source Control** → stage all → commit → **Sync Changes**.

GitHub Pages republishes automatically after each push.

**Adding a page:** create a new `.qmd` file (e.g. `projects.qmd`) with a `title:` in its header, add it under `navbar: left:` in `_quarto.yml`, then render the project.

### Site didn't update?

| Symptom | Likely cause | Fix |
|---|---|---|
| Old version still showing | Forgot to render, so `docs/` didn't change | **Quarto: Render Project**, then commit and push `docs/` |
| Only one page updated | Used Preview instead of rendering the project | **Quarto: Render Project**, then commit and push |
| Changes pushed but not live yet | Deployment still running, or the browser cached the old page | Check the **Actions** tab; hard-refresh the browser (`Ctrl+F5`) |
| 404 at the site URL | Pages set to `/ (root)`, or `docs/` was never pushed | Settings → Pages → Folder `/docs`; confirm `docs/index.html` is on GitHub |
| Styles or images missing | `.nojekyll` missing | In the R Console: `file.create(".nojekyll")`, then commit and push |
| New page doesn't appear in the menu | Page not listed in `_quarto.yml` | Add it under `website: navbar:` and re-render |
| `docs/` never appears | `output-dir: docs` missing from `_quarto.yml` | Add it under `project:` and re-render |
