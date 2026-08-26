# Zemax Learning Journal — site setup

This is a minimal Jekyll site, pre-configured to run on **GitHub Pages for free** with zero build setup.

## Deploy it (15 minutes, one time)

1. Create a new GitHub repository named exactly `your-username.github.io` (replace `your-username` with your actual GitHub username — this exact naming is what makes GitHub Pages auto-publish it).
2. Upload everything in this `site/` folder to the root of that repository (drag-and-drop works fine via the GitHub web UI, or use git).
3. In the repo, go to **Settings → Pages**, set source to the `main` branch, root folder. Save.
4. Wait 1–2 minutes, then visit `https://your-username.github.io` — your site is live.
5. Edit `_config.yml`: replace `github_username` and `linkedin_username` with your real handles, and update the title/description if you want.

No local installation, no Ruby/Jekyll setup required — GitHub builds it for you automatically every time you push a change.

## Weekly workflow

1. Copy `_posts/2026-08-25-week-1-setup-and-first-system.md` as a template for each new post.
2. Rename it `YYYY-MM-DD-short-title.md` (the date in the filename controls the publish date).
3. Fill in: problem → approach → result → what surprised me. Add screenshots (drop images in an `/assets/` folder, reference with `![alt](/assets/filename.png)`).
4. Push to GitHub — the live site updates automatically within a minute or two.
5. Update `projects.md` to link the new post once it's a milestone-level project.
6. Cross-post a 3–5 line summary to LinkedIn linking back to the full post.

## Optional local preview (not required)

If you want to preview changes before pushing:
```
gem install bundler jekyll
bundle exec jekyll serve
```
Then open `http://localhost:4000`. Entirely optional — GitHub Pages will build it for you regardless.
