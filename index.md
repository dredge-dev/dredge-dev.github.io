---
layout: splash
permalink: /
hidden: true
header:
  overlay_color: "#5e616c"
  overlay_image: /assets/images/back.jpg
  actions:
    - label: "Get started"
      url: "#get-started"
title: Automates DevOps workflows
excerpt: >
  Dredge is a open-source tool that seamlessly integrates with your DevOps tools to streamline and standardize your development and operations workflows, helping your team to work more efficiently and effectively.
feature_row:
  - title: "Abstract"
    excerpt: >
      Makes contributing to your code easy by abstracting tools into resources. New team members or contributors get an easy on-ramp. Less time is spent on figuring out how to get started.
  - title: "Wrap"
    excerpt: >
      Wraps existing tools, doesn't replace them. The goal of the project is to have a common interface for workflows. The implementations are tool specific.

  - title: "Extend"
    excerpt: >
      Create workflows for toilsome tasks, & share them with the team. Use this to automate incident runbooks and to streamline day-to-day operations.
---

<script async id="asciicast-564048" src="https://asciinema.org/a/564048.js"></script>

# Get started

```
curl https://dredge.dev/install.sh | bash    # install Dredge
drg init                                     # initialize the project
drg add release                              # add your first resource
```

# Use cases

## Releases
Automate your release workflows
`drg get release | drg create release`

## Deployments
Manage deployments across environments
`drg get deploy`

## Documentation
Discover and create documentation in a uniform way
`drg get doc | drg search doc | drg create doc`

## Feature flags
Manage features flags across environments
`drg get featureflag | drg create featureflag | drg update featureflag`

## Tickets
Discover and create tickets in a uniform way
`drg get ticket | drg search ticket | drg create ticket`

## Security scans
Make security part of your day-to-day workflows
`drg get securityscan`

## Many more...
Add your own resources and workflows


# Extending Dredge

The resources available in Dredge can be extended by users by creating a resource definition. The resource definition contains the name of the resource, the field definitions and the command definitions. The command definitions are abstract and need to be implemented by the resource provider. Workflows can be added to the Dredgefile in order to automate tasks across multiple resources. See [the docs](/docs) for more information.

Dredge uses the following principles:

{% include feature_row %}

# Vision

There is a proliferation of DevOps tools. Each project puts together a set of tools in a specific way, creating their bespoke workflows and processes to develop and operate the software. This leads to a large cognitive load for the engineers, makes getting started on projects difficult and ultimately wastes engineering resources.

Dredge uses a standardized set of resources as an abstraction for DevOps tools. This makes it easy for engineers to discover and execute the workflows for the project, instead of depending on tribal knowledge or READMEs.

By abstracting specific tools into generic resources, Dredge will enable a loosely coupled interoperability between DevOps tools. Workflows operate on abstract resources, enabling replacing tools and reusing workflows between projects with different tooling.
