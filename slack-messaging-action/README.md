# Toumoro Slack Messaging

Interactive messaging services for Slack.

## Limitation

This action need a compatible backend using API Key from the header `X-API-Key`.

## Inputs

### `API_URL`

**Required** API URL handling the messaging system.

### `API_KEY`

**Required** API KEY for authentication.

### `SERVICE`

**Required** Service using this action as `'github'`, `'gitlab'`, `'bitbucket'`.

### `PROJECT`

**Required** Name of the project.

### `CHANNEL`

**Required** Slack channel to post the message.

### `REF`

**Required** Repo reference as `GITHUB_REF`, `'refs/head/master'`, ...

### `RUN_ID`

**Required** Runner ID as `GITHUB_RUN_ID`.

### `STEP`

**Required** Step triggering the call.

### `TYPE`

**Required** Message type as (`'error'`, `'request'`, `'status'`).

### `STATUS`

**Required** Inline status following the regex `(?(DEFINE)(?<status>\w*:(?:FAIL|PASS)))^(?&status)(?:;(?&status))*$` as `Build:PASS;Test:FAIL`.

### `ISSUE_ID`

**Required** Issue ID for internal reference.

### `ACTOR`

**Required** Trigger Actor as `GITHUB_ACTOR`.

### `REPOSITORY`

**Required** Repository as `GITHUB_REPOSITORY`, `'user/project'`.

### `WORKFLOW`

**Optional** Workflow reference for manual triggering, for GitHub it represent the workflow file name.

### `TIMESTAMP`

**Optional** Slack timestamp for editing a previous message.

### `VERBOSE`

**Optional** Level of verbose, options: ['0', '1', '2'], default: '0'. **WARNING: Level 1 and 2 can print sensitive information.**

## Example usage

### GitHub Actions

```yml
jobs:
  tests:
  runs-on: ubuntu-latest
  steps:
    - uses: docker://toumoro/slack-messaging-action:latest
      env:
        API_URL: ${{ env.API_URL }}
        API_KEY: ${{ secrets.API_KEY }}
        SERVICE: github
        CHANNEL: my-channel
        REF: ${{ github.ref }}
        RUN_ID: ${{ github.run_id }}
        STEP: Tests
        TYPE: deploy
        STATUS: Build:PASS;Test:PASS
        ISSUE_ID: '1234'
        ACTOR: ${{ github.actor }}
        REPOSITORY: ${{ github.repository }}
        WORKFLOW: 'my-next-workflow.yml'
        VERBOSE: '0'
```

### GitLab CI

```yml
stage:
  ...
  - send_message
image: toumoro/slack-messaging-action:latest
  script: [ 'true' ]
  variables:
    API_URL: $API_URL
    API_KEY: $API_KEY
    SERVICE: github
    CHANNEL: my-channel
    REF: github.ref
    RUN_ID: github.run_id
    STEP: Tests
    TYPE: deploy
    STATUS: Build:PASS;Test:PASS
    ISSUE_ID: '1234'
    ACTOR: github.actor
    REPOSITORY: github.repository
    WORKFLOW: 'my-next-workflow.yml'
    VERBOSE: '0'
```

### Bitbucket Pipeline

### Bitbucket `bitbucket-pipelines.yml` Example

```yaml
pipelines:
  default:
    ...
    - step:
      name: Send Message
      script:
        - *auto_devops
        - set_environment_variable
        - pipe: toumoro/slack-messaging-action:latest
          variables:
            API_URL: $API_URL
            API_KEY: $API_KEY
            SERVICE: bitbucket
            CHANNEL: my-channel
            REF: BITBUCKET_BRANCH
            RUN_ID: github.run_id
            STEP: Tests
            TYPE: deploy
            STATUS: Build:PASS;Test:PASS
            ISSUE_ID: '1234'
            ACTOR: github.actor
            REPOSITORY: github.repository
            WORKFLOW: 'my-next-workflow.yml'
            VERBOSE: '0'
```
