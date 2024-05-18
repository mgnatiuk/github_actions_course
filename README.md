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

## Diagram

The following diagram illustrates the relationship between the different components of a GitHub Actions Workflow:

![GitHub Actions Workflow Diagram](/images/components.png)

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

