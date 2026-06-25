# Documentation: cloud-ci-cd-demo

## Overview
This project demonstrates a complete CI/CD (Continuous Integration / Continuous Deployment) pipeline using **GitHub Actions**. Every time code is pushed to the `main` branch, the pipeline automatically builds, tests, and deploys the site — with no manual steps required.

**Live site:** https://hiro-zeroth.github.io/cloud-ci-cd-demo/
**Repository:** https://github.com/hiro-zeroth/cloud-ci-cd-demo

---

## How It Works

The pipeline is defined in `.github/workflows/main.yml` and runs on GitHub's hosted infrastructure (`ubuntu-latest` runner). It consists of five steps that run in order:

1. **Checkout Code** – Pulls the latest version of the repository onto the runner so later steps have access to the files.
2. **Setup Node** – Installs Node.js (v18) on the runner, providing the environment needed to run JavaScript/npm commands.
3. **Install Dependencies** – Runs `npm install` to install any packages listed in `package.json`.
4. **Run Tests** – Runs `npm test`, which executes the test script defined in `package.json`. This is where automated testing would catch bugs before deployment.
5. **Deploy to GitHub Pages** – Uses the `peaceiris/actions-gh-pages` action to publish the contents of the repository to the `gh-pages` branch, which GitHub Pages serves as a live website.

If any step fails, the pipeline stops and the deployment does **not** happen — this prevents broken code from ever reaching the live site.

---

## What Triggers Deployment

The workflow is triggered automatically by this configuration:

```yaml
on:
  push:
    branches:
      - main
```

This means **any push to the `main` branch** — whether it's a direct commit or a merged pull request — automatically kicks off the full pipeline: build → test → deploy. No manual trigger, button click, or separate deployment step is needed. This is the core principle of Continuous Deployment: code that passes its checks goes live immediately.

Progress and history of every run can be viewed under the repository's **Actions** tab, and a live status badge in the README reflects whether the most recent run passed or failed.

---

## Security Setup

Two security practices are used in this pipeline:

1. **GitHub Secrets** – Sensitive values are never hardcoded into the workflow file or source code (which would expose them publicly, since this is a public repo). Instead, they're stored in **Settings → Secrets and variables → Actions** and referenced in the workflow using `${{ secrets.SECRET_NAME }}` syntax. For example:
   - `API_KEY` is stored as a repository secret and referenced (not printed) during the workflow run.
   - `GITHUB_TOKEN` is an automatically-provided secret used to authenticate the deployment step, so no personal credentials are ever exposed.

2. **Masked logging** – If a secret's actual value is ever accidentally referenced in a log output, GitHub automatically masks it as `***` in the Actions logs, providing a safety net against accidental exposure.

3. **Least-privilege permissions** – The workflow explicitly scopes its permissions:
   ```yaml
   permissions:
     contents: write
   ```
   This grants only the access needed to push to the `gh-pages` branch, rather than full unrestricted access.

---

## Summary of Skills Demonstrated

- Understanding CI vs. CD and how they work together
- Writing and editing GitHub Actions workflow YAML files
- Automating builds and tests on every push
- Automating deployment to a live public site (GitHub Pages)
- Securely managing secrets using GitHub's built-in secrets manager
- Monitoring pipeline runs and status via the Actions tab and a README status badge
