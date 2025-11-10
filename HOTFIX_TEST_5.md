# Hotfix test-notify-5

Minimal change to trigger the hotfix workflow. After the PR from `hotfix/test-notify-5` is merged into `main` with a merge commit (title includes `hotfix/`), the workflow should cherry-pick into `develop` and post to Slack.