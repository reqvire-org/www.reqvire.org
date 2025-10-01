# Reqvire Documentation Website

This repository contains the documentation website for Reqvire, hosted at [www.reqvire.org](https://www.reqvire.org).

## Managing Website Content

### Structure

All documentation files are located in the `docs/` folder.

### Editing Pages

1. Edit the markdown files in the `docs/` folder
2. Use standard GitHub Flavored Markdown syntax
3. Each page must have front matter with `layout` and `title`:
   ```yaml
   ---
   layout: page
   title: Your Page Title
   ---
   ```

### Adding New Pages

1. Create a new `.md` file in the `docs/` folder
2. Add front matter at the top
3. Link to it from other pages (typically from `index.md`)

### Theme Configuration

The site uses the **Minima** Jekyll theme, configured in `docs/_config.yml`.

#### Custom Styling

To customize the theme's appearance, edit `docs/assets/css/style.scss`.

### GitHub Pages Deployment

The site is automatically deployed from the `docs/` folder on the `main` branch.

**Configuration location:** Repository Settings → Pages → Source: Deploy from branch → Branch: main, /docs

### Local Development (Optional)

To preview the site locally:

```bash
cd docs
bundle install
bundle exec jekyll serve
```

Then visit `http://localhost:4000`

### CNAME Configuration

The `CNAME` file in the root directory maps the custom domain `www.reqvire.org` to GitHub Pages.
