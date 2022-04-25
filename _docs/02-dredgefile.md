---
title: "Dredgefile format"
permalink: /docs/dredgefile/
excerpt: "Format and features of the Dredgefile"
---

A Dredgefile is a YAML file describing workflows that consist of steps. Workflows are executed in an environment and can be grouped into buckets.

## Workflows

A workflow has a name, description and a list of steps. Workflows are listed when the `drg` command is executed without arguments.

A simple example of a workflow:

```yaml
workflows:
  - name: hello
    description: Say hello
    steps:
      - shell:
          cmd: echo hello
```

Output of the execution:

```
$ drg hello
hello
```

Workflow fields:
 - `name: string` - Name of the workflow
 - `description: string` - Description of the workflow
 - `inputs: []Input` - Inputs for the workflow
 - `steps: []Step` - Steps in the workflow
 - `import: Import` - Import a workflow from Dredgefile, cannot be combined with steps or inputs

Step types
 - `shell`: execute a command in the shell
 - `template`: template a string and write to a file
 - `browser`: open a URL in a browser window
 - `edit_dredgefile`: add workflows, buckets or variables to the calling Dredgefile
 - `if`: conditionally execute a list of steps

### Inputs

Asks the user for inputs that can be used in the workflow. The inputs can be free-form text input:

```yaml
workflows:
- name: echo
  description: Echo the input
  inputs:
  - name: value
    description: The value to echo
  steps:
  - shell:
      cmd: echo {{ .value }}
```

Or selecting a value for a list:

```yaml
workflows:
- name: echo
  description: Echo the input
  inputs:
  - name: value
    description: The value to echo
    type: select
    values:
    - hello world
    - bye world
  steps:
  - shell:
      cmd: echo {{ .value }}
```

Input fields:
 - `name: string` - Name of the variable to set
 - `description: string` - Description of the input
 - `type: string` - *Optional.* Possible types are `select` and `text`, `text` by default
 - `values: []string` - Values of a `select` input, not applicable for `text` inputs
 - `default_value: string` - *Optional.* Default value for a text input

Inputs can be provided as environment variables. If the environment variable is not set, `drg` will prompt for the input:

```
$ value=hello drg echo
hello
```

In case the value environment variable is not set:

```
$ drg echo
The value to echo [value]: hello
hello
```

### Shell Step

Execute a command in the shell. Note: the hello and echo examples above used the shell step.

Fields:
 - `cmd: string` - The command to execute
 - `runtime: string` - Reference to the runtime to execute the command (see [Runtimes](#runtimes)). If no runtime is provided, defaults to executing in the current shell.
 - `stdout: string` - Name of the variable to capture the stdout of the command in. Leaving this field empty prints the output of the command to Dredge stdout
 - `stderr: string` - Name of the variable to capture the stderr of the command in. Leaving this field empty prints stderr of the command to Dredge stderr

### Template Step

Template a string and write to a file. Uses [Go templating](https://pkg.go.dev/text/template), variables can be templated like this: {% raw %}`{{ .title }}`{% endraw %}.

Fields:
 - `input: string` - String to template
 - `source: Source` - Read the string to template from [source](#importing-workflows), cannot be combined with input
 - `dest: string` - Path of the file to write
 - `insert: Insert` - *Optional.* Don't overwrite the `dest` file but insert the generated text into the file. The `Insert` fields defined where the text should be inserted.

{% raw %}
```yaml
workflows:
- name: new-post
  description: Create a new blog post
  inputs:
  - name: title
    description: Title for the blog post
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

Functions:
 - `date "<format>"`: format the current date (see [this blog](https://golang.cafe/blog/golang-time-format-example.html#:~:text=Golang%20Time%20Format%20YYYY%2DMM%2DDD) for more info)
 - `replace <s> <old> <new>`: replace `<old>` by `new` in `<s>`
 - `join <s1> <s2> <separator>`: join the 2 strings with the provided separator
 - `trimSpace <s>`: trims whitespace from the beginning and ending of `<s>`
 - `isTrue <s>`: returns true if `<s>` is `true`, `t`, `yes` or `1`
 - `isFalse <s>`: returns true if `<s>` is `false`, `f`, `no` or `0`

`isTrue` and `isFalse` can be used for conditional templating:
{% raw %}
```
{{if isFalse .ISSUES}}--disable-issues{{end}}
```
{% endraw %}

Insert describes how to insert the templated text into an existing file.
 - `placement: [begin|end|unique]` - Insert at the begin or end of the file / section. Unqiue adds the line at the end of the file if it is not yet present in the file.
 - `section: string` - *Optional.* If not provided the text is inserted at the begin or end of the file. Section is only supported for Go and takes either `import` or a `func` definition.

The example below adds an http service to a Go program by performing 3 steps:

 1. edits the imports
 2. adds setting up the http server in the main function
 3. adds the handler function.

```yaml
workflows:
  - name: add-http-server
    description: Add a http server to the code
    steps:
      - template:
          input: |
            "fmt"
            "net/http"
          dest: main.go
          insert:
            section: import
      - template:
          input: |
            http.HandleFunc("/", handleRoot)
            http.ListenAndServe(":8080", nil)
          dest: main.go
          insert:
            section: func main()
            placement: end
      - template:
          input: |
            func handleRoot(w http.ResponseWriter, req *http.Request) {
                 fmt.Fprintf(w, "hello\n")
            }
          dest: main.go
          insert:
            placement: end
```


### Browser Step

Open a URL in a browser window.

```yaml
workflows:
- name: open
  description: Open the issues ui
  steps:
  - browser:
      url: https://github.com/dredge-dev/dredge/issues
```

### Edit Dredgefile step

Use the `edit_dredgefile` step to add workflows, buckets or variables to the calling Dredgefile. It should be used for Dredgefiles that help developers create or extend their Dredgefile and can only be used when called through an [exec command](/docs/drg/#exec-command).

edit_dredgefile fields:
 - `add_variables: map` - [Variables](#variables) to add to the Dredgefile
 - `add_workflows: []Workflow` - [Workflows](#workflows) to add to the Dredgefile
 - `add_buckets: []Bucket` - [Buckets](#buckets) to add to the Dredgefile

For example, an `init` workflow to setup a Python project in `./python.Dredgefile`:

```yaml
workflows:
  - name: init
    description: Init a new python project
    steps:
      - template:
          input: print("hello")
          dest: main.py
      - edit_dredgefile:
          add_workflows:
            - name: run
              description: Run the script
              steps:
                - shell:
                    cmd: python3 main.py
```

To execute the workflow, use the [exec](/docs/drg/#exec-command) or [init](/docs/drg/#init-command) command:

```
$ drg init ./python.Dredgefile
```

This adds the `run` workflow the the Dredgefile in the current directory:

```yaml
workflows:
  - name: run
    description: Run the script
    steps:
      - shell:
          cmd: python3 main.py
```

Which can be executed using:

```
$ drg run
```

### If step

Execute a list of steps conditionally.

if fields:
 - `cond: string` - Templated string (eg. `{{ .EXISTS }}`). Execute the list of steps if `cond` is true-ish (`true`, `t`, `yes`, `1`)
 - `steps: []Step` - The list of steps to execute

The example below asks the user whether they want to use GitHub issues for issue tracking. The `issue` bucket from `github/issues` is imported into the Dredgefile if they selected `true`.

{% raw %}
```yaml
workflows:
- name: init
  inputs:
  - name: ISSUES
    description: Use GitHub issues for issue tracking
    type: select
    values:
    - "true"
    - "false"
  steps:
  - if:
      cond: "{{ .ISSUES }}"
      steps:
      - edit_dredgefile:
          add_buckets:
          - name: issue
            import:
              source: github/issues
              bucket: issue
```
{% endraw %}

### Importing workflows

Workflows can be imported from other Dredgefiles by supplying the import field. The import object has 3 fields:
 * `source: Source` - Source of the Dredgefile to import from
 * `bucket: string` - *Optional.* Name of bucket to import from
 * `workflow: string` - Name of the workflow to import

Supported sources:
 * Relative paths: `./<relative_path>` eg. `./utils/Dredgefile`
 * Git repo: `<clone_url>:<path_to_dredgefile>` eg. `https://github.com/dredge-dev/dredge-repo.git:python/Dredgefile`
 * [Default dredge repo](https://github.com/dredge-dev/dredge-repo): `<path_to_dredgefile>` eg. `python/Dredgefile`

If the source is a directory, drg will look for a file called `Dredgefile` in the directory. The examples above can be shortened to:
 * `./utils`
 * `https://github.com/dredge-dev/dredge-repo.git:python`
 * `python`

Example to import the `create` workflow from the `issue` bucket from the `github/issues` Dredgefile in the public Dredge repo:

```yaml
workflows:
  - name: create-issue
    import:
      source: github/issues
      bucket: issue
      workflow: create
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

Bucket fields:
 - `name: string` - Name of the bucket
 - `description: string` - Description of the bucket
 - `workflows: []Workload` - Workflows in the bucket
 - `import: Import` - Import a bucket from Dredgefile, cannot be combined with workflows

The workflows inside a bucket can be found by executing `drg <bucket>`. For the example above:

```
$ drg issue
List, create, update issues for this project

Usage:
  drg issue [command]

Available Commands:
  open        Open the issues ui
```

### Importing buckets

Buckets can be imported from other Dredgefiles by supplying the import field. The import object has 2 fields:
 * `source: Source` - Source of the Dredgefile to import from
 * `bucket: string` - Name of bucket to import

For the supported sources see [Importing workflows](#importing-workflows).

Example to import the `issue` bucket from the `github/issues` Dredgefile in the public Dredge repo: 

```yaml
buckets:
  - name: issue
    import:
      source: github/issues
      bucket: issue
```

## Variables

Variables to use in the workflows, for example the version of the tools.

```yaml
variables:
  GO_VERSION: "1.17"
```

## Runtimes

Runtime to execute commands. Dredge can execute commands in the current shell (native runtime) or a Docker container.

For the container runtime, Dredge takes care of creating and executing the container command. This includes:
 - Mounting the project directory into the container
 - Caching container content on the host
 - Opening the required ports

Fields for type `container`
 - `name: string` - Name of the runtime to be reference in the workflows
 - `type: string` - 'container' in this case
 - `image: string` - Docker image
 - `home: string` - *Optional.* Directory to mount project (default: `/home`)
 - `cache: []string` - List of directories in the container to cache on the host for this project
 - `global_cache: []string` - List of directories in the container to cache on the host across all projects
 - `ports: []string` - List of [port mappings](https://docs.docker.com/config/containers/container-networking/#published-ports) for the container. `8080:80` maps TCP port 80 in the container to port 8080 on the host. `8080` maps port 8080 in the container to port 8080 on the host. One port entry can contain multiple port mappings delimited by a comma, eg. `1234,8080` maps ports 1234 and 8080 in the container to ports 1234 and 8080 on the host.

The example belows defines a container to run Jekyll and a workflow to run the website in the container.

```yaml
variables:
  JEKYLL_VERSION: latest
runtimes:
- name: jekyll-container
  type: container
  image: jekyll/jekyll:{{ .JEKYLL_VERSION }}
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

## Example Dredgefiles

* [Dredgefile for the dredge project](https://github.com/dredge-dev/dredge/blob/main/Dredgefile)
* [Dredgefile for this website](https://github.com/dredge-dev/dredge-dev.github.io/blob/main/Dredgefile)
