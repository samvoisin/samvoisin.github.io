Website hosted from `gh-pages` branch in `_site` directory.

To build site:

1. Go to `gh-pages` branch.
2. Merge any changes from other branches.
3. Build `_site` directory with `bundle exec jekyll build`.
4. Push new `_site` directory to `gh-pages` remote.

To review changes locally:

1. On the appropriate branch, run `bundle exec jekyll serve`.
2. Navigate to `localhost:4000`.