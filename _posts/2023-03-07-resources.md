---
title: Resources!
author: Fred
tags: release resources providers
---

It's been a while since I last wrote about Dredge and I think my last post explains why that is. I got myself into a corner with the approach of programming shareable workflows in YAML. Although I think the ability to share workflows is useful, the programming in YAML aspect didn't make sense to me. It took me a while to realise that developer workflows depend on the tools that you use and that the integration with those tools should not be programmed in YAML.

In order to simplify the interactions between existing tools and Dredge, I'm introducing the `resources` concept. Dredge abstracts tools into actionable resources. As a Dredge user, I can use these resources to perform my day-to-day work: creating a ticket, searching code docs, creating a release, deploying to production, updating the runbooks, etc. For example:

Getting the list of releases for the project:

```bash
$ drg get release
name        date               title
0.0.2       about 1 month ago  Second release
0.0.1       about 1 month ago  First release
```

Searching the project documentation:
```bash
$ drg search doc --text "design"
name       author  location                                                              date
design.md          /Users/fryckbos/Documents/git/fryckbos/dredge-example/docs/design.md
```

Deploying the latest version locally:
```bash
$ drg update deploy/local
version [version]: 0.0.2
instances [instances]: 1
[03 Mar 23 07:39 CET] Info Starting docker-compose
```

The resources are configured in the Dredgefile. Resources are defined using a provider specific for the tool you use.

Releases through Github releases:
```yaml
resources:
  release:
    - provider: github-releases
```

Docs stored in the repo:
```yaml
resources:
  doc:
    - provider: local-doc
      config:
        path: ./docs/
```

Local deploy using Docker compose:
```yaml
resources:
  deploy:
    - provider: local-docker-compose
      config:
        env: local
        path: ./run/local/
        image: fryckbos/dredge-example
        proto: http
```

You can find an example project with resources [here](https://github.com/fryckbos/dredge-example/).

Besides automating workflows, having the tooling for a project defined in the Dredgefile brings other benefits as well. As a new contributor to the project I'm able to immediatly see which tools the project uses and how. It should allow new contributors to get up to speed faster and help reduce the reliance on tribal knowledge.

Workflows are still an important component of Dredge and can be used to automate tasks by executing commands on resources. For example deploying the latest released version of the project locally can be achieved like this:

{% raw %}
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
{% endraw %}

The workflow calls the `get` action on the `release` resource in order to get the list of releases. In the second step the variables for the `update` action on the `deploy` resource are set, this action requires the name of the deployment, the version to deploy and the number of instances. The third step asks the user to confirm the deployment and the fourth step executes the `update` action on the `deploy` resource.

This workflow is agnostic to the provider of the resource. Currently the releases for this example project are stored in Github. However if the project was to be migrated to Gitlab, one only needs to swap the github-releases provider for a (non-existing at this point) gitlab-releases provider. The workflow would not be affected by this change.

The resource and provider concepts are still very early but feel like a good way to move the project forward. Currently there are only a few example providers implemented: github-releases, github-issues, local-doc, local-docker-compose. Which provider would you like to use?
