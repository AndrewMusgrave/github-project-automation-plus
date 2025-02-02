# GitHub Project Automation+

> 🤖 Automate GitHub Project cards with any webhook event

This action allows you to use any of the `pull_request` and `issue` [webhook events](https://help.github.com/en/articles/events-that-trigger-workflows#webhook-events) to automate your project cards. For example when an `issue` is `opened` create a card in the Backlog project, Triage column.

If the `pull_request` or `issue` has a card it will be moved to the column provided. Otherwise the card will be created in the column.


## Usage

1. Create a new workflow by adding `.github/workflows/backlog-automation.yml` to your project. 
2. Create a [project](https://help.github.com/en/articles/about-project-boards) with Columns set up in your repository or organisation.
3. In the `backlog-automation.yml` you have to decide what events and actions are going move an issue or pull request to a column.


_For example:_

To move opened issues into the Triage column and assigned pull requests into the To Do column of the Backlog project. You would add the following to the `backlog-automation.yml` file:

```yml
name: Automate project columns

on: [issues, pull_request]

jobs:
  automate-project-columns:
    runs-on: ubuntu-latest
    steps:
      - name: Move new issues into Triage
        if: github.event_name == 'issues' && github.event.action == 'opened'
        uses: alex-page/github-project-automation-plus@master
        with:
          project: Backlog
          column: Triage
          repo-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Move assinged pull requests into To do
        if: github.event_name == 'pull_request' && github.event.action == 'assigned'
        uses: alex-page/github-project-automation-plus@master
        with:
          project: Backlog
          column: To do
          repo-token: ${{ secrets.GITHUB_TOKEN }}
```


## Workflow options

These are the options recommended to be changed. For more detailed explanation of the workflow file, check out the [GitHub documentation](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file).

| Setting | Description | Values |
| --- | --- | --- |
| `on` | When the automation is ran | `[issues, pull_request]` |
| `github.event.name` | The event type | `issues` or `pull_request` |
| `github.event.action` | The [webhook event](https://help.github.com/en/articles/events-that-trigger-workflows#webhook-events) that triggers the automation | `opened`, `assigned`, [...more](https://help.github.com/en/articles/events-that-trigger-workflows#webhook-events) |
| `project` | The name of the project | `Backlog` |
| `column` | The column to create or move the card to | `Triage` |
| `repo-token` | The personal access token | `${{ secrets.GITHUB_TOKEN }}` |


## Private repositories

In some cases you may want to do add this functionality for a private repository or one you do not have admin rights to. You may get an error like:
```shell
GraphqlError: Resource not accessible by integration
```

When this happens you will need to:
1. Create a [personal access token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)
2. [Create a secret](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) containing the personal access token, call it `GH_PAT`
3. Change the `repo-token` in the `backlog-automation.yml`  to reference your new token name:
```yaml
repo-token: ${{ secrets.GH_PAT }}
```

With certain organisations there may be SAML enforcement. This means you will need to `Enable SSO` when you create the personal access token.
```
GraphqlError: Resource protected by organization SAML enforcement. You must grant your personal token access to this organization
```


## Release History

- v0.0.3 - Automatic build before commit
- v0.0.2 - Error handling using GitHub actions
- v0.0.1 - Update icon and color for GitHub actions
- v0.0.0 - Initial release
