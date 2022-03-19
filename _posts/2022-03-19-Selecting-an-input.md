---
title: Selecting an input
author: Fred
tags: input select release
---

As a next step in the project I'm trying to create useful Dredgefiles. For `init` workflows I noticed the need to select the version of a framework. Since only a limited set of versions make sense, providing a free-text input is a poor experience. Aa a user I still needs to find out what the possible framework versions are. Let's remove that piece of toil!

I've just released v0.0.4 of Dredge including `select` input types, where you can provide a list of applicable values for a workflow input.

```yaml
workflows:
- name: update_java
  description: Update to a newer Java version
  inputs:
  - name: version
    description: Java version to update to
    type: select
    values:
    - 11
    - 17
  steps:
  - shell:
      cmd: ...
```

![select](/assets/images/blog_select_example.png)

For course this can also be used with environment variables:

```
version=11 drg update_java
```

This is breaking change in the Dredgefile. The previous format of the workflow inputs was a map from the name of the input to the description of the input. Inputs are now objects with the following fields: name, description, type, values, default_value. The type can be `text` or `select`, is the type is not provided `text` is used. Check out the docs [docs](/docs/dredgefile/) for more information.

Big thank you to [promptui](https://github.com/manifoldco/promptui), a Go library that makes it very easy to implement select inputs on the command line.