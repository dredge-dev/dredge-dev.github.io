---
title: "drg command"
permalink: /docs/drg/
excerpt: "Explains the drg command"
---

## Dredge commands

Running `drg` without a [Dredgefile](/docs/dredgefile/) in the current directory, will give you this output.

```
$ drg
Dredge automates DevOps workflows.

Usage:
  drg [command]

Available Commands:
  exec        Execute a remote workflow
  help        Help about any command
  init        Initialize a Dredge project

Flags:
  -h, --help      help for drg
  -v, --verbose   Print verbose output

Use "drg [command] --help" for more information about a command.
```

### init command

The `init` command does the following:
 * creates a Dredgefile
 * adds a `hello` workflow
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

## Executing workflows

When you add a workflow to the `Dredgefile`, for example by running `drg init`:

```
workflows:
  - name: hello
    description: Say hello
    steps:
      - shell:
          cmd: echo hello
```

The output of the `drg` command will change to include this workflow in the Workflow Commands section:

```
Dredge automates DevOps workflows.

Usage:
  drg [command]

Workflow Commands:
  hello       Say hello

Additional Commands:
  exec        Execute a remote workflow
  help        Help about any command
  init        Initialize a Dredge project

Flags:
  -h, --help      help for drg
  -v, --verbose   Print verbose output

Use "drg [command] --help" for more information about a command.
```

The `hello` workflow can now be executed using:

```
$ drg hello
```

In general, [workflows](/docs/dredgefile/#workflows) and [buckets](/docs/dredgefile/#buckets) defined in the `Dredgefile` in the current directory are added as commands to `drg`. To execute a workflow use:

```
$ drg <workflow_name>
```

For example, if a `build` workflow defined, the workflow can be executed using:

```
$ drg build
```

To execute a workflow in a bucket:

```
$ drg <bucket_name> <workflow_name>
```

For example, if a `build` workflow defined in the `ci` bucket, the workflow can be executed by running:

```
$ drg ci build
```

### exec command

Execute a workflow from a remote Dredgefile.

```
$ drg exec <source> <bucket_name> <workflow_name>
```

The exec command takes the following parameters:
 * `source`: reference to the Dredgefile to execute
 * `bucket_name`: (optional) name of the bucket
 * `workflow_name`: name of the workflow

Supported sources:
 * Relative paths: `./<relative_path>` eg. `./utils/Dredgefile`
 * Git repo: `<clone_url>:<path_to_dredgefile>` eg. `https://github.com/dredge-dev/dredge-repo.git:python/Dredgefile`
 * [Default dredge repo](https://github.com/dredge-dev/dredge-repo): `<path_to_dredgefile>` eg. `python/Dredgefile`

If the source is a directory, drg will look for a file called `Dredgefile` in the directory. The examples above can be shortened to:
 * `./utils`
 * `https://github.com/dredge-dev/dredge-repo.git:python`
 * `python`

To execute the `setup` workflow of the Dredgefile in the `python` directory in [the default dredge repo](https://github.com/dredge-dev/dredge-repo/):

```
$ drg exec python setup
```

### Resource commands

Resources are implemented by providers and expose a set of commands. The fields and commands per resources can be found [in the resources section of the Dredgefile documentation](/docs/dredgefile/#resources).

When you add a resource, the `drg` command will add the available commands in the Resource Commands section:

```
Dredge automates DevOps workflows.

Resources: issue, release

Usage:
  drg [command]

Resource Commands:
  create      Create a resource of the provided type
  describe    Describe a resource with the provided type and name
  get         Get all resources of the provided type
  search      Search for a resource of the provided type
  update      Update a resource with the provided type and name

Additional Commands:
  exec        Execute a remote workflow
  help        Help about any command
  init        Initialize a Dredge project

Flags:
  -h, --help      help for drg
  -v, --verbose   Print verbose output

Use "drg [command] --help" for more information about a command.
```

To create a new issue use:

```
$ drg create issue
```
