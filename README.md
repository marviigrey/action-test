# action-test

This is me building my github actions workflow, learning how github action works.

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
