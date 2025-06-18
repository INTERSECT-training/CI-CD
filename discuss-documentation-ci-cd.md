---
title: "(Bonus) Discuss CI/CD for Documentation"
teaching: 5
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

  - What is the documetation artifact and what does CI/CD look like for this?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

  - Figure out the CI/CD for documentation

::::::::::::::::::::::::::::::::::::::::::::::::




Next, we will discuss another artifact from software we can setup CI / CD for: container images!

# Documentation artifacts

In our example repository, we have our documentation source code in `docs/`.

Using [sphinx](), you can "build" the documentation.
Similar to our Python package, the output of the documentation build is an artifact.
Yet, it is not an installable package that we output.

::::::::::::::::::::::::::::::::::::: challenge 

# Action: What are the artifacts for documentation?

When you access documentation for some of your favorite softwares, what form is it in?
Which is your favorite form of documentation?

::::::::::::::::::::::::::::::::::::: solution 

## Examples

* Websites, like on [ReadTheDocs][rtd] (example: [NumPy docs][numpy])
* PDF files (example: [RMCProfile v6.7.4 docs][rmcprofile])
* API documentation like [Swagger / OpenAPI][openapi] for Software-as-a-Service (example: [Arcsecond Swagger][arcsecond] or [GitHub REST API docs][github])

::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

[sphinx]: https://www.sphinx-doc.org
[numpy]: https://numpy.readthedocs.io/en/latest/
[rmcprofile]: http://rmcprofile.org/images_rmcprofile/6/6c/Rmcprofile_v6.7.4_manual.pdf
[arcsecond]: https://api.arcsecond.io/schema/swagger/#/
[github]: https://docs.github.com/en/rest?apiVersion=2022-11-28

## What do we want from CI/CD for Sphinx documentation?

Sphinx will allow for multiple output formats (i.e. PDF or website).

Arguably, the website format is the favorite.
For this, Sphinx can generate HTML files that can be used to create a [static webpage][static-page]

As we discussed in the first episode, we can use CI / CD for all sorts of stuff.
We just need to ask the questions:
* _"What workflow am I trying to automate?"_
* For CI: _"What does 'breaking changes' mean for my workflow?"_
* For CD: _"What does deployment / delivery / release mean for my workflow?_


::::::::::::::::::::::::::::::::::::: challenge 

## Action: Answer these questions for our documentation workflow

Considering we use sphinx to create files and want a documentation webpage, what are your answers to the questions above?

* For CI, what will we automate and check for documentation?
* For CD, what will we be deploying for documentation?

::::::::::::::::::::::::::::::::::::: solution 

## Solution
* For CI: we want to automate building the documetation HTML files
* For CD: we want to automatically publish the artifact for the static webpage and, eventually, deploy it onto a web server.

::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

For documentation CI/CD, we do need to think about the end goal.

Basically, if we aren't using services like [ReadTheDocs][rtd],
we need to run a web site on a web server.

So the HTML files _could_ be considered the final artifact.
Yet, there is a "nicer" solution for software artifacts that have gained immense popularity do to their ease of deployment and reproducibility!

To be revealed in the next episode...



::::::::::::::::::::::::::::::::::::: keypoints 

  - Documentation and website artifacts are different than package artifacts

::::::::::::::::::::::::::::::::::::::::::::::::