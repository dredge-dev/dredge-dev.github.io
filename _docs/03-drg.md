---
title: "drg command"
permalink: /docs/drg/
excerpt: "Explains the drg command"
---

## Dredge commands

Running `drg` without a [Dredgefile](/docs/dredgefile/) in the current directory, will give you this output.

```
$ drg
Dredge automates developer workflows.

Usage:
  drg [command]

Available Commands:
  exec        Execute a remote workflow
  help        Help about any command
  init        Run a remote init workflow

Flags:
  -h, --help      help for drg
  -v, --verbose   Print verbose output

Use "drg [command] --help" for more information about a command.
```

The workflows and buckets defined in the `Dredgefile` in the current directory are added as commands. To execute a workflow:

```
$ drg <workflow_name>
```

For example, if a `build` workflow defined, the workflow can be executed by running:

```
$ drg build
```

To execute a workflow in a bucket:

```
$ drg <bucket_name> <workflow_name>
```

For example, if a `new` workflow defined in the `issue` bucket, the workflow can be executed by running:

```
$ drg issue new
```

### exec command

Execute a workflow from any Dredgefile.

```
$ drg exec <source> <bucket_name> <workflow_name>
```

The exec command takes the following parameters:
 * source: reference to the Dredgefile to execute
 * bucket_name: (optional) name of the bucket
 * workflow_name: name of the workflow

Supported sources:
 * Relative paths: `./<relative_path>` eg. `./utils/Dredgefile`
 * Git repo: `<clone_url>:<path_to_dredgefile>` eg. `https://github.com/dredge-dev/dredge-repo.git:python/Dredgefile`
 * [Default dredge repo](https://github.com/dredge-dev/dredge-repo): `<path_to_dredgefile>` eg. `python/Dredgefile`

If the source is a directory, drg will look for a file called `Dredgefile` in the directory. The examples above can be shortened to:
 * `./utils`
 * `https://github.com/dredge-dev/dredge-repo.git:python`
 * `python`

To execute the `init` workflow of the Dredgefile in the `python` directory in the default dredge repo:

```
$ drg exec python init
```

### init command

Shortcut for executing the `init` workflow in the provided Dredgefile. Uses the same source format as exec.

```
$ drg init <source>
```

To execute the `init` workflow of the Dredgefile in the `python` directory in the default dredge repo:

```
$ drg init python
```
