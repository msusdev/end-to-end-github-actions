---
title: Continuous integration using GitHub Actions
author: Part 2 of 3 in the [Build end-to-end CI/CD capabilities directly in GitHub](https://github.com/MSUSDEV/end-to-end-github-actions) series
---

# Continuous integration using GitHub Actions

## Overview

### About us

Presenter Name

☁️ *Presenter Title*

> For questions or help with this series: <msusdev@microsoft.com>

All demos and source code available online:

> <https://github.com/msusdev/end-to-end-github-actions>

## Setting the scene

### Series roadmap

* ~~Session 1:~~
  * ~~Introduction to GitHub Actions~~
* **Session 2:**
  * **↪️ Continuous integration using GitHub Actions**
* Session 3:
  * Deploy applications to Azure using GitHub Actions
  
### Agenda

1. Exploring a sample Next.js web application
1. Building and validating the project
1. Creating a GitHub release
1. Docker-izing the project
1. Automating Docker container builds

## Getting hands-on with GitHub Actions

## Demo: *Learning the syntax*

::: notes

### Automate Build

1. Get the web project (<https://github.com/msusdev/example-next-web-app>)

1. Build and validate web project

    ```sh
    npm install
    npm install --global next
    ```

1. Create build job in workflow

    ```yml
    name: Build Next.js web application
    on: push
    jobs:
    build-project:
      name: Build Project
      runs-on: ubuntu-latest
      steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
    ```

1. Upload artifact

    ```yml
    - name: Upload static site
      uses: actions/upload-artifact@v2
      with:
        name: static-site
        path: .next/
    ```

1. Create release job

    ```yml
    release-project:
      name: Release Project
      runs-on: ubuntu-latest
      needs: build-project
    ```

1. Download artifact

    ```yml
    - name: Download site content
      uses: actions/download-artifact@v2
      with:
        name: site-build
    ```

1. View contents of downloaded artifact

    ```yml
    - name: View content
      run: ls -R
    ```

1. Create GitHub release

    ```yml
    - name: Create GitHub release
      id: create-new-release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.run_number }}
        release_name: Release ${{ github.run_number }}
    ```

1. Compress folder

    ```yml
    - name: Archive site content
      uses: thedoctor0/zip-release@master
      with:
        filename: site.zip
    ```

1. Upload asset to release

    ```yml
    - name: Upload release asset
      uses: actions/upload-release-asset@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        upload_url: ${{ steps.create-new-release.outputs.upload_url }}
        asset_path: ./site.zip
        asset_name: site-v${{ github.run_number }}.zip
        asset_content_type: application/zip
    ```

### Dockerize app

1. Create Dockerfile

    ```dockerfile
    FROM node:alpine

    WORKDIR /app

    COPY . /app

    RUN npm install
    RUN npm run build

    EXPOSE 80

    CMD npm run start
    ```

1. Test container locally

    ```sh
    docker build --tag next .
    docker run --detach --publish 5000:3000 next
    ```

1. Add container job

    ```yml
    name: Build container
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
    ```

1. Push to Docker Hub

    ```yml
    - name: Push to Docker Hub
      uses: docker/build-push-action@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
        repository: msusdev/webnext
        tags: latest, ${{ github.run_number }}
    ```

1. Test Docker Hub locally

    ```sh
    docker pull msusdev/webnext
    docker run --detach --publish 4000:3000 msusdev/webnext:latest
    ```

1. Push to GitHub Packages

    ```yml
    - name: Publish to GitHub Packages
      uses: docker/build-push-action@v1
      with:
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        registry: docker.pkg.github.com
        repository: ${{ github.repository }}/webnext
        tags: latest, ${{ github.run_number }}
    ```

1. Test GitHub Packages locally

    ```sh
    docker login https://docker.pkg.github.com
    docker pull docker.pkg.github.com/msusdev/preshow-test/webnext
    docker run --detach --publish 2000:3000 docker.pkg.github.com/msusdev/preshow-test/webnext
    ```

1. Final results <https://github.com/msusdev/example-next-web-app>

:::

## Review

### Agenda (revisted)

1. Exploring a sample Next.js web application
1. Building and validating the project
1. Creating a GitHub release
1. Docker-izing the project
1. Automating Docker container builds

### Links

* <https://github.com/actions/upload-artifact>
* <https://github.com/actions/download-artifact>
* <https://docs.github.com/actions/reference/workflow-syntax-for-github-actions#jobsjob_idneeds>
* <https://github.com/actions/create-release>
* <https://docs.github.com/actions/reference/context-and-expression-syntax-for-github-actions#contexts>
* <https://github.com/marketplace/actions/zip-release>
* <https://github.com/actions/upload-release-asset>
* <https://docs.github.com/actions/guides/publishing-docker-images>
* <https://github.com/docker/build-push-action/tree/releases/v1>

### GitHub Lab

<https://lab.github.com/msusdev/build-end-to-end-cicd-capabilities-directly-in-github>

## Thanks! Q&A
