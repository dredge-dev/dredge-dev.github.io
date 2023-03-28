---
title: "Dredgefile format"
permalink: /docs/dredgefile/
excerpt: "Format and features of the Dredgefile"
---

A Dredgefile is a YAML file that describes resources and workflows. Resources expose commands implemented by providers. Workflows consist of step, that are executed in an environment and can be grouped into buckets. The main sections include `resources`, `workflows` and `buckets`.

An example Dredgefile for a Go project with releases on GitHub could look like this:

```yaml
resources:
  release:
    - provider: github-releases
workflows:
  - name: hello
    description: Say hello
    steps:
      - shell:
          cmd: echo hello
buckets:
  - name: ci
    description: Bucket for CI workflows
    workflows:
      - name: build
        description: Build the project
        steps:
          - shell:
              cmd: go build
```

The Dredgefile defines that `releases` for the project are provided by `github-releases`, there is a workflow that says hello and there is a bucket of CI workflows that contains a `build` workflow. The `drg` command line can now be used to execute the following workflows:

 - `drg get release` to get the releases for the project 
 - `drg hello` to say hello
 - `drg ci build` to execute the build CI workflow

The different parameters for [resources](#resources), [providers](#providers), [workflows](#workflows) and [buckets](#buckets) are defined below.

## Resources

The resources section defines the resources used by Dredge to manage various aspects of the project, such as deployment, documentation, issue tracking, and release management. Each resources has a set of fields and a set of commands. The list of resources for a given type can be retrieved by executing `drg get <resource>`. For example `drg get release` gets the releases for the project, in this case fetching them from GitHub releases.

```yaml
resources:
  deploy:
    - provider: local-docker-compose
      config:
        env: local
        image: fryckbos/dredge-example
        path: .
  doc:
    - provider: local-doc
      config:
        path: docs
  issue:
    - provider: github-issues
  release:
    - provider: github-releases
```

### Deploy

The `deploy` sub-section lists the deployment providers and their configurations. In this example, the `local-docker-compose` provider is used to deploy the application locally using Docker Compose.

```yaml
deploy:
  - provider: local-docker-compose
    config:
      env: local
      image: fryckbos/dredge-example
      path: .
```

#### Fields
The `deploy` resource has four fields: name, version, instances, and type.

 * name:
   * Description: Name
   * Type: string
   * This field represents the unique name or identifier of the deployment.

 * version:
   * Description: Version
   * Type: string
   * This field represents the version of the deployed application or service.

  * instances:
   * Description: Number of instances
   * Type: integer
   * This field represents the number of instances of the deployed application or service.

 * type:
   * Description: Instance type
   * Type: string
   * This field represents the type of the instances, which may refer to the underlying infrastructure or hardware resources, such as CPU, memory, or other specifications.

#### Commands
The `deploy` resource has three commands: get, describe, and update.

 * get:
   * Inputs: None
   * OutputType: []deploy
   * The get command retrieves a list of all deployments. It doesn't require any input parameters and returns an array of deploy objects.

 * describe:
    * Inputs: name
    * OutputType: object
    * The describe command provides detailed information about a specific deployment. The command returns an object containing the complete set of details for the requested deployment.

 * update:
    * Inputs: name
    * OutputType: deploy
    * The update command updates an existing deployment with new field values. The command returns the updated deploy object.

### Doc
The `doc` sub-section lists the documentation providers and their configurations. In this example, the `local-doc` provider is used to find the documentation from the docs folder in the repo.

```yaml
doc:
  - provider: local-doc
    config:
      path: docs
```

#### Fields
The `doc` resource has four fields: name, author, location, and date.

 * name:
   * Description: Name
   * Type: string
   * This field represents the unique name or identifier of the document.

 * author:
   * Description: Author
   * Type: string
   * This field represents the author or creator of the document.

 * location:
   * Description: Location
   * Type: string
   * This field represents the location or path of the document, which could be a local file path or a URL.

 * date:
   * Description: Last updated date
   * Type: date
   * This field represents the date when the document was last updated.

#### Commands
The `doc` resource has two commands: get and search.

 * get:
   * Inputs: None
   * OutputType: []doc
   * The get command retrieves a list of all documents. It doesn't require any input parameters and returns an array of doc objects.

 * search:
   * Inputs: text
   * OutputType: []doc
   * The search command searches for documents based on the given text. The command returns an array of doc objects that match the search criteria.

### Issue
The `issue` sub-section lists the issue tracking providers. In this example, the `github-issues` provider is used to manage project issues on GitHub.

```yaml
issue:
  - provider: github-issues
```

#### Fields
The `issue` resource has five fields: name, title, type, state, and date.

* name:
   * Description: Issue name
   * Type: string
   * This field represents the unique name or identifier of the issue.

 * title:
   * Description: Issue title
   * Type: string
   * This field represents the human-readable title of the issue, providing a brief and clear description of the problem or feature request.

 * type:
   * Description: Issue type
   * Type: string
   * This field represents the type of the issue, such as "bug", "feature request", "enhancement", etc.
 
 * state:
   * Description: Issue state
   * Type: string
   * This field represents the current state of the issue, such as "open", "closed", "in progress", etc.

 * date:
   * Description: Issue creation date
   * Type: date
   * This field represents the date when the issue was created.

#### Commands
The `issue` resource has two commands: get and create.

 * get:
   * Inputs: None
   * OutputType: []issue
   * The get command retrieves a list of all issues. It doesn't require any input parameters and returns an array of issue objects.

 * create:
   * Inputs: None
   * OutputType: issue
   * The create command creates a new issue. There are no default input fields as the provider specifies the inputs they require to create an issue.

### Release
The `release` sub-section lists the release management providers. In this example, the `github-releases` provider is used to manage project releases on GitHub.

```yaml
release:
  - provider: github-releases
```

#### Fields
The `release` resource has five fields: name, date, and title.

 * name:
   * Description: Release name
   * Type: string
   * This field represents the name of the release, typically used to identify the release in a unique and descriptive manner, such as version numbers or code names.

 * date:
   * Description: Release date
   * Type: date
   * This field represents the date of the release. The date type ensures that the value is a valid date format.

 * title:
   * Description: Release title
   * Type: string
   * This field represents the title of the release, which is a more human-readable and descriptive name for the release. It may provide context or highlight major features or changes introduced in that particular release.

#### Commands
The `release` resource has two commands: get, search and describe.

 * get:
    * Inputs: None
    * OutputType: []release
    * The get command retrieves a list of all releases. It doesn't require any input parameters and returns an array of release objects.

 * search:
   * Inputs: text
   * OutputType: []release
   * The search command searches for releases based on a given text input. It takes a single input parameter text, which is used to filter the list of releases based on matching criteria. The command returns an array of release objects that match the provided text.

* describe:
   * Inputs: name
   * OutputType: object
   * The describe command provides detailed information about a specific release identified by its name. It takes a single input parameter name, which corresponds to the name of the release. The command returns an object containing the complete set of details for the requested release.

## Providers

Providers are responsible for implementing the various commands defined for each resource. Dredge supports a range of providers that can be used to manage resources such as deployments, documentation, issues, and releases. Each provider has its own specific configuration options and capabilities.

The following is a list of currently supported providers:
 * `github-releases` for the release resource
 * `github-issues` for the issue resource
 * `local-docker-compose` for the deploy resource
 * `local-doc` for the doc resource

As Dredge continues to evolve, the number of supported providers will increase to accommodate a wider range of use cases and platforms. To ensure seamless integration and extensibility, the mechanism for adding new providers will be enhanced, allowing users to easily plug in custom providers as needed.

### Deploy providers

#### local-docker-compose
The local-docker-compose provider allows management of local deployments using Docker Compose. This provider requires a docker-compose.yml file to be present in the project.

Provider configuration
* `env`: The environment of the deployment (e.g., local, development, staging, production).
* `image`: The name of the Docker image to be used for the deployment.
* `path`: The path to the directory containing the `docker-compose.yml` file.

### Doc providers

#### local-doc
The local-doc provider manages project documentation stored locally in the repository. This provider scans the specified directory for documentation files.

Provider configuration
* `path`: The path to the directory containing the documentation files.

### Issues providers

#### github-issues
The github-issues provider integrates with GitHub to manage project issues. The `gh` command line utility is used to interact with Github, this provider does not require configuration.

### Release providers

#### github-releases
The github-releases provider manages project releases through GitHub. The `gh` command line utility is used to interact with Github, this provider does not require configuration.

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

Use the `edit_dredgefile` step to add workflows, buckets or variables to the calling Dredgefile. It should be used for Dredgefiles that help developers create or extend their Dredgefile. This step can be part of a remote `setup` workflow called through an [exec command](/docs/drg/#exec-command).

edit_dredgefile fields:
 - `add_variables: map` - [Variables](#variables) to add to the Dredgefile
 - `add_workflows: []Workflow` - [Workflows](#workflows) to add to the Dredgefile
 - `add_buckets: []Bucket` - [Buckets](#buckets) to add to the Dredgefile

For example, an `setup` workflow to setup a Python project from the [dredge-repo](https://github.com/dredge-dev/dredge-repo/blob/main/python/Dredgefile):

```yaml
workflows:
  - name: setup
    description: Setup a new python project
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

To execute the workflow, use the [exec](/docs/drg/#exec-command) command:

```
$ drg exec python setup
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

The example setup workflow below asks the user whether they want to initialize a git repo.

{% raw %}
```yaml
workflows:
- name: setup
  inputs:
  - name: GINIT
    description: Inititalize a git repo
    type: select
    values:
    - "true"
    - "false"
  steps:
  - if:
      cond: "{{ .GINIT }}"
      steps:
      - shell:
          cmd: git init
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

### Execute a resource command

Execute a resource command by using `execute`. The output of the command can be registered in a variable.

execute fields:
 * `resource`: name of the resource (eg. deploy)
 * `command`: name of the command to execute on the provided resource (eg. get)
 * `register`: name of the variable to store the result of the command

The following example first executes the `get` command on the `release` resource and uses the result to execute the `update` command on the `deploy` resource.

```yaml
workflows:
  - name: local-latest
    description: deploy the latest version to local
    steps:
      - execute:
          resource: release
          command: get
          register: releases
      - set:
          name: local
          version: "{{ (index .releases 0).name }}"
          instances: 1
      - confirm:
          message: Latest release is {{ .version }}, deploy to {{ .name }}?
      - execute:
          resource: deploy
          command: update
```

### Set a variable value

Set a variable by providing a map of variable names to values.

The following example set the `name` variable to local, the `version` variable to the output of the `"{{ (index .releases 0).name }}"` template and the `instances` variable to 1:

```yaml
workflows:
  - name: set-vars
    steps:
      - set:
          name: local
          version: "{{ (index .releases 0).name }}"
          instances: 1
```

### Output a log message

Output a log message by providing the message and the level for the message.

log fields:
 * `level`: the log level (fatal, error, warn, info, debug, trace). Debug and trace message are only show if `drg` is running in verbose mode
 * `message`: the message to log

The following example set the `name` variable to local, the `version` variable to the output of the `"{{ (index .releases 0).name }}"` template and the `instances` variable to 1:

```yaml
workflows:
  - name: print-log
    steps:
      - log:
          level: info
          message: "My first log message"
```

### Ask the user for confirmation

Asks the use to confirm before proceeding to execute the workflow. If the user does not confirm, the workflow is aborted.

confirm fields:
 * `message`: the confirmation message

The following example ask the user 'Are you sure?'. If the user responds 'no', the workflow is aborted.

```yaml
workflows:
  - name: please-confirm
    steps:
      - confirm:
          message: Are you sure?
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
