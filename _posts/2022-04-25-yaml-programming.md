---
title: Oh no! YAML programming...
author: Fred
tags: yaml release conditional
---

I've just released version 0.0.6 and I must say I have doubts about this one. I'll start with the least controversional.

The `unique` placements was added to the template step. This placement will only add the provided line at the end of the file if the line doesn't already exist in the file. For example to add something to `.gitignore`.

The container runtime now has a `global_cache` field to mount things that should be cached across all projects that use this runtime. For example when mounting credentials into cloud tools containers.

The output (stdout and stderr) of a shell command can now be captured into a variable. This allows you to use the output of a command in a next step.

New template functions `trimSpace`, `isTrue` and `isFalse` were added. `trimSpace` trims the whitespace at the beginning and ending of a string. `isTrue` returns true for true-ish values (`true`, `t`, `yes`, 1). `isFalse` returns true for false-ish values (`false`, `f`, `no`, `0`).

`isTrue` and `isFalse` can be used for conditional templating:
{% raw %}
```
{{if isFalse .ISSUES}}--disable-issues{{end}}
```
{% endraw %}

And now the one I'm worried about: the `if` step. The `if` step takes a `cond` field and a list of `steps`. If the `cond` template is returned to a true-ish value, the steps are executed.

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

At which point you would start to think: why are you building a programming language from scratch in YAML? Well... Yes, but I'm trying to reduce toil on developers and make it easy from them to automate their workflows... By having people learn a whole new language based on YAML? I don't like the sound of that.

As I said at the start of this post, I'm having doubts about this approach... Let me know what you think!
