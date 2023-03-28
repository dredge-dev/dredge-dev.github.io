---
title: "Quick-Start Guide"
permalink: /docs/quick-start-guide/
excerpt: "How to quickly install and setup Dredge"
---

## Installation

```bash
$ curl https://dredge.dev/install.sh | bash
```

[The install.sh script](https://github.com/dredge-dev/dredge/blob/main/assets/install.sh) downloads the latest version of the `drg` command line utility and uses sudo to install it in `/usr/local/bin`. Binaries are available for Linux and MacOS on [GitHub releases](https://github.com/dredge-dev/dredge/releases).

## First use

```bash
$ drg init
```

Init does the following:
 * creates a Dredgefile
 * adds a 'hello' workflow
 * discovers the tools you're using and creates resources for them

During the discovery step all providers gets executed, each provider determines whether it is applicable for the project.
 * `github-releases` uses git to determine your repo is on github.com
 * `github-issues` uses git to determine your repo is on github.com
 * `local-docker-compose` checks if there is a `docker-compose.yml` file present
 * `local-doc` checks if there is a `docs` or `documentation` directory present 

At the end of `drg init`, you will get some examples on where to go next:

```
Examples to start using Dredge:

    drg                                Lists all available resources and workflows
    drg hello                          Executes the hello workflow
```

Run `drg` without arguments to see what it can do or have a look at the [drg documentation](/docs/drg/).

## Manual installation

If you prefer not to use the install.sh script, you can manually install `drg` by downloading it from GitHub and making it executable:

```bash
$ curl -L https://github.com/dredge-dev/dredge/releases/latest/download/drg-linux-amd64 > drg
$ chmod +x drg
```

Replace `linux` with `darwin` for MacOS and `amd64` with `arm64 for arm processors. Or start from [the source code](https://github.com/dredge-dev/dredge).

## Creating your first Dredgefile manually

Create a `Dredgefile` with a basic workflow.

```yaml
workflows:
- name: hello
  description: Say hello
  steps:
  - shell:
      cmd: echo hello
```

Run `drg` to see the available workflows.

```bash
$ drg
```

In the output you will find the `hello` workflow.

```
Available Commands:
  hello       Say hello
```

Run the `hello` workflow using `drg`.

```bash
$ drg hello
```
