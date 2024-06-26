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

```yaml
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_PERSONAL_ACCESS_TOKEN" \
  https://api.github.com/repos/mgnatiuk/github_actions_course/dispatches \
  -d '{"event_type":"webhook","client_payload":{"name":"Mykola"}}'
```

## Expression functions

```yaml
steps:
  - name: Check if string contains substring
    if: contains('Hello world', '11o')
    run: echo "The string contains the substring."

  - name: Check if string starts with
    if: startswith("Hello world", "He")
    run: echo "The string starts with 'He'."

  - name: Check if string ends with
    if: endswith('Hello world', 'ld')
    run: echo "The string ends with 'ld'."

  - name: Format and echo string
    run: echo "${{ format('Hello {0} {1} {2}', 'Mona', 'the', 'Octocat') }}"

  - name: Convert job context to JSON
    run: echo "Job context in JSON: ${{ toJSON(github.job) }}"

  - name: Parse JSON string
    run: echo "Parsed JSON: ${{ fromJSON('{"hello": "world"}').hello }}"

  - name: Hash files
    run: echo "Hash of files: ${{ hashFiles('***/package-lock.json', '***/Gemfile.lock') }}"

  - name: The job has succeeded
    if: success()
    run: echo "SUCCESS!"

  - name: The job has failed
    if: failure()
    run: echo "Failure!"
```

## Workflow commands

```yaml
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

# Stopping and Failing Action
 - name: Fail workflow
      run: |
        echo "::error::This is a error message."
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

## Workflow Context

  - `github`: Information about the workflow run.
  - `env`: Contains variables set in a workflow, job, or step.
  - `vars`: Contains variables set at the repository, organization, or environment levels.
  - `job`: Information about the currently running job.
  - `jobs`: For reusable workflows only, contains outputs of jobs from the reusable workflow.
  - `steps`: Information about the steps that have been run in the current job.
  - `Runner`: Information about the runner that is running the current job.
  - `secrets`: Contains the names and values of secrets that are available to a workflow run.
  - `strategy`: Information about the matrix execution strategy for the current job.
  - `matrix`: Contains the matrix properties defined in the workflow that apply to the current job.
  - `inputs`: Contains the inputs of a reusable or manually triggered workflow.
  - `needs`: Contains the outputs of all jobs that are defined as a dependensy of the current job.

## Dependent Jobs

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: Step 1
        run: echo "This is step 1"
  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - name: Step 1
        run: echo "This is step 1 of job 2, depending on job 1"
```

## How to set secrets

### Repository level
```bash
# set a secret and prompt for secret value
gh secret set SECRET_NAME

# sets a secrets and sets values from a text file
gh secret set SECRET_NAME < secret.txt
```
### Environment level
```bash
# set a secret and prompt for secret value
gh secret set --env ENV_NAME SECRET_NAME

# get list of secrets for an env
gh secret list --env ENV_NAME
```
### Organization level
```bash
# login with admin:org scope to managed org secrets
gh auth login --scopes "admin:org"

# set a secret only for private repos and prompt for secret alue
gh secret set --org ORG_NAME SECRET_NAME

# set a secret for public, private and internal repos
gh secret set --org ORG_NAME SECRET_NAME -- visibility all

# set a secret for specific repos
gh secret set --org ORG_NAME SECRET_NAME --repos REPO-NAME-1, REPO-NAME-2

# list secrets for the org
gh secret list --org ORG_NAME
```

## Variables Configuration
```bash
# Add variable value for the current repository in an interactive prompt
gh variable set MYVARIABLE

# Read variable value from an environment variable
gh variable set MYVARIABLE --body "$ENV_VALUE"

# Read variable value from a file
gh variable set MYVARIABLE < myfile.txt

# Set variable for a deployment environment in the current repository
gh variable set MYVARIABLE --env myenvironment

# Set organization-level variable visible to both public and private repositories
gh variable set MYVARIABLE --org myOrg -visibility all

# Set organization-level variable visible to specific repositories
gh variable set MYVARIABLE --org myorg --repos repol, repo2, rep3

# Set multiple variables imported from the ".env" file
gh variable set -f .env
```

## Set Environment Variables With Workflow Commands
```yaml
steps:
    - name: Hello
      run echo "DYNAMIC_VAR=Hello world from action" >> $GITHUB_ENV

    - name: World
      run echo "$DYNAMIC_VAR - value is"
```
