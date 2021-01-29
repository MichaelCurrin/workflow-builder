# **GH Actions Workflow Builder**
> The quick and easy way to design a GitHub Actions workflow

{% raw %}

## Source

Repo:

[![MichaelCurrin - workflow-builder](https://img.shields.io/static/v1?label=MichaelCurrin&message=workflow-builder&color=blue&logo=github)](https://github.com/MichaelCurrin/workflow-builder)

Contributions via issues, PRs and [Discussions](https://github.com/MichaelCurrin/workflow-builder/discussions) are welcome.


## Tips

To learn about GH Actions from GitHub's docs, see these [Resources](https://michaelcurrin.github.io/dev-resources/resources/ci-cd/github-actions/).

If you want to know available options for a workflow and if you YAML syntax is valid, edit your workflow on GitHub rather than in a local file. Click on a field or at an indent level where you want to add a field, the press <kbd>CTRL</kbd>+<kbd>SPACE</kbd>. You'll see available options for that context. This can save reading through the docs.


## Name

Example names for your workflow:

```yaml
name: CI
```
```yaml
name: CI Build
```
```yaml
name: CI Test
```
```yaml
name: Node CI
```
```yaml
name: Deno CI
```
```yaml
name: Python CI
```
```yaml
name: Deno CI
```
```yaml
name: GH Pages CI
```
```yaml
name: Release
```


## Triggers

For more options such as building on schedule, see my [Triggers](https://michaelcurrin.github.io/dev-cheatsheets/cheatsheets/ci-cd/github-actions/triggers.html) cheatsheet.

### On a commit push

Push a local commit or commit using the GitHub UI to trigger these `on` conditions.

- `main.yml` - simplest approach. Build on _any_ branch, regardless of Pull Requests.
    ```yaml
    on: push
    ```
- `main.yml` - Build on a chose branch only.
  ```yaml
    on:
      push:
          branches: main
  ```
- `main.yml` - Commit and push to your main branch, or a branch with a Pull Request.
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

The workflow above is setup to not run if there are just changes in your `docs` directory. This is useful to reduce a run that gives no benefit and would still take up processing minutes allocate to your account. If you actually have content in your `docs` directory that matters like for a documentation site, then of course you can remove the ignore parts.

### On a tag or release

Create a tag or a release to trigger your workflow.

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

### Manual

Allows you to run this workflow manually from the Actions tab

```yaml
on:
  workflow_dispatch:
```


## Operating systems

Run on Ubuntu.

```yaml
jobs:
  build:
    name: My job title

    runs-on: ubuntu-latest
```

Run on a matrix of operating systems. Note that quotes not needed for a YAML array.

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

For more details, please explore the [Workflows](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/) section in my Code Cookbook.

That covers:

- Recipes for languages like Python, Ruby, Node, Deno and Go.
- How to choose and configure Actions from the Marketplace.
- Plus how to do tasks like cache assets, build any app to GH Pages (e.g. React, Vue, Next, MkDocs), build a Jekyll 4 site or to build and release assets.

### Checkout

Using the [checkout](https://github.com/denolib/setup-deno) actions.

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v2
```

```yaml
steps:
  - name: Checkout 🛎️
    uses: actions/checkout@v2
    with:
      persist-credentials: false
```

### Setup environment

#### Node

[Node CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/node/).

GH Actions comes with Node and Yarn setup already. But you can use an action if you ned more recent version, or to use a matrix of Node or Yarn versions.

Using the [setup-node](https://github.com/actions/setup-node) action.

```yaml
steps:
  - name: Setup Node.js
    uses: actions/setup-node@v2
    with:
      node-version: '14.x'
```

Use a matrix of Node versions.

```yaml
steps:
  strategy:
    matrix:
      node-version: [10.x, 12.x, 14.x]

  steps:
    - uses: actions/checkout@v2

    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
```

#### Deno

[Deno CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/deno/).

Using the [setup-deno](https://github.com/denolib/setup-deno) action.

```yaml
steps:
  - uses: denolib/setup-deno@v2
    with:
    deno-version: v1.x
```

#### Go

[Go CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/go/).

Using the [setup-go](https://github.com/actions/setup-go) action.

```yaml
steps:
  - name: Set up Go
    uses: actions/setup-go@v2
    with:
      go-version: 1.15
```

#### Python

[Python CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/python/install-deps.html).

Using the [setup-python](https://github.com/actions/setup-python) action.

```yaml
steps:
  - name: Setup Python
    uses: actions/setup-python@v2
    with:
      python-version: '3.x'
```

#### Ruby

Using the [setup-ruby](https://github.com/actions/setup-ruby) action.

```yaml
steps:
  - uses: actions/setup-ruby@v1
    with:
      ruby-version: '2.7'
```

### Install dependencies

This section is not needed for Go or Deno where packages are installed on running, building, testing, etc.

See GH Actions [Cache](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/cache.html) guide.

Using the [cache](https://github.com/actions/cache) action.

#### Python

```yaml
steps:
  - name: Cache dependencies
    uses: actions/cache@v2
    with:
      path: ~/.cache/pip
      key: ${{ runner.os }}-pip-${{ hashFiles('docs/requirements.txt') }}
      restore-keys: |
        ${{ runner.os }}-pip-
        ${{ runner.os }}-
```

```yaml
steps:
  - name: Install dependencies
    run: |
      python -m pip install --upgrade pip
      pip install -r requirements.txt
```

#### Node

See related workflows [here](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/node/npm.html).

```yaml
steps:
  - name: Cache Node.js modules
    uses: actions/cache@v2
    with:
      path: ~/.npm
      key: ${{ runner.OS }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.OS }}-node-
        ${{ runner.OS }}-
```

```yaml
steps:
  - name: Install dependencies
    run: npm install
```

#### Yarn

This uses Yarn's cache directory. On Ubuntu this is `~/.cache/yarn/v6`.

```yaml
steps:
  - name: Get yarn cache
    id: yarn-cache
    run: echo "::set-output name=dir::$(yarn cache dir)"

  - uses: actions/cache@v1
    with:
      path: ${{ steps.yarn-cache.outputs.dir }}
      key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
      restore-keys: |
        ${{ runner.os }}-yarn-
```

```yaml
steps:
  - name: Install dependencies
    run: yarn install
```

### Ruby

```yaml
steps:
  - name: Get cached gems
    uses: actions/cache@v2
    with:
      path: vendor/bundle
      key: ${{ runner.os }}-gems-${{ hashFiles('**/Gemfile.lock') }}
      restore-keys: |
        ${{ runner.os }}-gems-

  - name: Install gems
    run: |
      bundle config set path vendor/bundle
      bundle install --jobs 4 --retry 3
```


## GitHub Pages

For more info and actions related, see [GH Pages](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/deploy-gh-pages/) in my Code Cookbook.

```yaml
- name: Deploy 🚀
  uses: peaceiris/actions-gh-pages@v3

  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
      publish_dir: public
```


## Future development

After some more content, I'll add interactivity - like using React to control a form and YAML output.


{% endraw %}
