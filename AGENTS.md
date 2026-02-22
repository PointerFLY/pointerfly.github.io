# Agent Instructions for PointerFLY's Jekyll Site

This document provides instructions for AI agents and developers working on this Jekyll-based website.

## 1. Environment Setup

Ensure you have Ruby and Bundler installed.

To install dependencies:

```bash
bundle install
```

## 2. Running Locally

To start the local development server:

```bash
bundle exec jekyll serve
```

To include posts dated in the future:

```bash
bundle exec jekyll serve --future
```

The site will be available at `http://localhost:4000/`.

## 3. Creating a New Post

Posts are located in the `_posts/` directory.

### File Naming Convention
Files must be named in the following format:
`YYYY-MM-DD-title-slug.md`

Example: `2026-02-22-my-new-post.md`

### Front Matter Template
Every post must start with valid YAML front matter. Use the following template:

```markdown
---
title: "Your Post Title"
date: YYYY-MM-DD HH:MM:SS +/-TZ
categories: [category1, category2]
---

Your content starts here. Markdown is supported.
MathJax/LaTeX is supported (e.g., $$ \frac{a}{b} $$).
```

*Note: Ensure the `date` matches the filename date.*

## 4. Deployment

This site is hosted on GitHub Pages. Deployment is automated via GitHub Actions or the standard GitHub Pages build process when changes are pushed to the default branch (usually `main` or `master`).

To deploy:
1.  Stage your changes: `git add .`
2.  Commit your changes: `git commit -m "Add new post: Title"`
3.  Push to the repository: `git push origin main`

## 5. Directory Structure Key

- `_posts/`: Blog posts.
- `_config.yml`: Site configuration (title, url, plugins).
- `_includes/`: HTML partials (e.g., `latex.html`).
- `Gemfile`: Ruby dependencies.
