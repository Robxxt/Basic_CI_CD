# Basic_CI_CD using Github Actions
This repository is a laboratory to implement CI/CD using github actions

# Common Workflow Types

## Validation

- Linters
- Testers
- Static analysis (both quality and security)

## Build

- Executables (compiled languages)
- Container Images

## Deploy

- Deploy to the server (push based or git-ops)

## Repository Automations

- Release automations
- Automatically manage Issues & PR
- Execute dependency upgrades

# Workflow Triggers

You specify the triggers using `on`. Almost all GitHub events can trigger a workflow.
Some that I have been using are:

- `workflow_dispatch`: You trigger manually. You can see this example in `.github/workflows/simple_hello_world.yaml`
- `push`: Push to branch or Tag.
```yaml
on:
    push:
        branches:
            - "feature/*"
```
- `pull_request`: Create/Update PR
```yaml
on:
    pull_request:
        types:
            - opened
            - synchronize
            - reopened
```
- `schedule`: Cron Schedule. You can specify when do you want it to run.
```yaml
on:
    schedule:
        - cron: "0 0 * * *" # Run at midnight
```

# Workflow runners

- `run`: Runs an inline command. You can run using different shells `sh, bash, powershell, cmd, node, python`. For running workflow that should work for both `ubuntu` and `windows` you should use `pwsh`.
- `uses`: Runs 3rd party actions. When you use 3rd party actions is good practice to specify the commit to use in order to manage the security and stability of the action. Writing the commit instead of @version is safer because it prevents the provider to try to inject malicious code and just change the version tag.

# Job order priority

You can specify that a job must wait until other jobs have finished by using `needs by using `needs`. You can find an example in `.github/workflows/multi_step.yaml`
