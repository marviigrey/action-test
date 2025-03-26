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

we will have 3 operating systems running 2 jobs each, in parallel. basically ubuntu-latest running the hello-world and alpine image at a go, same for windows-latest and ubuntu-20.04