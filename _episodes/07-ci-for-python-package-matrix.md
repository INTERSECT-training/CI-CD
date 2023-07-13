---
title: "Matrix"
teaching: 5 
exercises: 10
questions:
  - How do I run CI for multiple Python versions and /or different platforms?
objectives:
  - Don't Repeat Yourself (DRY)
  - Use a single job for multiple jobs
keypoints:
  - Matrix can help DRY your CI for multi-version and cross-platform testing
  - Using `matrix` allows to test the code against a combination of versions.
---

Let's assume our tests are running great.

Yet, the domain scientist reports they are getting errors when they run the Python package.

After talking with them, you find that they are running Python 3.11, not 3.10.

How do we include this version of Python in our testing?

# Multiple version Python testing - Naive Approach

One approach would be to add another job to run tests for Python 3.11 as well.

Up to this point, we currently have the following:
~~~
name: example
on: push
jobs:
  greeting:
    runs-on: ubuntu-latest
    steps:
      -run: echo hello world

  test-python-3-10:
    name: Check Python 3.10 on Ubuntu
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-python@v4
        with:
          python-version: "3.10"

      - name: Install package
        run: python -m pip install -e .[test]

      - name: Test package
        run: python -m pytest
~~~
{: .language-yaml}

Let's add testing a different version of Python in our CI!

> ## Action: Add Python 3.11 test
> First, remove the `greeting` job.
>
> Then, add a new job called `test-python-3-11`.
>
> Have this new job:
> * Checkout the code
> * Setup a Python 3.11 environment
> * Install the package with our test dependencies
> * Run the tests via `pytest`
> > ## Solution
> > ~~~
> > name: example
> > on: push
> > jobs:
> > 
> >   test-python-3-10:
> >     name: Check Python 3.10 on Ubuntu
> >     runs-on: ubuntu-latest
> >     steps:
> >       - uses: actions/checkout@v3
> > 
> >       - uses: actions/setup-python@v4
> >         with:
> >           python-version: "3.10"
> > 
> >       - name: Install package
> >         run: python -m pip install -e .[test]
> > 
> >       - name: Test package
> >         run: python -m pytest
> >
> >   test-python-3-11:
> >     name: Check Python 3.11 on Ubuntu
> >     runs-on: ubuntu-latest
> >     steps:
> >       - uses: actions/checkout@v3
> > 
> >       - uses: actions/setup-python@v4
> >         with:
> >           python-version: "3.11"
> > 
> >       - name: Install package
> >         run: python -m pip install -e .[test]
> > 
> >       - name: Test package
> >         run: python -m pytest
> > ~~~
> > {: .language-yaml}
> {: .solution}
{: .challenge}

Now, let's add this so we can catch any Python 3.11 bugs going forward!

```bash
git checkout -b add-ci-test-py311
git add .github/workflows/main.yml
git commit -m "Adds job to CI for Python 3.11"
git push -u origin add-ci-test-py311
```

Check the output!

Now, we do technically have multiple-versions of Python supported.

Yet, we just added 13 lines of identical code with only one character change...

## DRY your CI

"DRY" is an ancronym for "Don't Repeat Yourself",
meaning don't have repeated code in your software.
This reduces repetition and avoids redundancy.

We do this for our software.
There is no reason we should not apply this principle to CI!

# Matrix

> ## Action: Building a matrix across different versions
>
> We could do better using `matrix`. The latter allows us to test the code against a combination of versions in a single job.
>
> ~~~
> name: example
> on: push
> jobs:
> 
>   test-python-3-10:
>     name: Check Python ${{ matrix.python-version }} on Ubuntu
>     runs-on: ubuntu-latest
>     strategy:
>       matrix:
>         python-version: ["3.10", "3.11"]
>     steps:
>       - uses: actions/checkout@v3
> 
>       - name: Setup Python ${{ matrix.version }}
>         uses: actions/setup-python@v4
>         with:
>           python-version: ${{ matrix.version }}
> 
>       - name: Install package
>         run: python -m pip install -e .[test]
>
>       - name: Test package
>         run: python -m pytest
> ~~~
> {: .language-yaml}
> 
> YAML truncates trailing zeroes from a floating point number, which means that `version: [3.9, 3.10, 3.11]` will automatically
> be converted to `version: [3.9, 3.1, 3.11]` (notice `3.1` instead of `3.10`). The conversion will lead to unexpected failures
> as your CI will be running on a version not specified by you. This behavior resulted in several failed jobs after the release
> of Python 3.10 on GitHub Actions. The conversion (and the build failure) can be avoided by converting the floating point number
> to strings - `version: ['3.9', '3.10', '3.11']`.
>
> More details on matrix: [https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstrategymatrix).
{: .callout}

Let's update our `.github/workflow/main.yml` and use `matrix`.

We can push the changes to GitHub and see how it will look like.
~~~
git add .github/workflows/main.yml
git commit -m "Adds multi-version Python testing to CI via matrix"
git push -u origin add-ci
~~~
{: .language-bash}

That is a much better way to add new versions and we have a DRY-compliant CI!

## Test experimental versions in CI

Sometimes in your matrix, you want to push the boundaries of your testing.
An example would be to test up to the latest alpha / beta release of a Python version.
You do not want a failure in such cutting-edge, unstable tests stopping your CI.

At time of writing, the latest supported beta version of Python for the `actions/python-versions` is 3.12.0-beta.4 based on their [GitHub Releases](https://github.com/actions/python-versions/releases)

Let's add this to our python version testing.

### Allow experimental jobs to fail in CI

This gets a little complicated, but there are a few flags we need to set.

For the matrix case, Github Actions fails the entire workflow and stops all the running jobs if any of the jobs in the matrix fails. This can be prevented by using `fail-fast: false` key:value.

GitHub Actions will cancel all in-progress and queued jobs in the matrix if any job in the matrix fails.

To disable this, we need to disable `fail-fast` on the matrix strategy.
~~~
strategy:
  fail-fast: false
~~~
{: .language-yaml}

That should do it, right? Nope...

We have to add `continue-on-error: true` to a single job that we allow to fail.
If enabled, jobs in the matrix continue to run if there is an error.
By default `continue-on-error: false` and will also cancel the matrix jobs if the
"experimental" job fails.

~~~
runs-on: ubuntu-latest
continue-on-error: true
~~~
{: .language-yaml}

More details: [https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategyfail-fast](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategyfail-fast)

Finally, we need to add this experimental job to the matrix
but also flag it as allowed to fail somehow.

For this, we use `include` to add a single extra job to the matrix with "metadata".
~~~
strategy:
  matrix:
    - python-versions: "3.12.0-beta.4"
      allow_failure: true
~~~
{: .language-yaml }

This will add the string `"3.12.0-beta.4"` to the `python-versions`
list in matrix with the arbitrary variable `allow_failure` set to `true`
as "metadata".
We also must add the `allow_failure` "metadata" to the other members of the matrix (see below).

More detail: [https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategymatrixinclude](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategymatrixinclude)

So if we **only** want to allow the job with version set to `3.12.0-beta.4` to fail without failing the workflow run, we need something like:

Then the following would work for a job:
~~~
jobs:
  job:
    runs-on: ubuntu-latest
    continue-on-error: {% raw %}${{ matrix.allow_failure }}{% endraw %}
    strategy:
      fail-fast: true
      matrix:
        python-versions: ["3.10", "3.11"]
        allow_failure: [false]
      include:
        - python-versions: "3.12.0-beta.4" 
          allow_failure: true
~~~
{: .language-yaml}

> ## Action: Add experimental job that is allowed to fail in the matrix
>
> Add "3.12.0-beta.4" to our current Python package CI YAML
> via the matrix but allow it to fail.
>
> > ## Solution
> > ~~~
> > name: example
> > on: push
> > jobs:
> > 
> >   test-python-3-10:
> >     name: Check Python ${{ matrix.python-version }} on Ubuntu
> >     runs-on: ubuntu-latest
> >     continue-on-error: {% raw %}${{ matrix.allow_failure }}{% endraw %}
> >     strategy:
> >       fail-fast: true
> >       matrix:
> >         python-version: ["3.10", "3.11"]
> >         allow_failure: [false]
> >       include:
> >         - python-versions: "3.12.0-beta.4" 
> >           allow_failure: true
> >
> >     steps:
> >       - uses: actions/checkout@v3
> > 
> >       - name: Setup Python ${{ matrix.version }}
> >         uses: actions/setup-python@v4
> >         with:
> >           python-version: ${{ matrix.version }}
> > 
> >       - name: Install package
> >         run: python -m pip install -e .[test]
> >
> >       - name: Test package
> >         run: python -m pytest
> > ~~~
> > {: .language-yaml}
> {: .solution}
{: .challenge}

Let's commit this and see how it looks!

```bash
git add .github/workflows/main.yml
git commit -m "Adds experimental Python 3.12.0-beta.4 test to CI"
git push -u origin add-ci
```

{% include links.md %}