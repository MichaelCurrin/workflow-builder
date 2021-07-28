# **GH Actions Workflow Builder**
> The quick and easy way to design a GitHub Actions workflow

{% raw %}

[![MichaelCurrin - workflow-builder](https://img.shields.io/static/v1?label=MichaelCurrin&message=workflow-builder&color=blue&logo=github&style=for-the-badge)](https://github.com/MichaelCurrin/workflow-builder)
[![Made for GH Actions](https://img.shields.io/badge/Made_for-GitHub_Actions-blue?logo=github-actions&logoColor=white&style=for-the-badge)](https://github.com/features/actions)

<!--
  Note - for now this is deliberately a one-page site - the external sites handle nesting.
  This page is intended to be a SPA or at least use CSS/JS to filter to relevant sections.
  For now use TOC here, maybe sidebar in future.
-->

**Table of contents**

- [About](#about)
- [Contributing](#contributing)
- [Tips](#tips)
- [Resources](#resources)
- [YAML syntax](#yaml-syntax)
- [Name](#name)
- [Triggers](#triggers)
  - [On a commit push](#on-a-commit-push)
  - [On a tag or release](#on-a-tag-or-release)
  - [On manual button press](#on-manual-button-press)
  - [On a schedule](#on-a-schedule)
- [Job setup](#job-setup)
  - [Name](#name-1)
  - [Operating systems](#operating-systems)
  - [Environment variables](#environment-variables)
- [Steps](#steps)
  - [Cookbook](#cookbook)
  - [Outline](#outline)
  - [Checkout](#checkout)
  - [Set up environment](#set-up-environment)
    - [Node](#node)
    - [Deno](#deno)
    - [Go](#go)
    - [Rust](#rust)
    - [Python](#python)
    - [Ruby](#ruby)
  - [Install dependencies](#install-dependencies)
    - [Python](#python-1)
    - [Node](#node-1)
  - [Ruby](#ruby-1)
- [Build](#build)
  - [Make](#make)
  - [Node](#node-2)
  - [Jekyll](#jekyll)
- [Deploy](#deploy)
  - [GitHub Pages](#github-pages)
- [Workflows out in the world](#workflows-out-in-the-world)


## About

- A tool to help you build your own CI workflow.
- You don't have to go through pages and pages of documentation on GH Actions, skipping over syntax or Actions you don't care about. This guide focuses on the areas you are most likely to use in a certain context. Such as building a static site, or doing linting and building for a Node or Python app. Already neatly configured and explained.
- Pick and choose from the pieces below that are relevant for you and add them to your workflow file. Like a build your own meal.
- Details are provided as a cheatsheet / tutorial, so you can learn about functionality and advanced settings.
- The snippets here aim to be simple, clean and easy to read and write. Relying on external Actions as dependencies only when appropriate.


## Contributing

See [Contributing](https://github.com/MichaelCurrin/workflow-builder/blob/main/CONTRIBUTING.md) guide.

## Tips

If you want to know available options for a workflow and if you YAML syntax is valid, edit your workflow on GitHub rather than in a local file. Click on a field or at an indent level where you want to add a field, the press <kbd>CTRL</kbd>+<kbd>SPACE</kbd>. You'll see available options for that context. This can save reading through the docs.


## Resources

- I wrote two blog posts on GH Actions:
    - [Beginners guide to GH Actions](https://dev.to/michaelcurrin/beginner-s-guide-to-github-actions-49aa) - overview of features without code snippets.
    - [CI pipeline guide](https://dev.to/michaelcurrin/intro-tutorial-to-ci-cd-with-github-actions-2ba8) - covering Hello World and then a Node app in GH Actions.
- To learn about GH Actions from GitHub's docs, see links in my [Resources](https://michaelcurrin.github.io/dev-resources/resources/ci-cd/github-actions/) list.
- To understand the workflow syntax with some practical examples, see [GitHub Actions](https://michaelcurrin.github.io/dev-cheatsheets/cheatsheets/ci-cd/github-actions/) in my Dev Cheatsheets.


## YAML syntax

- YAML official homepage - [yaml.org](https://yaml.org/).
- The recommended style:
    - For variable names for YAML is dash not underscore. So job name for example should be `build-deploy`.
    - Quotes are not needed in a lot of cases, even if you have spaces.
    - If you do use quotes, use double, not single. A formatter in your IDE can take care of this for you.


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
name: Deploy GH Pages
```
```yaml
name: Release
```


## Triggers

For more options such as building on schedule, see my [Triggers][] cheatsheet.

You can combine the sections below - for example, you can run a task on pushes `main`, on a nightly schedule and also whenever you press the run button (without having to make a commit).

[Triggers]: https://michaelcurrin.github.io/dev-cheatsheets/cheatsheets/ci-cd/github-actions/triggers.html

### On a commit push

Push a local commit or commit using the GitHub UI to trigger these `on` conditions.

- `main.yml` - The simplest approach. Builds on _any_ branch, regardless of Pull Requests.
    ```yaml
    on: push
    ```
- `main.yml` - Builds on a chosen branch only.
  ```yaml
    on:
      push:
        branches: main
  ```
- `main.yml` - Triggered on a commit or push to your main branch or any branch with a Pull Request. Ignore changes to markdown files (such as docs) at _all_ levels (I've tested and this rule works, without having to use `**/*.md`). Note that anything in `docs` directory that is not markdown (such as an image or YAML) can still trigger a run.
    ```yaml
    on:
      push:
        branches: main
        paths-ignore:
          - "*.md"

      pull_request:
        branches: main
        paths-ignore:
          - "*.md"
    ```
- `main.yml` - Similar to above but still watches for changes in markdown files outside the `docs` directory. Such as if you have a static site with markdown content in the root of the repo. If you have a directory within `docs/`, you need the double globalstar as below - I found `docs/` was not sufficient as a rule.
    ```yaml
    on:
      push:
        branches: main
        paths-ignore:
          - "docs/**"
          - README.md

      pull_request:
        branches: main
        paths-ignore:
          - "docs/**"
          - README.md
    ```

The workflow above has been set up to not run if there are just changes in your `docs` directory. This is useful to reduce a run that gives no benefit and would still take up processing minutes allocate to your account. If you actually have content in your `docs` directory that matters like for a documentation site, then of course you can remove the ignore parts.

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

### On manual button press

Allows you to run this workflow manually from the Actions tab.

```yaml
on:
  workflow_dispatch:
```

### On a schedule

Run on a given frequency, using [Crontab][] notation.

Daily at midnight:

```yaml
on:
  schedule:
    - cron:  "0 0 * * *"
```

This is useful for building a site, deploying an application or publishing to a package registry. Also, if you have any code quality or security scans such as a [CodeQL][] workflow.

[Crontab]: https://crontab.com
[CodeQL]: https://github.com/MichaelCurrin/badge-generator/blob/master/.github/workflows/codeql-analysis.yml


## Job setup

### Name

Give a job a name using a key under `jobs` and a pretty name under `name` field.

The job name might be similar to the workflow file's name at the top, but I find it better to be more specific. Like "Node CI" for the workflow and "Build and deploy" as a job name.

Some samples:

```yaml
jobs:
  build:
    name: Build
```

```yaml
jobs:
  build-test:
    name: Build and test
```

```yaml
jobs:
  build-deploy:
    name: Build and deploy
```

### Operating systems

Define the operating system for a job.

Run on Ubuntu. This is the most common flow.

```yaml
jobs:
  build:
    name: Build

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

This seems to be case-insensitive as some people use `macos-latest`.

Note that quotes are not needed in YAML, whether for a string or array of strings.

### Environment variables

```yaml
env:
  VAR_A: Hello

jobs:
  build:
    env:
      VAR_B: World
```


## Steps
> Define steps for a job to run

This section covers some common snippets across languages, for some of my common flows that build, test and deploy.

A note on wording - "setup" is a noun, while "set up" is a verb. See both under the [setup definition][] on the Merriam-Webster online dictionary.

[setup definition]: https://www.merriam-webster.com/dictionary/setup

### Cookbook

For more details, please explore the [Workflows][] section in my Code Cookbook.

That covers:

- Recipes for languages like Python, Ruby, Node, Deno and Go.
- How to choose and configure Actions from the Marketplace.
- Plus how to do tasks like cache assets, build any app to GH Pages (e.g. React, Vue, Next, MkDocs), build a Jekyll 4 site or to build and release assets.

[Workflows]: https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/

### Outline

This is an outline of a generic workflow.

It includes emojis to brighten up the log and make it easier to scan visually. For set up, you might use something related to the language, like a snake for Python.

```yaml
steps:
  - name: Checkout 🛎️
    uses: actions/checkout@v2

  - name: Set up Foo ⚙️  # e.g. Python 🐍, Node or Ruby 💎
    uses: actions/setup-foo@v2
    with:
      foo-version: '1.x'

  - name: Cache dependencies 💾
    uses: actions/cache@v2
    with:
      # ...

  - name: Install dependencies 🔧
    run: # ...

  - name: Check formatting 🎨
    run: # ...

  - name: Lint 🧐
    run: # ...

  - name: Test 🚨
    run: # ...

  - name: Build 🏗️
    run: # ...

  - name: Deploy to GitHub Pages 🚀
    uses: # ...
```

### Checkout

Using the [checkout](https://github.com/actions/checkout) action.

```yaml
steps:
  - name: Checkout 🛎️
    uses: actions/checkout@v2
```

### Set up environment

#### Node

[Node CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/node/).

GH Actions comes with Node and Yarn set up already. But you can use an action if you ned more recent version, or to use a matrix of Node or Yarn versions.

Using the [setup-node][] action.

```yaml
steps:
  - name: Set up Node.js ⚙️
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
    - name: Checkout 🛎️
      uses: actions/checkout@v2

    - name: Use Node.js ${{ matrix.node-version }} ⚙️
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
```

[setup-node]: https://github.com/actions/setup-node

#### Deno

[Deno CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/deno/).

Using the [setup-deno][] action.

```yaml
steps:
  - name: Set up Deno ⚙️ 🦕
    uses: denolib/setup-deno@v2
    with:
    deno-version: v1.x
```

[setup-deno]: https://github.com/denolib/setup-deno

#### Go

[Go CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/go/).

Using the [setup-go][] action.

```yaml
steps:
  - name: Set up Go ⚙️ ⏩
    uses: actions/setup-go@v2
    with:
      go-version: 1.15
```

[setup-go]: https://github.com/actions/setup-go

#### Rust

Using an action from the [rust-lang/simpleinfra](https://github.com/rust-lang/simpleinfra) repo.

```yaml
steps:
  - name: Set up Rust ⚙️ 🦀
    uses: rust-lang/simpleinfra/github-actions/simple-ci@master
    with:
      check_fmt: true
```

#### Python

[Python CI samples](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/python/install-deps.html).

Using the [setup-python]() action.

```yaml
steps:
  - name: Set up Python ⚙️ 🐍
    uses: actions/setup-python@v2
    with:
      python-version: 3.x
```

[setup-python]: https://github.com/actions/setup-python

#### Ruby

Using the [setup-ruby][] action.

```yaml
steps:
  - name: Set up Ruby ⚙️ 💎
    uses: actions/setup-ruby@v1
    with:
      ruby-version: 2.7
```

[setup-ruby]: https://github.com/actions/setup-ruby

### Install dependencies

This section is not needed for Go or Deno where packages are installed on running, building, testing, etc.

See GH Actions [Cache][] guide.

 The cache step is optional but makes the build faster. It will load dependencies from the cache, if the dependencies file is unchanged. Also adds a clean-up step for you to save dependencies to the cache.

[Cache]: https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/cache.html

#### Python

```yaml
steps:
  - name: Cache Python packages 💾
    uses: actions/cache@v2
    with:
      path: ~/.cache/pip
      key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
      restore-keys: |
        ${{ runner.os }}-pip-
        ${{ runner.os }}-

  - name: Install dependencies 🔧
    run: |
      python -m pip install --upgrade pip
      pip install -r requirements.txt
```

#### Node

See related workflows [here](https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/node/npm.html).

For NPM projects:

```yaml
steps:
  - name: Cache NPM packages 💾
    uses: actions/cache@v2
    with:
      path: ~/.npm
      key: ${{ runner.OS }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.OS }}-node-
        ${{ runner.OS }}-

  - name: Install dependencies 🔧
    run: npm install
```

For Yarn projects:

```yaml
steps:
  - name: Get Yarn cache dir 💾
    id: yarn-cache
    run: echo "::set-output name=dir::$(yarn cache dir)"

  - uses: actions/cache@v1 💾
    with:
      path: ${{ steps.yarn-cache.outputs.dir }}
      key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
      restore-keys: |
        ${{ runner.os }}-yarn-

  - name: Install dependencies 🔧
    run: yarn install
```

This uses Yarn's cache directory. On Ubuntu this is `~/.cache/yarn/v6`.


### Ruby

This will setup Ruby in the environment, install gems with Bundler and even cache the gems for faster builds.

```yaml
steps:
  - name: Set up Ruby 💎
    uses: actions/setup-ruby@v1
    with:
      ruby-version: '2.7'
      bundler-cache: true
```

This Ruby setup flow works great for Jekyll projects too. Just make sure to add `jekyll` gem to your `Gemfile` and add a build step as below.

```yaml
steps:
  - name: Build 🏗
    run: bundle exec jekyll build --trace
```

Then your workflow will set up Ruby and Jekyll then build `_site` directory.

If you want to **persist** the `_site` directory output to serve as a GH Pages site, see the [GitHub Pages](#github-pages) section.

See more Jekyll samples and info in my [Jekyll CI][] recipes.

[Jekyll CI]: https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/jekyll/


## Build
> Create your production build output

Bundle your package or app so it can be installed, or build your website assets.

### Make

This depends on setting up `Makefile` with a `build` target.

```yaml
steps:
  - name: Build 🏗️
    run: make build
```

### Node

```yaml
steps:
  - name: Build 🏗️
    run: npm run build
```

```yaml
steps:
  - name: Build 🏗️
    run: yarn build
```

### Jekyll

After setting up Ruby and installing dependencies with Bundler.

Build the site with Jekyll.

```yaml
steps:
  - name: Build 🏗️
    run: bundle exec jekyll build --trace
```

If you want to **persist** the `_site` directory output to serve as a GH Pages site, see the [GitHub Pages](#github-pages) section.


## Deploy

### GitHub Pages

This action will take a given build output directory (like `dist`, `build` or `_site`) and commit it as a single commit on the `gh-pages` branch at the root path. This can then be served as static assets (HTML, CSS and JS) on a GH Pages site.

For more info and related workflows and actions, see [GH Pages][] in my Code Cookbook.

```yaml
- name: Deploy to GitHub Pages 🚀
  if: ${{ github.event_name != 'pull_request' }}
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: _site
```

Steps:

1. Run your build command. This could be anything - such using Jekyll, MkdDocs, or `npm run build` (for React, Vue or Next.js).
2. Then set up this action to point to that directory e.g. in `_site` or `build`.
3. The action will copy the content root of the `gh-pages` branch (this it is default behavior).
4. When that commit is pushed, then your GH Pages site will reload, using the latest content.

Note the use of the `if` condition on this step. This means that your entire workflow can run on a push to your main branch. But on a push to a Pull Request branch, the earlier build steps will run but the deploy step at the end will be skipped. This is useful to avoid deploy your work in progress branch to your production site.

[GH Pages]: https://michaelcurrin.github.io/code-cookbook/recipes/ci-cd/github-actions/workflows/deploy-gh-pages/


## Workflows out in the world

Here are some workflows I have set up for my projects which I'd like to share.

- Badge Generator [GH Pages Deploy](https://github.com/MichaelCurrin/badge-generator/blob/master/.github/workflows/main.yml) - a workflow to build, test and a deploy a Vue app as a GitHub Pages site.
- React Quickstart [GH Pages Deploy](https://github.com/MichaelCurrin/react-quickstart/blob/master/.github/workflows/main.yml) - same as above, but for React.
- Jekyl GH Actions Quickstart [GH Pages Deploy](https://github.com/MichaelCurrin/jekyll-gh-actions-quickstart/blob/main/.github/workflows/main.yml) - using GH Actions in order to support Jekyll 4, rather than the standard Jekyll 3.
- Auto Commit Message [Node CI](https://github.com/MichaelCurrin/auto-commit-msg/blob/master/.github/workflows/main.yml) to build a VS Code extension. Including caching of Node packages for faster builds,lint and format checks, TypeScript compilation, and tests.
- Go Project Template [Go CI](https://github.com/MichaelCurrin/go-project-template/blob/main/.github/workflows/main.yml) - test and build a Go app.
- MkDocs Quickstart [Deploy Docs](https://github.com/MichaelCurrin/mkdocs-quickstart/blob/master/.github/workflows/docs.yml) - build a docs site and deploy to GH Pages.

{% endraw %}
