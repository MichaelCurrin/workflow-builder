## GH Actions Workflow Builder
> The quick and easy way to design a GitHub Actions workflow

{% raw %}

## Source

Repo:

[![MichaelCurrin - workflow-builder](https://img.shields.io/static/v1?label=MichaelCurrin&message=workflow-builder&color=blue&logo=github)](https://github.com/MichaelCurrin/workflow-builder)

Contributions via issues, PRs and [Discussions](https://github.com/MichaelCurrin/workflow-builder/discussions) are welcome.


## Tips

To learn about GH Actions from GitHub's docs, see these [Resources](https://michaelcurrin.github.io/dev-resources/resources/ci-cd/github-actions/).

If you want to know available options for a workflow and if you YAML syntax is valid, edit your workflow on GitHub rather than in a local file. Click on a field or at an indent level where you want to add a field, the press <kbd>CTRL</kbd>+<kbd>SPACE</kbd>. You'll see available options for that context. This can save reading through the docs.


## Triggers

For more options such as building on schedule, see my [Triggers](https://michaelcurrin.github.io/dev-cheatsheets/cheatsheets/ci-cd/github-actions/triggers.html) cheatsheet.

### On pushes

- `main.yml`
    ```yaml
    on:
    push:
        branches: main
        paths-ignore:
        - 'docs/**'
    pull_request:
        branches: main
        paths-ignore:
        - 'docs/**'
    ```

### On a tag or release

- `release.yml`
    ```yaml
    on:
      release:
        types: created
    ```
- `tag.yml`
    ```yaml
    on:
    push:
        tags:
        - 'v*'
    ```


## Operating system

Run on Ubuntu.

```yaml
jobs:
  build:
    name: My job title

    runs-on: ubuntu-latest
```

Run on a matrix of operating systems.

```yaml
jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, macOS-latest, windows-latest]
      runs-on: ${{ matrix.os }}
```


## Steps

This section covers some common snippets across languages, for some of my common build, test and deploy flows.

TODO: I'll add some snippets here soon.

For more details, please explore the [Workflows](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/) section in my Code Cookbook. 

That covers:

- Recipes for languages like Python, Ruby, Node, Deno and Go.
- How to choose and configure Actions from the Marketplace. 
- Plus how to do tasks like cache assets, build any app to GH Pages (e.g. React, Vue, Next, MkDocs), build a Jekyll 4 site or to build and release assets.


## Future development

After some more content, I'll add interactivity - like using React to control a form and YAML output.


{% endraw %}
