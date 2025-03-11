# Content

1. [Gitlab-server](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#gitlab-server "Gitlab-server")

   1.1. [Introduction](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#introduction "Introduction")

   1.2. [Install GitLab](https://github.com/NikitaPrimakov/GitLab?tab=readme-ov-file#install-gitlab "Install Gitlab")

2. [Conlusion]("Conclusion")

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


## Install Gitlab-runner

