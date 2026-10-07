# Repository Guidelines

## Project Structure & Module Organization

This is a Jekyll-based GitHub Pages site.

- `_posts/`: blog articles, named `YYYY-MM-DD-slug.md`
- `_tools/`: tool introduction pages
- `_layouts/`, `_includes/`, `_sass/`: custom theme files
- `assets/`: images and other static assets
- `articles/`, `about.md`, `index.md`: generated/list pages and static pages
- `.github/workflows/pages.yml`: build and deploy workflow

## Build, Test, and Development Commands

```bash
bundle install                 # install dependencies
bundle exec jekyll serve       # local preview at http://localhost:4000
bundle exec jekyll build       # production build into _site
```

There is no test suite. Validation is done by building the site.

## Coding Style & Naming Conventions

- Write content in Chinese, using Markdown with Kramdown.
- Use front matter fields such as `title`, `date`, `category`, `tags`, `lede`, and `excerpt`.
- Use descriptive lowercase slugs for filenames and URLs.
- Keep images in `assets/images/`, referenced with `relative_url`.
- Use callout blocks (`{: .tip }`, `{: .note }`, `{: .pitfall }`, `{: .warn }`) for notes and warnings.

## Testing Guidelines

GitHub Actions runs `bundle exec jekyll build --trace` on every pull request. Ensure this check passes before requesting review. Manually verify generated pages and links if your change affects navigation, layouts, or permalinks.

## Commit & Pull Request Guidelines

Commit messages use the imperative mood and describe the change, for example `Add AI Agent and harness introduction article` or `Remove demo welcome post`.

For pull requests:

- Create a branch named `codex/<short-topic>` for agent-driven work.
- Include a concise summary and test plan.
- Link related issues when applicable.
- Add screenshots for visual changes.
- Wait for the GitHub Actions build to pass.
- Use squash merge and delete the branch after merge.

## Security & Configuration Tips

- Keep secrets out of repository files; deployment uses GitHub Pages environment permissions.
- Do not commit `_site/`, `.jekyll-cache/`, or `vendor/`.
