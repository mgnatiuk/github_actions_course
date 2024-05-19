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
```powershell
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
    types: [custom-event]

jobs:
  dispatch:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v2

      - name: Print Event Payload
        run: echo "Event Payload: ${{ github.event.client_payload }}"
```

