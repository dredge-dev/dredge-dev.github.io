---
title: "Quick-Start Guide"
permalink: /docs/quick-start-guide/
excerpt: "How to quickly install and setup Dredge"
---

## Installation

Binaries are available in [the GitHub releases](https://github.com/dredge-dev/dredge/releases). The commands below are for x86 processors. For ARM processors replace amd64 with arm64.

### Linux

```
curl -L https://github.com/dredge-dev/dredge/releases/latest/download/drg-linux-amd64 > drg
chmod +x drg
mv drg /usr/local/bin/
```

## macOS

```
curl -L https://github.com/dredge-dev/dredge/releases/latest/download/drg-darwin-amd64 > drg
chmod +x drg
mv drg /usr/local/bin/
```

## Windows

```
Invoke-WebRequest -Uri https://github.com/dredge-dev/dredge/releases/latest/download/drg-windows-amd64 -OutFile drg
```

# Run Dredge

Create a `Dredgefile` with a basic workflow.

```
workflows:
- name: hello
  description: Say hello
  steps:
  - shell:
      cmd: echo hello
```

Run `drg` to see the available workflows.

```
drg
```

In the output you will find the `hello` workflow.

```
Available Commands:
  hello       Say hello
```

Run the `hello` workflow using `drg`.

```
drg hello
```
