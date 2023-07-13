---
title: "CD for Python Package"
teaching: 15
exercises: 15
questions:
  - How do I setup CD for a Python package in GitHub Actions?
objectives:
  - Learn about building package using GitHub Actions 
  - Learn what is required to publish to PyPi
keypoints:
  - GitHub Actions can also help you deploy your package to a registry for distribution.
---

We have CI setup to perform our automated checks.

We have continued to add new features and now, we want to get these new features to our Users.

Just as we have automated code checks, we can automated deployment of our package to a package registry upon a release trigger.

# Setup CD for Releases

We previously created our CI workflow YAML file.

We _could_ keep using that one, possibly.

However, we previously were triggering on every single `push` event
(i.e. anytime we uploaded any commits to any branch!)

## Trigger for CD

Do we want to publish our Python Package for any pushed code? Definitely not!

We want to instead pick the trigger event
and create a new GitHub Actions YAML just for our CD workflow.

There are different triggers we can use:
* any tags pushed to the repository
~~~
on:
    push:
      tags:
        - '*'
~~~
{: .language-yaml }

* tags that match semantic version formatting (below is a [regular expression](https://regexlearn.com/learn/regex101) used to match a pattern of text)
~~~
on:
    push:
      tags: 'v[0-9]+.[0-9]+.[0-9]+-*'
~~~
{: .language-yaml }

* manually triggered from UI and / or on GitHub Releases
~~~
on:
    workflow_dispatch:
    release:
      types:
        - published
~~~
{: .language-yaml }

Let's continue using the "releases" one.

The first, [`workflow_dispatch`][workflow-dispatch], allows you to manually trigger the workflow from the GitHub web UI for testing.

The second, [`release`][release-action], will trigger whenever you make a [GitHub Release][release-github].

Now that we have our triggers, we need to do something with them.

We want to push our Python Package to a package registry.

But first, we must build our distrbution to upload!

## Build job for CD

Let's start on our new CD YAML for releases.

Write the following in `.github/workflows/releases.yaml`
~~~
name: Releases

on:
    workflow_dispatch:
    release:
      types:
        - published

jobs:
  dist:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Build SDist & wheel
      run: pipx run build

    - uses: actions/upload-artifact@v3
      with:
        name: build-artifact
        path: dist/*
~~~
{: .language-yaml }

Here we have a job called "dist" that:
* Checks out the repository using the `actions/checkout` Action again.
* Uses [pipx][pipx] to run the build of the source (SDist) and build (wheels) distrubtions. This produces the wheel and sdist in `./dist` directory.
* Use a new Action, called `actions/upload-artifact` ([Markplace page](https://github.com/marketplace/actions/upload-a-build-artifact))

From the `actions/upload-artifact` [Markplace page](https://github.com/marketplace/actions/upload-a-build-artifact), we can see this saves artifacts between jobs by uploading and storing them in GitHub.

Specifically, we are uploading and storing the artifacts from the `./dist` directory in the previous step and naming that artiface "build-artifact". We will see, this is how we can pass the artifacts to another job or download ourselves from the GitHub UI.

This gives us our first step in the CD process, building the artifacts for a software release!

## Test using build artifacts in another job

Now, we want to take the artifact in the `dist` job and use it in our next phase.

To do so, we need to use the complimentary Action to `actions/upload-artifact, which is `action/download-artifact`.

From the [Marketplace page](https://github.com/marketplace/actions/download-a-build-artifact), we can see this simply downloads the artifact that is stored using the `path`.

Let's add a "test" `publish` job.

~~~
name: Releases

on:
    workflow_dispatch:
    release:
      types:
        - published

jobs:
  dist:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build SDist & wheel
        run: pipx run build

      - uses: actions/upload-artifact@v3
        with:
          name: "build-artifact"
          path: dist/*

  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact
        with:
          name: "build-artifact"
          path: dist

      - name: Publish release
        run: echo "Uploading!"
~~~
{: .language-yaml }

> ## Action: Test out the publish YAML
>
> Using the above test YAML, perform a release so the CD pipeline ran.
>
> Does the pipeline run successfully?
> > ## Solution
> > No. 
> {: .solution }
{: .challenge }


{: .language-yaml }
[workflow-dispatch]: https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch
[release-action]: https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#release
[release-github]: https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release
[pipx]: https://pypa.github.io/pipx/

{% include links.md %}