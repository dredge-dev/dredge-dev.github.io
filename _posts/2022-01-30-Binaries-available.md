---
title: Binaries available
author: Fred
tags: github action binaries mvp
---

I've just released v0.0.1 of Dredge. It's very minimal: I used it to compile and test Dredge and to template this blog post. Not very useful yet.

If you want to take it for a spin, you can find the binaries on [the Github release page](https://github.com/dredge-dev/dredge/releases).

# Installation

Binaries are built for Linux, macOS and Windows. In case you're using Linux:

```
curl -L https://github.com/dredge-dev/dredge/releases/latest/download/drg-linux-amd64 > drg
chmod +x drg
mv drg /usr/local/bin/
```

For macOS:

```
curl -L https://github.com/dredge-dev/dredge/releases/latest/download/drg-darwin-amd64 > drg
chmod +x drg
mv drg /usr/local/bin/
```

For Windows:

```
Invoke-WebRequest -Uri https://github.com/dredge-dev/dredge/releases/latest/download/drg-windows-amd64 -OutFile drg
```

# Dredgefile

Define a Dredgefile to put your development workflows into code:

```
workflows:
- name: hello
  description: Say hello
  steps:
  - shell:
      cmd: echo hello
```

# Try it out

See the define workflows:

```
drg
```

Run the `hello` workflow defined above:

```
drg hello
```

It should say 'hello'.