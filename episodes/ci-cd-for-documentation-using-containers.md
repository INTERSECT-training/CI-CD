---
title: "(Bonus) Implementing CI/CD for documentation using containers"
teaching: 10
exercises: 10
---

:::::::::::::::::::::::::::::::::::::: questions 

   - How to setup CI/CD for documentation using containers?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

  - Setup a CI and CD workflow for container images using GitHub Actions
  - Learn about the GitHub Container Image Registry

::::::::::::::::::::::::::::::::::::::::::::::::



Knowing about containers and how we want to create a container image,
we will dive into implementing CI/CD for our documentation using Docker.

We won't be running these commands locally so you do not need Docker installed.
We will just created the GitHub Actions YAML file
to let CI/CD run it instead.

If you do have docker installed, feel free to run these commands locally.
It is just not required.

# Setup CI for documentation

The main job we need for CI is to build the container image for the documentation.

We can accomplish this simply with Docker via:
```bash
docker build -t my-docs .
```

So let us add this to our CI YAML.
Luckily, Docker is already installed on the GitHub Actions runners!

If not already in the project directory, go ahead and get there.
We will make our feature branch:
```bash
cd intersect-training-cicd
git checkout main
git pull
git checkout -b add-docs-cicd
```

We will create a new `.github/workflows/docs-ci.yml` file in the project.

Open `.github/workflows/docs-ci.yml` with your favorite editor and add the following
```yaml
name: Documentation CI
on: push
jobs:
  build-docs-using-docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build docs via Docker
        run: docker build -t my-docs .
```

NOTE: Don't forget the `.` at the end of the `docker build -t my-docs .` command! That tells the build command which directory to use.

Let's push this to the repository and see what happens!
```bash
git add .github/workflows/docs-ci.yml
git commit -m "Adds job to build docs in CI via docker"
git push -u origin add-docs-cicd
```

![GitHub Actions docs CI](fig/docker-ci-docs.png){alt='Displays the output of a CI Github Action'}

So we are successfully building our container image in CI!

Now, we need to publish this image via CD.
This is equivalent to pushing the container image to a container image registry.

# GitHub Container Image Registry

We won't be using the [DockerHub][dockerhub] container image registry.

Instead, we will use GitHub's built-in [GitHub Container Image Regsitry][ghcr] (GHCR).
Similar to one reason why we choose GitHub Actions for CI/CD,
GitHub can integration with GHCR without requiring 3rd party credentials
to be shared, like we did with TestPyPi.

Also, it is free! DockerHub is as well, mostly.

[dockerhub]: https://hub.docker.com/
[ghcr]: https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry

# Setup CD for documentation

As before, we need the trigger setup for the publish / releases.

We can re-use what we previously had for the Python package CD trigger:
```yaml
on:
    workflow_dispatch:
    release:
      types:
        - published
```

For the jobs, we will do a build and push of the container image on Releases.

We will heavily rely on existing Actions in GitHub to accomplish this:
* `docker/setup-buildx-action`: Sets up Docker for us on the runner [Marketplace][setup-buildx-action]
* `docker/login-action`: Logs into container registries using runner credentials [Marketplace][login-action]
* `docker/build-push-action`: Builds and pushes image to container registry. [Marketplace][build-push-action]

[setup-buildx-action]: https://github.com/marketplace/actions/docker-setup-buildx
[login-action]: https://github.com/marketplace/actions/docker-setup-buildx
[build-push-action]: https://github.com/marketplace/actions/docker-build-push-action

Open `.github/workflows/docs-cd.yml` with your favorite editor and add the following:
```yaml
name: Documentation CD
on:
    workflow_dispatch:
    release:
      types:
        - published

jobs:
  publish-docs-using-docker:
    runs-on: ubuntu-latest
    permissions:
      packages: write
    steps:
      -
        name: Checkout
        uses: actions/checkout@v3

      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      -
        name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      -
        name: Build and push catalog image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true 
          platforms: linux/amd64,linux/arm64
          tags: ghcr.io/${{ github.repository }}:${{ github.ref_name }}
```

A few things to note:
* The `permissions:packages:write` is important to allow this job to write to [GitHub Packages][github-packages], which the container registry is part of.
* In `docker/login-action`, `github.actor` is a special variable GitHub provides for the runner to use as a "username". Similar to `__token__` usernames.
* In `docker/login-action`, `secrets.GITHUB_TOKEN` is a secret GitHub provides for the runner to log back into other GitHub services. Reference: [https://docs.github.com/en/actions/security-guides/automatic-token-authentication](https://docs.github.com/en/actions/security-guides/automatic-token-authentication)
* In `docker/build-push-action`, the `push: true` is what tells this action we want to push the image to the container image registry
* In `docker/build-push-action`, we are building for multiple hardware platforms via `platforms: linux/amd64,linux/arm64`.
* In `docker/build-push-action`, the `tags` is essentially the tag name others will use to "pull" the image from.
* We are using some other `github.*` variables in the `tags` to construct the image name in the container registry:
  - `github.repository`: Repository name (i.e. `marshallmcdonnell/intersect-training-cicd`)
  - `github.ref_name`: Short name of branch or tag, using it as a sort of "version number" (i.e. `add-docs-cd` or `v0.1.0`)

Let's push this to the repository and see what happens!
```bash
git add .github/workflows/docs-cd.yml
git commit -m "Adds CD for docs to container image registry"
git push -u origin add-docs-cicd
```

![GitHub Actions docs CD](fig/github-container-package-cd.png){alt='Displays the output of a Github Action'}

Looks like it passed!

And if we look at the "Packages" tab in GitHub, we should see our package!

![GitHub Actions docs package](fig/github-container-package.png){alt='Shows how to navigate to Action packages'}

Going into the package, we can see our Users are given instructions on how to get this container via `docker pull`

![GitHub Actions docs package](fig/github-container-package-page.png){alt='Displays an open action package'}

We can launch this container, mapping from the container port 80 (HTTP) to one on our host machine (8080), to spin up our docs server!
```bash
docker run -p 8080:80 <container image tag>
```

Then, navigate to `http://localhost:8080`.

Now we can allow operations or system administrators to serve our docs using containers!

# Side note about our "CD"

::::::::::::::::::::::::::::::::::::: challenge

## Action: Question about our CD

So we just setup CD for a documentation server right?

::::::::::::::::::::::::::::::::::::: solution 

## Solution

Not quite. We are doing CD for the "publshing" part.
 
Yet, CD is continuous *delivery* or *deployment*.
We really have not deployed our server.
Someone else still has to do this for us right now.
 
True CD for a pipeline like this would include deploying this onto the server as a website

:::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

# Wrap up

This wraps up CI/CD for containers.

Once you are happy, let's use our open pull request for this branch and get it merged back into `main`!

## Clean up local repository
To get your local repository up-to-date, you can run the following:
```
git checkout main
git pull
git remote prune origin # prune any branches deleted in the remote / GitHub that are still local
git branch -D add-docs-cicd # optional
```




::::::::::::::::::::::::::::::::::::: keypoints 

  - GitHub provides resources to publish container images

::::::::::::::::::::::::::::::::::::::::::::::::