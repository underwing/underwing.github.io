# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Chinese-language personal blog built with Hugo and deployed to GitHub Pages. The site uses the **hugo-theme-terminal** theme as a git submodule.

## Commands

```bash
# Start local dev server with live reload
hugo server

# Build for production (outputs to ./public)
hugo --minify

# Create a new post (uses archetypes/posts.md)
hugo new posts/my-new-post/index.md
```

## Architecture

- **Content**: Markdown posts in `content/posts/<post-name>/index.md`
- **Frontmatter fields**: title, date, author, tags, description, cover, readingTime, showFullContent
- **Theme**: `themes/hugo-theme-terminal` — cloned as a git submodule from panr/hugo-theme-terminal
- **Deployment**: GitHub Actions (`.github/workflows/hugo.yml`) deploys `./public` to GitHub Pages on push to main
- **Config**: `hugo.toml` — site URL, language (zh-cn), menu, and theme params
- **Customizations**: `layouts/` is empty (theme is a submodule), overrides go in root-level `static/style.css`

## Post Format

Posts use the archetype from `themes/hugo-theme-terminal/archetypes/posts.md`. Frontmatter example:

```yaml
+++
title = "Post Title"
date = "2026-04-25T18:31:14+08:00"
author = "Amos"
tags = ["tag1", "tag2"]
description = "Description for excerpt"
cover = ""  # image path in static/
readingTime = true
showFullContent = false
+++
```

## Theme Notes

- Requires Hugo Extended v0.90.x+
- Uses Chroma for syntax highlighting
- The theme is a git submodule, not directly editable — custom CSS can go in `static/style.css`
