# SSH Commands Action

**Important**: Only support **Linux** [docker](https://www.docker.com/) container.

## Usage

All sensitive variables should be [set as encrypted secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) in the action's configuration.

### Quick Example

#### GitHub Actions

```yaml
name: remote ssh command
on: [push]
jobs:

  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
    - name: executing remote ssh commands using password
      uses: docker://toumoro/ssh-commands-action:latest
      env:
        INPUT_HOST: ${{ secrets.HOST }}
        INPUT_USERNAME: ${{ secrets.USERNAME }}
        INPUT_SCRIPT_PASSWORD: ${{ secrets.PASSWORD }}
        INPUT_PORT: ${{ secrets.PORT }}
        INPUT_SCRIPT: whoami
```

#### GitLab CI

```yaml
stage:
  ...
  - ssh_command

...

ssh_command:
  image: toumoro/ssh-commands-action:latest
  script: [ 'true' ]
  variables:
    INPUT_HOST: $HOST
    INPUT_USERNAME: $USERNAME
    INPUT_PASSWORD: $PASSWORD
    INPUT_PORT: $PORT
    INPUT_SCRIPT: whoami
```

#### Bitbucket Pipeline

```yaml
pipelines:
  default:
    ...
    - step:
      name: SSH Commands
      script:
        - *auto_devops
        - set_environment_variable
        - pipe: docker://toumoro/ssh-commands-action:latest
          variables:
            INPUT_HOST: $HOST
            INPUT_USERNAME: $USERNAME
            INPUT_PASSWORD: $PASSWORD
            INPUT_PORT: $PORT
            INPUT_SCRIPT: whoami
```

### Complex Example

Executing remote ssh commands.

```yaml
name: remote ssh command
on: [push]
jobs:

  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
    - name: executing remote ssh commands using password
      uses: docker://toumoro/ssh-commands-action:latest
      env:
        INPUT_HOST: ${{ secrets.HOST }}
        INPUT_USERNAME: ${{ secrets.USERNAME }}
        INPUT_SCRIPT_PASSWORD: ${{ secrets.PASSWORD }}
        INPUT_PORT: ${{ secrets.PORT }}
        INPUT_SCRIPT: whoami
```

output:

```sh
======CMD======
whoami
======END======
out: ***
==============================================
✅ Successfully executed commands to all host.
==============================================
```

## Input variables

* INPUT_HOST - ssh host
* INPUT_PORT - ssh port, default is `22`
* INPUT_USERNAME - ssh username
* INPUT_PASSWORD - ssh password
* INPUT_PASSPHRASE - the passphrase is usually to encrypt the private key
* INPUT_SYNC - synchronous execution if multiple hosts, default is false
* INPUT_TIMEOUT - timeout for ssh to remote host, default is `30s`
* INPUT_COMMAND_TIMEOUT - timeout for ssh command, default is `10m`
* INPUT_KEY - content of ssh private key. ex raw content of ~/.ssh/id_rsa
* INPUT_KEY_PATH - path of ssh private key
* INPUT_FINGERPRINT - fingerprint SHA256 of the host public key, default is to skip verification
* INPUT_SCRIPT - execute commands
* INPUT_SCRIPT_STOP - stop script after first failure
* INPUT_ENVS - pass environment variable to shell script
* INPUT_DEBUG - enable debug mode
* INPUT_USE_INSECURE_CIPHER - include more ciphers with use_insecure_cipher
* INPUT_CIPHER - the allowed cipher algorithms. If unspecified then a sensible

SSH Proxy Setting:

* INPUT_PROXY_HOST - proxy host
* INPUT_PROXY_PORT - proxy port, default is `22`
* INPUT_PROXY_USERNAME - proxy username
* INPUT_PROXY_PASSWORD - proxy password
* INPUT_PROXY_PASSPHRASE - the passphrase is usually to encrypt the private key
* INPUT_PROXY_TIMEOUT - timeout for ssh to proxy host, default is `30s`
* INPUT_PROXY_KEY - content of ssh proxy private key.
* INPUT_PROXY_KEY_PATH - path of ssh proxy private key
* INPUT_PROXY_FINGERPRINT - fingerprint SHA256 of the proxy host public key, default is to skip verification
* INPUT_PROXY_USER_INSECURE_CIPHER - include more ciphers with INPUT_USE_INSECURE_CIPHER
* INPUT_PROXY_CYPHER - the allowed cipher algorithms. If unspecified then a sensible

### Example

Executing remote ssh commands using password.

```yaml
- name: executing remote ssh commands using password
  uses: docker://toumoro/ssh-commands-action:latest
  env:
    INPUT_HOST: ${{ secrets.HOST }}
    INPUT_USERNAME: ${{ secrets.USERNAME }}
    INPUT_SCRIPT_PASSWORD: ${{ secrets.PASSWORD }}
    INPUT_PORT: ${{ secrets.PORT }}
    INPUT_SCRIPT: whoami
```

Using private key

```yaml
- name: executing remote ssh commands using ssh key
  uses: docker://toumoro/ssh-commands-action:latest
  env:
    INPUT_HOST: ${{ secrets.HOST }}
    INPUT_USERNAME: ${{ secrets.USERNAME }}
    INPUT_KEY: ${{ secrets.KEY }}
    INPUT_PORT: ${{ secrets.PORT }}
    INPUT_SCRIPT: whoami
```

Multiple Commands

```yaml
- name: multiple command
  uses: docker://toumoro/ssh-commands-action:latest
  env:
    INPUT_HOST: ${{ secrets.HOST }}
    INPUT_USERNAME: ${{ secrets.USERNAME }}
    INPUT_KEY: ${{ secrets.KEY }}
    INPUT_PORT: ${{ secrets.PORT }}
    INPUT_SCRIPT: |
      whoami
      ls -al
```

Multiple Hosts

```diff
  - name: multiple host
    uses: docker://toumoro/ssh-commands-action:latest
    env:
-     INPUT_HOST: "foo.com"
+     INPUT_HOST: "foo.com,bar.com"
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
      INPUT_SCRIPT: |
        whoami
        ls -al
```

Synchronous execution on multiple hosts

```diff
  - name: multiple host
    uses: docker://toumoro/ssh-commands-action:latest
    env:
      INPUT_HOST: "foo.com,bar.com"
+     INPUT_SYNC: true
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
      INPUT_SCRIPT: |
        whoami
        ls -al
```

Pass environment variable to shell script

```diff
  - name: pass environment
    uses: docker://toumoro/ssh-commands-action:latest
    env:
+     FOO: "BAR"
+     BAR: "FOO"
+     SHA: ${{ github.sha }}
      INPUT_HOST: ${{ secrets.HOST }}
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
+     INPUT_ENVS: FOO,BAR,SHA
      INPUT_SCRIPT: |
        echo "I am $FOO"
        echo "I am $BAR"
        echo "sha: $SHA"
```

_Inside `env` object, you need to pass every environment variable as a string, passing `Integer` data type or any other may output unexpected results._

Stop script after first failure. ex: missing `abc` folder

```diff
  - name: stop script if command error
    uses: docker://toumoro/ssh-commands-action:latest
    env:
      INPUT_HOST: ${{ secrets.HOST }}
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
+     INPUT_SCRIPT_STOP: true
      INPUT_SCRIPT: |
        mkdir abc/def
        ls -al
```

output:

```sh
======CMD======
mkdir abc/def
ls -al

======END======
2019/11/21 01:16:21 Process exited with status 1
err: mkdir: cannot create directory ‘abc/def’: No such file or directory
##[error]Docker run failed with exit code 1
```

How to connect remote server using `ProxyCommand`?

```bash
+--------+       +----------+      +-----------+
| Laptop | <-->  | Jumphost | <--> | FooServer |
+--------+       +----------+      +-----------+
```

in your `~/.ssh/config`, you will see the following.

```bash
Host Jumphost
  HostName Jumphost
  User ubuntu
  Port 22
  IdentityFile ~/.ssh/keys/jump_host.pem

Host FooServer
  HostName FooServer
  User ubuntu
  Port 22
  ProxyCommand ssh -q -W %h:%p Jumphost
```

How to convert to YAML format of GitHubActions.

```diff
  - name: ssh proxy command
    uses: docker://toumoro/ssh-commands-action:latest
    env:
      INPUT_HOST: ${{ secrets.HOST }}
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
+     INPUT_PROXY_HOST: ${{ secrets.PROXY_HOST }}
+     INPUT_PROXY_USERNAME: ${{ secrets.PROXY_USERNAME }}
+     INPUT_PROXY_KEY: ${{ secrets.PROXY_KEY }}
+     INPUT_PROXY_PORT: ${{ secrets.PROXY_PORT }}
      INPUT_SCRIPT: |
        mkdir abc/def
        ls -al
```

Protecting a Private Key. The purpose of the passphrase is usually to encrypt the private key. This makes the key file by itself useless to an attacker. It is not uncommon for files to leak from backups or decommissioned hardware, and hackers commonly exfiltrate files from compromised systems.

```diff
  - name: ssh key passphrase
    uses: docker://toumoro/ssh-commands-action:latest
    env:
      INPUT_HOST: ${{ secrets.HOST }}
      INPUT_USERNAME: ${{ secrets.USERNAME }}
      INPUT_KEY: ${{ secrets.KEY }}
      INPUT_PORT: ${{ secrets.PORT }}
+     INPUT_PASSPHRASE: ${{ secrets.PASSPHRASE }}
      INPUT_SCRIPT: |
        whoami
        ls -al
```
