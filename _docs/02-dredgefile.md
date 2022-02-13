---
title: "Dredgefile Format"
permalink: /docs/dredgefile/
excerpt: "Format and features of the Dredgefile"
---

A Dredgefile is a YAML file describing workflows that consist of steps. Workflows are executed in an environment and can be grouped into buckets.

## Workflows

A workflow has a name, description and a list of steps. Workflows are listed when the `drg` command is executed without arguments.

Fields
 - `name: string` - Name of the workflow
 - `description: string` - Description of the workflow
 - `inputs: dict` - Map with the name and description of the input
 - `steps: []Step` - Steps in the workflow

Step types
 - `shell`: execute a command in the shell
 - `template`: template a string and write to a file
 - `browser`: open a URL in a browser window

### Inputs

Inputs can be provided as environment variables. If the environment variable is not set, `drg` will prompt for the input.

### Shell Step

Execute a command in the shell.

Fields
 - `cmd: string` - The command to execute
 - `runtime: string` - Reference to the runtime to execute the command (see [Env > Runtimes](#runtimes))

If no runtime is provided, defaults to executing in the current shell.

### Template Step

Template a string and write to a file. Uses [Go templating](https://pkg.go.dev/text/template), variables can be templated like this: `{{ .title }}`.

{% raw %}
```yaml
workflows:
- name: new-post
  description: Create a new blog post
  inputs:
    title: Title for the blog post
  steps:
  - template:
      input: |
        ---
        title: {{ .title }}
        author:
        tags:
        ---

        Start here.
      dest: '_posts/{{ date "2006-01-02" }}-{{ replace .title " " "-" }}.md'
```
{% endraw %}

Functions
 - `date "<format>"`: format the current date (see [this blog](https://golang.cafe/blog/golang-time-format-example.html#:~:text=Golang%20Time%20Format%20YYYY%2DMM%2DDD) for more info)
 - `replace <s> <old> <new>`: replace `<old>` by `new` in `<s>`

### Browser Step

Open a URL in a browser window.

```yaml
workflows:
- name: open
  description: Open the issues ui
  steps:
  - browser: https://github.com/dredge-dev/dredge/issues
```

## Env

The environment defines variables and runtimes that can be used in the workflows.

### Variables

Variables to be reuse in the workflows, for example the version of the tools used.

```yaml
env:
  variables:
    GO_VERSION: 1.17
```

### Runtimes

Runtime to execute commands. Dredge can execute commands in the current shell (native runtime) or a Docker container.

For the container runtime, Dredge takes care of creating and executing the container command. This includes:
 - Mounting the project directory into the container
 - Caching container content on the host
 - Opening the required ports

Fields for type `container`
 - `name: string` - Name of the runtime to be reference in the workflows
 - `type: string` - 'container' in this case
 - `image: string` - Docker image
 - `home: string` - Optional directory to mount project (default: `/home`)
 - `cache: []string` - List of directories in the container to cache on the host
 - `ports: []string` - List of [port mappings](https://docs.docker.com/config/containers/container-networking/#published-ports) for the container. `8080:80` maps TCP port 80 in the container to port 8080 on the Docker host.

The example belows defines a container to run Jekyll and a workflow to run the website in the container.

```yaml
env:
  variables:
    JEKYLL_VERSION: latest
  runtimes:
  - name: jekyll-container
    type: container
    image: jekyll/jekyll:${JEKYLL_VERSION}
    home: /srv/jekyll
    cache:
    - /usr/gem/gems/
    ports:
      - 4000:4000
workflows:
- name: run
  description: Run Jekyll locally on port 4000
  steps:
  - shell:
      cmd: /bin/bash -c 'bundle install && jekyll serve --watch --force_polling --verbose'
      runtime: jekyll-container
```

## Buckets

Workflows can be grouped into buckets. The `drg` command without arguments will show the buckets and the top level workflows.

```yaml
buckets:
- name: issue
  description: List, create, update issues for this project
  workflows:
  - name: open
    description: Open the issues ui
    steps:
    - browser: https://github.com/dredge-dev/dredge/issues
```

The workflows inside a bucket can be found by executing `drg <bucket>`. For the example above:

```
$ drg issue
List, create, update issues for this project

Usage:
  drg issue [command]

Available Commands:
  open        Open the issues ui
```

## Example Dredgefiles

* [Dredgefile for the dredge project](https://github.com/dredge-dev/dredge/blob/main/Dredgefile)
* [Dredgefile for this website](https://github.com/dredge-dev/dredge-dev.github.io/blob/main/Dredgefile)
