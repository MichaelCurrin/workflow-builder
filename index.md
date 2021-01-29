## GH Actions Workflow Builder
> The quick and easy way to design a GitHub Actions workflow

{% raw %}

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

See [Workflows](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/) in my Code Cookbook.

That covers GH Actions recipes for languages like Python, Ruby, Node, Deno and Go. Plus how to do tasks like build to GH Pages, build a Jekyll site or build and release assets.

{% endraw %}
