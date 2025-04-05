# action-test

This is me writing all you need to know about github actions workflow, learning how github action works.

Timeouts in github actions:
Github automatically kills job at 6hrs, you can add a timeout to you jobs at the step and job level level:
``` timeout-minutes: 1 ```

Matrix:
In a situation where we have different versions of an application and we want to deploy them on different operating system,we can do so by having multiple jobs running.  This step makes code bulky, we can initiate a matrix strategy. A matrix strategy lets you use variables in a single job definition to automatically create multiple job runs that are based on the combinations of the variables. For example, you can use a matrix strategy to test your code in multiple versions of a language or on simple operating systems. To use the matrix, we must first define them in our pipeline like this:

```
jobs:
    deploy:
        strategy: 
          matrix: 
            os: [ubuntu-latest, ubuntu-20.04, windows-latest]
            images: [hello-world, alpine]
        runs-on: ${{ matrix.os }}
        steps:
            - name: Echo Docker details
              run: docker info

            - name: Run Images
              run: docker run ${{ matrix.images }}


```

we will have 3 operating systems running 2 jobs each, in parallel. basically ubuntu-latest running the hello-world and alpine image at a go, same for windows-latest and ubuntu-20.04.

Note: if a job fails the whole workflow will be considered a failure.

We can make use of the exclude and include keywords to add or remove a particular matrix.

```
exclude: #exclude a matrix from running
               - images: alpine
                 os: windows-latest
            include: #include a new matrix
               - images: amd64/alpine
                 os: ubuntu-20.04
```

Context: 

Context is a set of predefined objects or variables that contain information about a workflow run. Information such as environments,events,variables,secrets,jobs,steps and other variety of information. It's a way to access information about a workflow.

```
name: Context testing
on: push

jobs:
  dump_contexts_to_log:
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        env:
          GITHUB_CONTEXT: ${{ toJson(github) }}
        run: echo "$GITHUB_CONTEXT"
```
The following context gathers information about the github. 

 To make reference to context within our workflow configuration file we make use of expressions.

We can use expression to programmatically set environment variables in workflow files and access contexts. 

Workflow event filters:
We can hae workflow triggered by an event activity type

skipping workflow runs:
You can skip workflow runs triggered by the push and pull_request events by including a command in your commit message. Examples of skip strings:

- skip ci
- ci skip
- no ci
- skip actions
- actions skip

Debug workflows:
If the workflow logs do not provide details to diagnose why a workflow,job, or step is not working as expected,you can enable additional debug logging. These extra logs are enabled by setting secrets or variables in the repository containing the workflow, so the same permission requirements will be applied.
you can enable: 

- runner logging: runner diagnostic logging provides additional log files that contain information about how a runner is executing a job. two extra log files are added to the log srchive:

- step logging: step debug logging increase the verbosity of a job's logs during and after a job's execution.

We can enable dubug logging either by re-running the job on the UI or by creating variables or secret in the UI repository that contains the workflow:
For runner diagnostic logging:
 $ACTIONS_RUNNER_DEBUG: true

For step Debug logging:
$ACTIONS_STEP_DEBUG: true

You can create either secrets or vsriables for this but any rule set on secret will take precedence over the one set on variable.

Github provides a lot of REST API for you to integrate with your workflow, We can use the REST API to view logs and workflow jobs in github actions.

You can get the logs of a job by using the github rest api follow the doc: https://docs.github.com/en/rest/actions/workflow-jobs?apiVersion=2022-11-28#get-a-job-for-a-workflow-run

Workflow_dispatch inputs:
We can use workflow_dispatch to manually trigger our workflows. When using workflow_dispatch event, you can optionally specify inputs that are passed to the workflow. This trigger only recieves events when the workflow file is on the default branch. The triggered workflow recieves the inputs in the inputs context:

key factors to note:
  - The workflow will also receive the inputs in the github.events.inputs context. The information in the inputs context preserves boolean values as boolean instead of converting them to strings. The choice type resolves to a string and is a single selectable option.
  - The maximum number of top level-properties for input is 10
  - The maximum payload for inputs is 65535 characters.

  Trigger workflows for manual events:
  - workflow_dispatch
  - repository_dispatch
  - workflow_call - build based on the success of another workflow.
  - workflow_run