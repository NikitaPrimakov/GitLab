# Content

1. [Gitlab-server](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#gitlab-server "Gitlab-server")

   1.1. [Introduction](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#introduction "Introduction")

   1.2. [Install GitLab](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#install-gitlab "Install Gitlab")

2. [Gitlab-runner](https://github.com/NikitaPrimakov/GitLab#gitlab-runner "Gitlab-runner")

   2.1 [Intruduction](https://github.com/NikitaPrimakov/GitLab#intruduction "intruduction")

   2.2 [Install Gitlab-runner](https://github.com/NikitaPrimakov/GitLab#install-gitlab-runner "Install Gitlab-runner")

3. [Conclusion](https://github.com/NikitaPrimakov/GitLab#conclusion "Conclusion")

# Gitlab-server

## Introduction

GitLab is an Open Source code repository and collaborative software development platform for large DevOps and DevSecOps projects. GitLab is free for individuals. GitLab offers a location for online code storage and capabilities for issue tracking and CI/CD.

## Install Gitlab

Let's install and configure the Gitlab server

Below is a complete list of commands that will help you install and configure our GitLab server.

1. ___```Updating the system:```___

   ```
   sudo apt update
   sudo apt upgrade -y
   ```

2. ___```installing the necessary dependencies:```___

   ```
   sudo apt install -y curl openssh-server ca-certificates tzdata perl
   ```

3. ___```Adding a GitLab repository:```___

   ```
   curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
   ```

4. ___```Installing GitLab CE:```___

   ```
   sudo apt install gitlab-ce
   ```

5. ___```Installing SSL certificates```___

   This point can be viewed [here](https://github.com/NikitaPrimakov/Certificate?tab=readme-ov-file#certificate-issue "Gitlab certificate-issue").

# Gitlab-runner

## Intruduction

GitLab Runner is an open source application that performs GitLab CI/CD pipelining tasks according to instructions from a special file. gitlab-ci. yml . It allows you to run automated builds inside a Managed Service for Kubernetes cluster.

## Install Gitlab-runner

1. ___```First, let's add the official GitLab repository:```___

   ```
   curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
   ```

2. ___```Install gitlab-runner:```___

   ```
   sudo apt-get install gitlab-runner
   ```

3. ___```After installation, we will register the runner. To do this, you will need the URL of your GitLab server and the registration token. The token can be found in the CI/CD settings of your project or group:```___

   ```
   sudo gitlab-runner register
   ```

___When registering, you will need to specify:___
- URL of the GitLab server
- Registration token
- Runner Description
- tags (optional)
- Executor (e.g. shell, docker, kubernetes, etc.)

In our case, the configuration looks like this:

```
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "my-runner"
  url = "https://git01.local"
  token = "YOUR_TOKEN"
  executor = "docker"
  [runners.docker]
    tls_verify = false
    image = "docker:dind"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/var/run/docker.sock:/var/run/docker.sock:rw", "/cache"]
    extra_hosts = ["git01.local:192.168.*.*"]
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
```

# Conclusion

We installed the GitLab server and integrated Gitlab runner.
