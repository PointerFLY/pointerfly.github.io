# PointerFLY's Personal Website

## Project Overview

This is a personal website/blog built using **Jekyll**, a static site generator written in Ruby. The site uses the `minima` theme and supports Markdown (`kramdown` parser) and MathJax/LaTeX for math equations. It is designed to be hosted on GitHub Pages.

**Main Technologies:**
- **Jekyll:** Static site generator.
- **Ruby / Bundler:** For managing dependencies (`Gemfile`).
- **Markdown:** Content creation.
- **MathJax:** For rendering mathematical formulas.

## Directory Structure Key

- `_posts/`: Blog posts.
- `_config.yml`: Site configuration (title, url, plugins).
- `_includes/`: HTML partials (e.g., `latex.html`).
- `Gemfile`: Ruby dependencies.

## Building and Running

Ensure you have Ruby and Bundler installed.

**Install Dependencies:**
```bash
bundle install
```

### Installing New Gems

To add a new gem (plugin or dependency):

1.  Add the gem to the `Gemfile`:
    ```ruby
    gem "gem-name", "~> x.x"
    ```
2.  Install the new gem:
    ```bash
    bundle install
    ```
3.  If it's a Jekyll plugin, also add it to `_config.yml` under `plugins:` if required.

### Updating Gems

To update all gems to their latest compatible versions:

```bash
bundle update
```

To update a specific gem:

```bash
bundle update gem-name
```

**Run Local Development Server:**
Start the server and include future posts and drafts:
```bash
bundle exec jekyll serve --future --drafts
```
The site will be available at `http://localhost:4000/`.

## Development Conventions

### Creating Posts and Drafts
It is recommended to use `jekyll-compose` commands to create drafts and posts:

- **Create a draft:**
  ```bash
  bundle exec jekyll draft "My New Draft"
  ```
- **Publish a draft:**
  ```bash
  bundle exec jekyll publish _drafts/my-new-draft.md
  ```
- **Create a post directly:**
  ```bash
  bundle exec jekyll post "My New Post"
  ```

### Manual Post Creation
- **Location:** `_posts/`
- **Naming Convention:** `YYYY-MM-DD-title-slug.md` (e.g., `2026-02-22-my-new-post.md`)
- **Front Matter:** Every post must start with YAML front matter.
  ```markdown
  ---
  title: "Your Post Title"
  date: YYYY-MM-DD HH:MM:SS +/-TZ
  categories: [category1, category2]
  mathjax: true # Optional: enable math rendering if needed
  ---
  ```

### Deployment
Deployment is handled automatically via GitHub Pages when pushing to the default branch (`main` or `master`).
```bash
git add .
git commit -m "Your commit message"
git push origin main
```
