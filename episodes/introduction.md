---
title: "Introduction"
teaching: 5
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is continuous integration / continuous deployment?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand why CI/CD is important
- Learn what can be possible with CI/CD
- Find resources to explore in more depth

::::::::::::::::::::::::::::::::::::::::::::::::



# What is CI/CD?

Continuous Integration (CI) is the concept of continuously integrating all code changes. So every time a contributor (student, colleague, random bystander) provides new changes to your codebase, those changes can trigger builds, tests, or other code checks to make sure the changes don't "break" anything. As your team of developers grow and your community of contributors grow, CI becomes your best friend to ensure quality of code!

CD can actually mean two different terms. First there is Continuous Deployment (CD) and then their is Continuous Delivery (also CD). Most people use these interchangably but there is a subtle difference.

Both CD methods are similar in that they are the literal continuous deployment of code changes. If CI is the automation of verifying code changes via testing, formatting, etc., then CD is  automation of  deploying / delivering those changes to your community.  

The differences are that after CI passes, Continous Delivery will stage a deployment but wait for a manual action to perform the actual deployment. Continous Deployment is if CI passes, there is no manual intervention. The deployment will automatically continue.

![Figure 1. Differences between continous delivery vs. continous deployment and their relationship to continous integration. Image retrieved from: [HERE](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment)](fig/atlassian_continous_delivery_vs_continuous_deployment.png){alt='Differences between continous delivery vs. continous deployment and their relationship to continous integration.'}

# Breaking Changes

What does it even mean to “break” something? The idea of “breaking” something is pretty contextual. If you’re working on C++ code, then you probably want to make sure things compile and run without segfaulting at the bare minimum. If it’s python code, maybe you have some tests with pytest that you want to make sure pass (“exit successfully”). Or if you’re working on a paper draft, you might check for grammar, misspellings, and that the document compiles from LaTeX. Whatever the use-case is, integration is about catching breaking changes.

## Deployment

Similarly, "deployment" can mean a lot of things. Perhaps you have a Curriculum Vitae (CV) that is automatically built from LaTeX and uploaded to your website. Another case is to release docker images of your framework that others depend on. Maybe it's just uploading documentation. Or to even upload a new tag of your python package on `pypi`. Whatever the use-case is, deployment is about **releasing** changes.

## Workflow Automation

CI/CD is the first step to automating your entire workflow. Imagine everything you do in order to run an analysis, or make some changes. Can you make a computer do it automatically? If so, do it! The less human work you do, the less risk of making human mistakes.


::::::::::::::::::::::::::::::::::::: callout 

## Anything you can do, a computer can do better

Any command you run on your computer can be equivalently run in a CI job.

::::::::::::::::::::::::::::::::::::::::::::::::

Don't just limit yourself to thinking of CI/CD as primarily for testing changes, but as one part of automating an entire development cycle. You can trigger notifications to your cellphone, fetch/download new data, execute cron jobs, and so much more. However, for this lesson, you'll be going through what you have recently learned about for python testing with `pytest` in the [INTERSECT testing lesson](https://intersect-training.org/testing-lesson/) and python packaging in the [INTERSECT packaging lesson](https://intersect-training.org/packaging).

# CI/CD Tools and Solutions

When it comes to picking a CI/CD solution, there are plenty to pick from.
Below is a list of a few of these options:

- [Native GitHub Actions](https://github.com/features/actions)
- [Native GitLab CI/CD](https://docs.gitlab.com/ee/ci/)
- [Circle CI](https://circleci.com/)
- [Jenkins](https://jenkins.io/)
- [TeamCity](https://www.jetbrains.com/teamcity/)
- [Bamboo](https://www.atlassian.com/software/bamboo)
- [Travis CI](https://travis-ci.org/)
- [Buddy](https://buddy.works/)
- [CodeShip](https://codeship.com/)
- [CodeFresh](https://g.codefresh.io/)

For an even more comprehensive list, the GitHub repository [ligurio/awesome-ci](https://github.com/ligurio/awesome-ci) is a great reference!

For today's lesson, we'll only focus on GitHub's solution (GitHub Actions). However, be aware that all the concepts you'll be taught today: including pipelines, jobs, artifacts; all exist in other solutions by similar/different names. For example, GitLab supports two features known as caching and artifacts; but Travis doesn't quite implement the same thing for caching and has no native support for artifacts. Therefore, while we don't discourage you from trying out other solutions, there's no "one size fits all" when designing your own CI/CD workflow.

*Why are we picking GitHub Actions?!?*

A few quick reasons that this was select is:
  * If you already use GitHub for version control, you do not have to have a 3rd party integration for your CI/CD solution; this usually requires storing secrets between GitHub and CI/CD solution, which can be problematic if security breaches happen (example of such an event [here](https://github.blog/2022-04-15-security-alert-stolen-oauth-user-tokens/))
  * GitHub Actions is completely free for open source and free with limits for private repos! 
  * Servers / runners are provided for multiple operating systems.
  * US-RSE uses GitHub Actions on projects like the [website](https://github.com/USRSE/usrse.github.io)! So you will learn about one of the CI solutions used by the association! 




::::::::::::::::::::::::::::::::::::: keypoints 

- CI/CD is crucial for any reproducibility and testing
- Take advantage of automation to reduce your workload

::::::::::::::::::::::::::::::::::::::::::::::::