# Cherry-Pick Workflow Test Repo

This repository demonstrates an automated GitHub Actions workflow that cherry-picks hotfix PR merges from `main` into `develop`.

## What’s included
- Workflow: `.github/workflows/auto-cherry-pick-hotfix.yml`
- Branches: `main` and `develop`
- Sample change: `index.html` (added via `hotfix/add-index` branch)

## How the workflow works
- Triggers on `push` to `main`.
- Detects the latest merge commit on `main` where the commit message includes `hotfix/` (from the source branch name).
- Cherry-picks that merge commit into `develop` using `git cherry-pick -m 1`.
- Pushes the updated `develop` branch.

## Test the workflow
1. Ensure branches exist and are pushed:
   - `main` and `develop` are already pushed.
2. Open a Pull Request:
   - Go to the link GitHub printed after pushing: `https://github.com/motez-trino/cherry-picking-workflow/pull/new/hotfix/add-index`.
   - Create a PR from `hotfix/add-index` into `main`.
3. Merge the PR using a merge commit (the default option).
4. Watch the Actions run:
   - Navigate to the repository’s `Actions` tab and open the latest run.
   - It should detect the hotfix merge and cherry-pick into `develop`.
5. Verify results:
   - Check `develop` branch to confirm `index.html` exists.

## Notes
- Slack notification step is commented out for now.
- If a conflict occurs during cherry-pick, the workflow aborts and the run fails. Resolve the conflict manually on `develop`.

## Slack notifications
This workflow can notify Slack on both success and failure using either a Slack App (bot token) or an Incoming Webhook.

Option A — Slack App (recommended for flexibility):
- Create a Slack App: https://api.slack.com/apps → Create New App → From scratch.
- Add OAuth scopes to the bot: `chat:write` (required), `channels:read` (to look up channel IDs). If posting to private channels, also invite the app to the channel and consider `groups:read`.
- Install the app to your workspace and copy the Bot User OAuth Token (starts with `xoxb-`).
- Find the `internal-tech` channel ID:
  - Open Slack in the browser → go to the channel → the URL looks like `https://app.slack.com/client/Txxxxx/Cyyyyyy`. The `Cyyyyyy` part is the channel ID.
- In GitHub, add repository secrets:
  - `SLACK_BOT_TOKEN`: your bot token (`xoxb-...`).
  - `SLACK_CHANNEL_ID`: the channel ID (e.g., `C0123456789`).
- Ensure the app is a member of the channel (use `/invite @YourAppName` in `internal-tech` if needed).

Option B — Incoming Webhook (simple):
- In Slack, create an Incoming Webhook for the channel `internal-tech` and copy its URL.
- In GitHub, add a repository secret named `SLACK_WEBHOOK_URL` with that webhook URL:
  - Repo Settings → Secrets and variables → Actions → New repository secret → Name: `SLACK_WEBHOOK_URL`, Value: `<your webhook url>`.
- No need to specify the channel; the webhook is bound to the channel you selected.

What gets sent:
- Success: A message confirming the hotfix merge on `main` was cherry-picked to `develop`, with a link to the merge commit.
- Failure: A message indicating a cherry-pick conflict requiring manual intervention, with a link to the commit.

## Hotfix test note

This is a small documentation change to verify Slack notifications using the bot token. We will open a PR from `hotfix/test-notify` into `main` and merge it using a merge commit (ensure the PR title includes `hotfix/` so the merge commit message contains it). The workflow will then cherry-pick into `develop` and post to Slack.