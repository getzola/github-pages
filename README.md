# Build Zola sites for GitHub Pages

This GitHub Action builds a Zola site using a specific Zola version and uploads
a GitHub Pages artifact. Deployment is handled by GitHub’s official
`actions/deploy-pages` action and does not push commits to a `gh-pages` branch.

## Requirements

- GitHub Pages must be configured to use **GitHub Actions** in the repository settings
- A Zola version must be specified

## Usage

A basic build and deploy job looks like:

```yaml
name: Build and deploy Zola website
on:
  push:
    branches:
      - master

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v6.0.1

      - name: Build Zola + upload Pages artifact
        uses: getzola/github-pages@066755243e69f508fd1a74739fbf1a65f656c790
        with:
          zola_version: v0.22.0

  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Options

- `working_directory`: defaults to `.`, where you site is located.
- `output_dir`: defaults to `public`, where the Zola site is built
- `build_flags`: additional flags on top of basic `zola build`
- `check_links`: defaults to `false`, whether to run `zola check` before `zola build`
- `check_flags`: additional flags on top of basic `zola check`