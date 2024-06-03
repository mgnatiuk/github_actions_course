# GitHub Actions Certification

![Badge](/images/header.png)

## Components

- **Actions**: Reusable tasks that perform specific jobs within a workflow.

- **Workflows**: Automated processes defined in your repository that coordinate one or more jobs, triggered by events or on a schedule.

- **Jobs**: Groups of steps that execute on the same runner, typically running in parallel unless configured otherwise.

- **Steps**: Individual tasks within a job that run commands or actions sequentially.

- **Runs**: Instances of workflow execution triggered by events, representing the complete run-through of a workflow.

- **Runners**: Servers that host the environment where the jobs are executed, available as GitHub-hosted or self-hosted options.

- **Marketplace**: A platform to find and share reusable actions, enhancing workflow capabilities with community-developed tools.

- **Runner**: The environment where jobs are executed.
- **Jobs**: A set of steps executed by the runner.
- **Steps**: Sequential tasks within a job.
- **Actions**: Reusable tasks that are part of the steps in a job.

## Manual events

- We can trigger workflows manually via Github CLI or Github REST API
```sh
gh workflow run main.yaml \
-f name = Miko \
-F data=@myfile.txt \
```

- If you want run workflow manualy, you should add configuration **workflow_dispatch**
```yaml
name: My Workflow

on:
  workflow_dispatch:
    inputs:
      name:
        description: 'Name parameter'
        required: true
        default: 'Default Name'
      data:
        description: 'Data file'
        required: true

jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
      - name: Print Name and Data
        run: |
          echo "Hello, ${{ github.event.inputs.name }}"
          echo "Data file content:"
          cat ${{ github.event.inputs.data }}
```

## Webhooks

- Using `repository_dispatch` with webhook type you can trigger Workflow via an external HTTP endpoint. Will only trigger a workflow run if the workflow file is on the default branch.

```yaml
name: Repository Dispatch

on:
  repository_dispatch:
    types:
      - webhook

jobs:
  dispatch:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v2

      - name: Print Event Payload
        run: echo "Hello ${{ github.event.client_payload.name }}"
```

When you want make request to webhook:
- Send POST request
- Accept type = aplication/vnd.github+json
- Add PAT token
- Event type = webhook

```sh
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_PERSONAL_ACCESS_TOKEN" \
  https://api.github.com/repos/mgnatiuk/github_actions_course/dispatches \
  -d '{"event_type":"webhook","client_payload":{"name":"Mykola"}}'
```

## Workflow commands

```sh
# Grouping log messages
- name: Group example
      run: |
        echo "::group::Starting build"
        echo "Building the project..."
        # Your build commands here
        echo "Build step 1 complete"
        echo "Build step 2 complete"
        echo "::endgroup::"

# Masking values
   - name: Use masked value
      run: |
        echo "Using masked value: ${{ secrets.MY_SECRET }}"
        echo "Masking the value manually..."
        echo "::add-mask::$MY_SECRET"
        echo "This line will not show the actual secret: $MY_SECRET"

# Debug
 - name: Print debug message
      run: |
        echo "::debug::This is a debug message."
```

### Enabling Debug Logging

To see the debug message in your logs, make sure you enable debug logging by adding a repository secret named `ACTIONS_STEP_DEBUG` with the value `true`:

1. Go to your repository on GitHub.
2. Click on **Settings**.
3. In the left sidebar, click on **Secrets and variables** > **Actions**.
4. Click on the **New repository secret** button.
5. Name the secret `ACTIONS_STEP_DEBUG` and set its value to `true`.
6. Click **Add secret**.

This setup will enable debug logging for your workflows, allowing you to see debug messages in the logs.



