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
- `push`: Push to branch or Tag. You have an example in `.github/workflows/triggers_and_filters.yaml`
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

# ENV variables

## Loading from workload yaml

The variables can have one of this scopes `Workflow`, `Job`, `Step`. It all depends where you declare them. Just add them like this:

```yaml
env:
    EXAMPLE: THIS_IS_AN_EXAMPLE_ENV_VARIABLE
```

You can persist or pass variables from one step to another. There are two ways and depending on each one you will have to call the variable in other steps/jobs with different syntax.

## Using `$GITHUB_OUTPUT` (Step output, for job output)

Standard way to pass specific data. It's highly scope (inter-job), that means when you call it you must give the step an `id`. You must write to `$GITHUB_OUTPUT` and then reference it in next steps using `${{}}`

```yaml
jobs:
    producer:
        runs-on: ubuntu-24.04
        outputs: # To pass it to other jobs.
            random_number: ${{ steps.random_generator.outputs.random_val }}
        steps:
          - name: Generate a random number
            id: random_generator # You MUST have an ID
            run: echo "random_val=$RANDOM" >> $GITHUB_OUTPUT
        
          - name: Use that number
            run: echo "The number was ${{ steps.random_generator.outputs.random_val }}"

    consumer:
        runs-on: ubuntu-24.04
        needs: producer # specify the task from where it will get the ENV variable
        steps:
            - name: Inspect values from producer
              run: echo "${{ needs.producer.outputs.random_number }}"
```

## Using `$GITHUB_ENV` (Job scoped ENV variable)

You must write to `$GITHUB_ENV` and then reference it in the next steps using `$`

# Secrets and Variables

You can save secrets and variables for the repository in the `Settings` section.
There are two types of variables and secrets. `Environment` this is used to split same variable across different contexts.
You coudl have two environments like production and development, each having a different `API_KEY`. Or `Repository` secrets and variables.
To access the variables within the action you just need to specify `${{ secrets.VARIABLE }}` or `${{ vars.VARIABLE }}`.
In the case where the secrets or variables are not from repository but rather from an `Environment` you need to specify it by giving `environment: <name>`
For reference you can check `.github/workflows/secrets_and_vars.yaml`

# Contexts

`needs`: outputs & results from other jobs

`steps`: prior steps outputs & conclusion

`secrets`: secret values

`vars`: repo/org/environment config variables (good for cross-workflow settings)

`env`: variable set at workflow/job/step

`job`: job status, container/service information

`inputs`: parameters to reusable/workflow_dispatch workflows.

# Advanced

## Runner Types

Specified by `runs-on` field. The runner type specifies the VM image used (OS & dependencies) as well as the resources available (CPU & memory). There are three hosting options:

- GitHub
- 3rd Party: Sometimes it is faster or it bring other advanced features like faster caching, better observability or custom images.
- Self-hosted: Run it on your own hardware, for example using a K8 cluster.
- self-hosted

## Artifacts

Persist data beyond the lifecycle of a job. Using `actions/upload-artifact` to upload the file/blob and `actions/download-artifact` to download it for the next job.

This is very useful because it allows you to dedicate a job to compile the code. And then you can download the binary in the next job to test it.
