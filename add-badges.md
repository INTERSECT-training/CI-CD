---
title: "Badge for CI/CD"
teaching: 5
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

   - How to add something that communicates / visualizes the status of CI/CD?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

  - Add a badge to project for CI/CD status


::::::::::::::::::::::::::::::::::::::::::::::::



# Badges

As you run CI, you do see the current status of CI/CD for feature branches in pull requests.

You can also check the status via navigating to the Actions tab in GitHub.

Yet, for the `main` branch or other "important" branches for your team,
wouldn't you like to bring more visibility to the status of CI?

Like a monitor to tell you if it is broken or not? (maybe yes, maybe no...)

If you do, badges are a very easy way to communicate things about your project to your team and others and can easily be put in your markdown README files!

# Badges

Badges are not specific to CI/CD.

You can create ones for which Python versions you use, what license you have, or what the current version is of your released software.

[Shields.io][shields] is a great resource to create all kinds of badges that you can then add to your README or other markdown.

Yet, for CI/CD, GitHub Actions already provides this badge for you, you just need to know what to add to your README.

The [GitHub Actions badges][badge-docs] has the following template to create badges in your README markdown files! 
Just replace:
* OWNER
* REPOSITORY
* YAML

```bash
![example workflow](https://github.com/OWNER/REPOSITORY/actions/workflows/YAML/badge.svg)
```

Example:
```bash
![example workflow](https://github.com/marshallmcdonnell/intersect-training-cicd/actions/workflows/main.yaml/badge.svg)
```

Which will give you something like:
![badge](../fig/github-actions-badge.png)

[shields]: https://shields.io/
[badge-docs]: https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge

If you like, feel free to add one to your repository.




::::::::::::::::::::::::::::::::::::: keypoints 

  - Badges are fun! 


::::::::::::::::::::::::::::::::::::::::::::::::