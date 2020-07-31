# Super-Linter Action

See [GitHub Super-Linter](https://github.com/github/super-linter/blob/master/README.md) for more detail.

## Usage

### GitHub Actions

```yaml
name: Lint Code Base Check

on:
  push:
    branches:
      - master

jobs:
  build:
    name: Lint Code Base
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Lint Code Base
        uses: docker://github/super-linter:latest
        env:
          VALIDATE_ALL_CODEBASE: "true"
          DEFAULT_BRANCH: master
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### GitLab CI

```yaml
superlinter:
  stage: linting
  image: github/super-linter:latest
  script: [ "true" ]
  variables:
    VALIDATE_ALL_CODEBASE: "true"
    DEFAULT_BRANCH: master
    RUN_LOCAL: "true"
    DEFAULT_WORKSPACE: $CI_BUILDS_DIR
    ANSIBLE_DIRECTORY: $CI_PROJECT_PATH
    LINTER_RULES_PATH: $CI_PROJECT_PATH/.github/linters
```

### Bitbucket Pipeline

```yaml
pipelines:
  default:
    ...
    - step:
        name: Super-Linter
        script:
          - *auto_devops
          - set_environment_variable
          - pipe: docker://github/super-linter:latest
            variables:
              VALIDATE_ALL_CODEBASE: true
              DEFAULT_BRANCH: master
              RUN_LOCAL: "true"
              DEFAULT_WORKSPACE: $BITBUCKET_CLONE_DIR
              ANSIBLE_DIRECTORY: $BITBUCKET_CLONE_DIR
              LINTER_RULES_PATH: $BITBUCKET_CLONE_DIR/.github/linters
```
