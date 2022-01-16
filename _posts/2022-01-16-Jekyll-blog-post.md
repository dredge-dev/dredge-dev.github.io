---
title: Jekyll blog post
author: Fred
tags: github pages use-case mvp
---

I used [Dredge](https://github.com/dredge-dev/dredge/) to create this post! This is the first use cases apart from building Dredge. It's one of those tasks where I have to look up where to put the file, what the filename convention is and how the file is structured. All simple tasks that I rather not have to about.

In [the repo for this blog](https://github.com/dredge-dev/dredge-dev.github.io) I can now use the following command to create a new blog post:

```
$ drg new-post
Title for the blog post [title]: Jekyll blog post
```

The command asks for the title and creates `_posts/2022-01-16-Github-pages.md` using a basic template.

## The new workflow

The workflow in [the Dredgefile](https://github.com/dredge-dev/dredge-dev.github.io/blob/main/Dredgefile) looks like this:

```
{% raw %}
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
{% endraw %}
```

I'm using [Go templating](https://pkg.go.dev/text/template) to render the template and added the `date` and `replace` function in order to generate the filename.

A workflow can now take inputs. Inputs can be provided through environment variables. When the environment variable is not present Dredge will prompt for the input on the command line as seen in the example above. The non-interactive equivalent of the command above is:

```
$ title="Github pages" drg new-post
```

Happy dredging!
