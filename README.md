# samvoisin.github.io

Personal website ([www.samvoisin.com](https://www.samvoisin.com)), built with
[Jekyll](https://jekyllrb.com/) on the [al-folio](https://github.com/alshedivat/al-folio)
theme.

## Publishing

Push to `master`. The `.github/workflows/deploy.yml` GitHub Actions workflow
builds the site and deploys it to GitHub Pages automatically — no manual build
step and no branch juggling.

GitHub Pages is configured with **Source: GitHub Actions** (Settings → Pages).
The custom domain (`www.samvoisin.com`) is set there and reproduced via the
root `CNAME` file.

## Local preview

```bash
bundle install
bundle exec jekyll serve
```

Then open <http://localhost:4000>.
