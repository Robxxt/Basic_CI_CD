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

You specify the triggers using `on`

- `workflow_dispatch`: You trigger manually. You can see this example in `workflows/simple_hello_world.yaml`
