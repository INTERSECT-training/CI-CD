---
title: Setup
---
## Set up Python

Part of this lesson consists in learning how to make scripts exit correctly. At some point, we will need to test exit codes with Pytest, Python testing tool.

To know whether your Python has `pytest`, just run `python -c "import pytest"`. If this command returns nothing, it means everything is fine. Otherwise, please visit [https://docs.pytest.org/en/stable/getting-started.html](https://docs.pytest.org/en/stable/getting-started.html) for installation.

## Set up Python project code


:::::::::::::::::::::::::::::::::::::: callout 

## Notice
We will be reusing the materials from the [INTERSECT packaging lesson](https://intersect-training.org/packaging) for our Python project code sample.

::::::::::::::::::::::::::::::::::::::::::::::::

- Create a new project from template [https://github.com/marshallmcdonnell/intersect-training-packaging-example](https://github.com/marshallmcdonnell/intersect-training-packaging-example) 
- Name it `intersect-training-cicd`.

:::::::::::::::::::::::::::::::::::::: callout 

![example of template]({{site.baseurl}}/fig/pick_template.png)
![example of filling out template]({{site.baseurl}}/fig/template_repository.png)

::::::::::::::::::::::::::::::::::::::::::::::::


If you're having issues, **please let us know immediately**
since you might not be able to follow this lesson without a proper setup.

[download-site]: https://github.com/INTERSECT-training/CI-CD/raw/main/downloads/intersect-training-cicd.zip

## Set up Docker (optional)

- Install Docker:  [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)
<!-- Mac OS:  [https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)-->
<!-- Windows: [https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/)-->

To check your installation open a terminal and run:
  ```bash
  docker --version
  ```
**Note** that you may have to run with *sudo*. If you don’t want to preface the *docker* command with *sudo*, you may need to checkout [https://docs.docker.com/engine/install/linux-postinstall/](https://docs.docker.com/engine/install/linux-postinstall/).

