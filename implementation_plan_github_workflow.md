# Implementation Plan: GitHub Action for Cloudflare Deployment

This plan outlines the steps to create a GitHub Actions workflow that automatically deploys the SvelteKit application to Cloudflare when a pull request to the `main` branch is merged.

## User Review Required

> [!IMPORTANT]
> This workflow requires two secrets to be configured in your GitHub repository:
> 1. `CLOUDFLARE_API_TOKEN`: A Cloudflare API token with "Cloudflare Pages: Edit" or "Workers: Edit" permissions.
> 2. `CLOUDFLARE_ACCOUNT_ID`: Your Cloudflare Account ID.

## Proposed Changes

### GitHub Workflow

#### [NEW] [deploy.yml](file:///c:/code/goodpassphrase-svelte/.github/workflows/deploy.yml)

Create a new workflow file with the following logic:
- **Trigger**: `pull_request` on `main` branch with `types: [closed]`.
- **Condition**: Only run if `github.event.pull_request.merged == true`.
- **Steps**:
  1. Checkout code.
  2. Setup Node.js.
  3. Install dependencies (`npm ci`).
  4. Build the project (`npm run build`).
  5. Deploy to Cloudflare using `cloudflare/wrangler-action`.

## Verification Plan

### Automated Steps
- Use a YAML linter (if available) or basic validation to ensure the workflow file is syntactically correct.
- Verify that the `npm run build` command works locally.

### Manual Verification
- Once the file is pushed, the user should:
  1. Add the secrets to GitHub.
  2. Create a test pull request to `main`.
  3. Merge the pull request.
  4. Check the "Actions" tab in GitHub to ensure the workflow runs and succeeds.
