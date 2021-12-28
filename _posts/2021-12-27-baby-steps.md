---
title:  "Baby steps"
author: Fred
tags: dredge mvp
---

I struggle with software projects being unique snowflakes.

For each project we software developers create workflows using a fleet of different tools. This leads to a unique learning curve for every project: what tools are used, how do I set them up, how do I get to run this thing locally, where can I find the documentation, where can I find help?

With Dredge I want to create a tool that wraps the existing tools and provides an uniform interface to run workflows, automates the setup and hides the intricacies for each particular tool.

## Getting started

To get started I'll create Dredge workflows to build, test and run Dredge itself. I'll use Docker to make it easy to execute the workflows anywhere. To reduce the toil I want Dredge to take care of the tricky bits like setting up the environment variables and mounts (for the code and package cache) for the build container.

## Building dredge

The workflow we want to create:
```
drg build
```

Runs the following command:
```
docker run --rm -v $(pwd)/.dredge/cache/go:/go -v $(pwd):/home -w /home -it golang:1.15.11 go build -o drg
```

The Dredgefile for containing the `build` workflow looks like this:
```
env:
  variables:
    GO_VERSION: 1.15.11
  runtimes:
  - name: build-container
    type: container
    image: golang:${GO_VERSION}
    cache:
    - /go
workflows:
- name: build
  description: Build the executable
  steps:
  - exec: go build -o drg
    runtime: build-container
```

Wow, that's a lot of YAML to create 1 simple docker command. Does that even make sense?

At least we can reuse the runtime for the test workflow:
```
- name: test
  description: Run the tests
  steps:
  - exec: go test
    runtime: build-container
```

## Workflows

I still need to know which workflows are available. That's exactly what happens when running `drg` without a command:

```
Dredge automates developer workflows.

Usage:
  drg [command]

Available Commands:
  build       Build the executable
  help        Help about any command
  run         Run the executable
  test        Run the tests

Flags:
  -h, --help      help for drg
  -v, --verbose   Print verbose output

Use "drg [command] --help" for more information about a command.
```

## Conclusion

Dredge can now be used to build itself and it looks like a very poor version of make. It's not much, but it's a start.

The build time is not great and is dominated by spinning up the docker container. Perhaps we can make Dredge a bit smarter to reuse the build container instead of creating a new one for every build. Stay posted!
