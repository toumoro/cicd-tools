# CICD Tools

Toolbox for _GitHub Actions_, _GitLab CI_ and _Bitbucket Pipeline_ workflow.

## List of tools

Logos <img src="./gl_logo.svg" width="15"> <img src="./gh_logo.svg" width="15"> <img src="./bb_logo.svg" width="15"> indicate compatibility.

- [Cloudflare Purge Action](cloudflare-purge-action/README.md) <img src="./gl_logo.svg" width="15"> <img src="./gh_logo.svg" width="15"> <img src="./bb_logo.svg" width="15"> <sub><sup>(From [jakejarvis/cloudflare-purge-action](https://github.com/jakejarvis/cloudflare-purge-action))</sub></sup>

- [Slack Messaging Action](slack-messaging-action/README.md) <img src="./gh_logo.svg" width="15">

- [SSH Commands Action](ssh-commands-action/README.md) <img src="./gl_logo.svg" width="15"> <img src="./gh_logo.svg" width="15"> <img src="./bb_logo.svg" width="15"> <sub><sup>(From [appleboy/ssh-action](https://github.com/appleboy/ssh-action))</sub></sup>

- [Super-Linter Action](super-linter-action/README.md) <img src="./gl_logo.svg" width="15"> <img src="./gh_logo.svg" width="15"> <img src="./bb_logo.svg" width="15"> <sub><sup>(From [github/super-linter](https://github.com/github/super-linter))</sub></sup>

## Convert GitHub Action

**IMPORTANT:** Only **Docker** base action can be convert and **GitHub Action** `outputs` are not convertible.

- Found action on GitHub Marketplace;
- Go to the repository and search for `action.yml`;
- `runs` key must be using `'docker'`:

  ```yaml
  runs:
    using: "docker"
    image: "Dockerfile"
  ```

- Looks for `inputs` key, **GitHub** convert each input in **uppercase** and add `INPUT_` at the beginning:

  ```yaml
  inputs:
    my_input:
      description: "My Input variable"
  ```

  ```diff
  - my_input
  + INPUT_MY_INPUT
  ```

- If the action use `env` instead of `with`, you don't have to convert the input name:

  ```diff
   - name: My Action
     uses: toumoro/my-action@v1
  -  with:
  -    my_input: 'Some Value'
  +  env:
  +    MY_INPUT: 'Some Value'
  ```

- Go to the DockerHub repository of the action to choose your tag.
- To use docker instead of action in GitHub replace the `uses` with the docker image and don't use `with` key:

  ```diff
   - name: My Action
  -  uses: toumoro/my-action@v1
  +  uses: docker://toumoro/my-action:latest
  env:
    MY_INPUT: 'Some Value'
  ```

### To GitLab CI

#### If GitHub Action use `env`

```yaml
my_job:
  stage: my_stage
  image: toumoro/my-action:latest
  script: ["true"]
  variables:
    MY_INPUT: "Some Value"
```

#### If GitHub Action use `with`

```yaml
my_job:
  stage: my_stage
  image: toumoro/my-action:latest
  script: ["true"]
  variables:
    INPUT_MY_INPUT: "Some Value"
```

### To Bitbucket CI

#### If GitHub Action use `env`

```yaml
pipelines:
  default:
    - step:
        name: My Action
        script:
          - pipe: docker://toumoro/my-action:latest
            variables:
              MY_INPUT: "Some Value"
```

#### If GitHub Action use `with`

```yaml
pipelines:
  default:
    - step:
        name: My Action
        script:
          - pipe: docker://toumoro/my-action:latest
            variables:
              INPUT_MY_INPUT: "Some Value"
```
