---
title: Importing workflows
author: Fred
tags: workflows buckets import
---

Reducing toil by adding yet another tool where you have to manually create yaml files? That doesn't sound right...

The idea behind Dredge is to reduce the toil. Setting up Dredge should be as toil-free as possible. We want to make it easy to start, upgrade and extend a software project. By storing Dredgefiles in a central Dredge repo, we can build out a repository of common workflows and share them in a executable way. Instead of following a tutorial on how to add tool x/y/z to a project, it should be as simple as:

```
drg init <tool>
```

The public Dredge repo can be found [here](https://github.com/dredge-dev/dredge-repo). It currently only has a small example to set up a very simple python project, that workflow can be executed like this:

```
drg init python
```

This creates a `main.py` file:

```python
print("hello world")
```

And a `Dredgefile`:

```yaml
workflows:
  - name: run
    description: Run the python program
    steps:
      - shell:
          cmd: python3 main.py
```

You can now run the program by executing:

```
drg run
```

The Dredgefile with the `init` workflow (stored in [the python directory in the public Dredge repo](https://github.com/dredge-dev/dredge-repo/blob/main/python/Dredgefile)) looks like this:

```yaml
workflows:
  - name: init
    description: Init a new python project
    steps:
      - template:
          input: 'print("hello world")'
          dest: main.py
      - edit_dredgefile:
          add_workflows:
            - name: run
              description: Run the python program
              steps:
                - shell: 
                    cmd: python3 main.py
```

This is a very simple example, but you can see where we're going with this. Here the `init` workflow is writing the `run` workflow to the new Dredgefile. In this case the `run` workflow is only 1 simple step.

For more complex cases, the copy-paste of a workflow in the Dredgefile can become difficult to maintain. For this I've added the ability to import workflows and buckets into a Dredgefile.

```yaml
buckets:
  - name: issue
    import:
      source: github/issues
      bucket: issue
```

The Dredgefile above imports the `issue` bucket from the [github/issues Dredgefile](https://github.com/dredge-dev/dredge-repo/blob/main/github/issues/Dredgefile). This bucket contains 3 workflows:
 * open: Open the issues ui
 * list: List the open issues
 * create: Create a new issue

For example `drg issue open` opens the Github issues UI.

To avoid writing the Dredgefile manually, use:

```
drg init github/issues
```

You can also create your own Dredge repo to store your Dredgefiles, this is well suited for internal workflows. Dredgefiles can be sourced from Git repos. The full version of the `init` command above:

```
drg init https://github.com/dredge-dev/dredge-repo.git:github/issues/Dredgefile
```

Or generically:

```
drg init <git-clone-path>:<path-to-dredgefile>
```

The import functionality can also be used to split a large Dredgefile into multiple parts. This can be achieved by importing from a relative path (starting with `./`):

```yaml
buckets:
  - name: tools
    import:
      source: ./tools/Dredgefile
      bucket: tools
```

imports the `tools` bucket from the local file with relative path `./tools/Dredgefile`. The relative path can also be used for the `init` command.


This new functionality was released in [version 0.0.3](https://github.com/dredge-dev/dredge/releases/tag/v0.0.3). This version contains:
 - Workflows and buckets can be imported from other Dredgefile
 - Exec command to execute workflows from other Dredgefile
 - Init shortcut to execute init workflow from other Dredgefile
 - New step type edit_dredgefile that allows to add workflows and buckets to the calling Dredgefile

Check out the updated documentation: the [drg command](/docs/drg/) and the [Dredgefile format](/docs/dredgefile/).

**Stay tuned for more to come! We are far from the goal to make common software workflows less toilsome, but the public repo, init workflows and imports allow us to start building a set of common workflows.**
