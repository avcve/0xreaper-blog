# 0xReaper

A Jekyll-based security writeup blog, built to run on GitHub Pages with zero build step on your end.

## Deploy it

This site is preconfigured as a **project page**, live at:

```
https://avcve.github.io/0xreaper-blog
```

Steps:

1. On GitHub, create a new repo named exactly `0xreaper-blog` under your `avcve` account. Public, no README/gitignore/license auto-init (you already have those here).
2. Push everything in this folder to the repo root:

```bash
cd 0xreaper-site
git init
git add .
git commit -m "initial site"
git branch -M main
git remote add origin https://github.com/avcve/0xreaper-blog.git
git push -u origin main
```

3. On GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main` / `(root)`**.
4. Wait a minute or two, then visit `https://avcve.github.io/0xreaper-blog`. No local Jekyll install needed — GitHub builds it on every push.

If you ever rename the repo, update `baseurl` in `_config.yml` to match (`/new-repo-name`), or the CSS/nav links will 404.

## Edit your identity

Open `_config.yml` and update:

- `title`, `tagline`, `description`
- `github_username`, `x_username`
- `baseurl` (only if this is a project page, see above)

## Post regularly

Every writeup is a markdown file in `_posts/`, named `YYYY-MM-DD-title-slug.md`. To publish a new one:

1. Copy one of the existing files in `_posts/` as a starting point.
2. Rename it with today's date and a slug for the title.
3. Update the front matter at the top:

```yaml
---
title: "Your Post Title"
tags: [tag-one, tag-two, writeup]
thumb: idor        # one of: access-control | idor | payment | recon | default
severity: High      # optional — omit if not a vuln writeup. Options: Critical | High | Medium
---
```

4. Write the post in markdown below the `---`.
5. Commit and push. It's live.

## Before you publish anything from a real engagement

- Never use a real client name, real domain, real endpoint path, or real screenshot without explicit permission — check your NDA every time, not just the first time.
- Replace real IDs, tokens, amounts, and names with fictional ones that still demonstrate the bug class.
- If a screenshot contains another person's real PII (name, ID, contact info), don't publish it — redact it or don't use it at all. Blurring in an image editor is not always reliable; when in doubt, recreate the relevant detail as text or a mocked-up graphic instead.

## Structure

```
_config.yml          site settings
_layouts/             default.html (shell), post.html (writeup template)
_includes/             sidebar.html, rightbar.html, thumb.html (svg icon picker)
_posts/                your writeups, one markdown file each
assets/css/style.css  all styling
index.html            home feed
about.html            about page
archives.html         posts grouped by year
categories.html        posts grouped by tag
tags.html              tag cloud with counts
```

## Local preview (optional)

If you want to preview before pushing, you'll need Ruby + Bundler installed:

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://localhost:4000`. Not required for deployment — GitHub Pages builds it remotely regardless.
