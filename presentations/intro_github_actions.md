---
title: Introduction to GitHub Actions
author: Part 1 of 3 in the [Build end-to-end CI/CD capabilities directly in GitHub](https://github.com/MSUSDEV/end-to-end-github-actions) series
---

# Introduction to GitHub Actions

## Overview

### About us

Presenter Name

☁️ *Presenter Title*

> For questions or help with this series: <msusdev@microsoft.com>

All demos and source code available online:

> <https://github.com/msusdev/end-to-end-github-actions>

## Setting the scene

### Series roadmap

* **Session 1:**
  * **↪️ Introduction to GitHub Actions**
* Session 2:
  * Continuous integration using GitHub Actions
* Session 3:
  * Deploy applications to Azure using GitHub Actions
  
### Agenda

1. Learning the syntax
1. Building a simple project

## Getting hands-on with GitHub Actions

## Demo: *Learning the syntax*

::: notes

```yml
on: push
```

```yml
on: push
jobs:
  first-job:
```

```yml
on: push
jobs:
  first-job:
    steps:
      - run: node --version
      - run: npm --version
```

```yml
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    steps:
      - run: node --version
      - run: npm --version
```

```yml
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
```

```yml
name: Test Node with Workflow
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
```

```yml
name: Test Node with Workflow
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
      - run: npm install
```

```yml
name: Test Node with Workflow
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
      - uses: actions/checkout@v2
```

```yml
name: Test Node with Workflow
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
      - uses: actions/checkout@v2
      - run: npm install
```

```yml
name: Test Node with Workflow
on: push
jobs:
  first-job:
    runs-on: ubuntu-latest
    container: node:12.18.4
    steps:
      - run: node --version
      - run: npm --version
      - uses: actions/checkout@v2
      - run: npm install
      - run: node index.js
```

:::

## Demo: *Building a simple project*

::: notes

```sh
npm init --force
npm install --save-dev moment
touch index.js
```

```json
{
  "name": "app",
  "version": "1.0.0",
  "main": "index.js",
  "devDependencies": {
    "moment": "^2.29.1"
  }
}
```

```js
var moment = require('moment');
var date = moment().format('LL');
console.log(date);
```

```gitignore
node_modules/
package-lock.json
```

:::

## Review

### Agenda (revisted)

1. Learning the syntax
1. Building a simple project
1. GitHub Lab

### Links

* <https://docs.github.com/actions/learn-github-actions/introduction-to-github-actions#understanding-the-workflow-file>
* <https://hub.docker.com/_/node>
* <https://github.com/actions/checkout>

### GitHub Lab

<https://lab.github.com/msusdev/build-end-to-end-cicd-capabilities-directly-in-github>

## Thanks! Q&A
