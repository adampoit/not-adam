# not-adam

Composite GitHub Action for workflows that run as the `not-adam` GitHub App.

It creates a short-lived GitHub App installation token with [`actions/create-github-app-token`](https://github.com/actions/create-github-app-token), configures the git commit identity, and exposes the token as an output.

## Usage

Add these to each repository that uses the action:

- Repository variable: `NOT_ADAM_APP_ID`
- Repository secret: `NOT_ADAM_APP_PRIVATE_KEY`

```yaml
jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - id: not-adam
        uses: adampoit/not-adam@v1
        with:
          app-id: ${{ vars.NOT_ADAM_APP_ID }}
          private-key: ${{ secrets.NOT_ADAM_APP_PRIVATE_KEY }}

      - uses: actions/checkout@v7
        with:
          token: ${{ steps.not-adam.outputs.token }}

      - name: Do authenticated work
        env:
          GH_TOKEN: ${{ steps.not-adam.outputs.token }}
        run: gh auth status
```

The token is scoped to the calling repository by default. Install the GitHub App on any repository that should use this action.

## Inputs

| Input              | Description                                                        | Default                                 |
| ------------------ | ------------------------------------------------------------------ | --------------------------------------- |
| `app-id`           | GitHub App ID.                                                     | Required                                |
| `private-key`      | GitHub App private key PEM.                                        | Required                                |
| `owner`            | Installation owner.                                                | Calling repository owner                |
| `repositories`     | Comma or newline-separated repository names to scope the token to. | Calling repository name                 |
| `configure-git`    | Configure `user.name` and `user.email`.                            | `true`                                  |
| `git-config-scope` | Git config scope: `global` or `local`.                             | `global`                                |
| `git-user-name`    | Git user.name override.                                            | `<app-slug>[bot]`                       |
| `git-user-email`   | Git user.email override.                                           | App bot noreply address when resolvable |

## Outputs

| Output            | Description                                |
| ----------------- | ------------------------------------------ |
| `token`           | Short-lived GitHub App installation token. |
| `installation-id` | GitHub App installation ID.                |
| `app-slug`        | GitHub App slug.                           |
