---
title: "Understanding Yet Another Markup Language"
teaching: 5
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is YAML?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn about YAML

::::::::::::::::::::::::::::::::::::::::::::::::



# YAML

YAML (Yet Another Markup Language or sometimes popularly referred to as YAML Ain't Markup Language (a recursive acronym)) is a human-readable data-serialization language. It is commonly used for configuration files and in applications where data is being stored or transmitted. CI systems' heavily rely on YAML for configuration. We'll cover, briefly, some of the native types involved and what the structure looks like.

::::::::::::::::::::::::::::::::::::: callout 

## Tabs or Spaces?

We strongly suggest you use spaces for a YAML document. Indentation is done with one or more spaces, however **two spaces** is the unofficial standard commonly used.

::::::::::::::::::::::::::::::::::::::::::::::::



## Scalars

```yaml
number-value: 42
floating-point-value: 3.141592
boolean-value: true # on, yes -- also work
# strings can be both 'single-quoted` and "double-quoted"
string-value: 'Bonjour'
unquoted-string: Hello World
hexadecimal: 0x12d4
scientific: 12.3015e+05
infinity: .inf
not-a-number: .NAN
null: ~
another-null: null
key with spaces: value
datetime: 2001-12-15T02:59:43.1Z
datetime_with_spaces: 2001-12-14 21:59:43.10 -5
date: 2023-07-14
```


::::::::::::::::::::::::::::::::::::: callout 

## Give your colons some breathing room

Notice that in the above list, all colons have a space afterwards, `: `. This is important for YAML parsing and is a common mistake.

::::::::::::::::::::::::::::::::::::::::::::::::

## Lists and Dictionaries

Elements of a list start with a "- " (a dash and a space) at the same indentation level.
```yaml
breeds:
  - Beagle
  - Corgi
  - German Shepherd
  - Poodle
```

Elements of a dictionary are in the form of "key: value" (the colon must followed by a space).
```yaml
madrigal:
  name: Bruno Madrigal
  voice-actor: John Leguizamo
  gift: sees the future
  allowed-to-talk-about-character: no no no
```

### Inline-Syntax

Since YAML is a superset of JSON, you can also write JSON-style maps and sequences.

```yaml
sisters: ["Julieta", "Pepa"]
parents: {father: Pedro, mother: Alma}
```

### Multiline Strings

In YAML, there are two different ways to handle multiline strings. This is useful, for example, when you have a long code block that you want to format in a pretty way, but don't want to impact the functionality of the underlying CI script. In these cases, multiline strings can help. For an interactive demonstration, you can visit [https://yaml-multiline.info/](https://yaml-multiline.info/).

Put simply, you have two operators you can use to determine whether to keep newlines (`|`, exactly how you wrote it) or to remove newlines (`>`, fold them in). Similarly, you can also choose whether you want a single newline at the end of the multiline string, multiple newlines at the end (`+`), or no newlines at the end (`-`). The below is a summary of some variations:

```yaml
folded_no_ending_newline:
  script:
    - >-
      echo "foo" &&
      echo "bar" &&
      echo "baz"


    - echo "do something else"

unfolded_ending_single_newline:
  script:
    - |
      echo "foo" && \
      echo "bar" && \
      echo "baz"


    - echo "do something else"
```

### Nested

```yaml
requests:
  # first item of `requests` list is just a string
  - http://example.com/

  # second item of `requests` list is a dictionary
  - url: http://example.com/
    method: GET
```

## Comments

Comments begin with a pound sign (`#`) and continue for the rest of the line:

```yaml
# This is a full line comment
foo: bar # this is a comment, too
```

## Anchors

YAML also has a handy feature called 'anchors', which let you easily duplicate content across your document. Anchors look like references `&` in C/C++ and named anchors can be dereferenced using `*`.

```yaml
anchored_content: &anchor_name This string will appear as the value of two keys.
other_anchor: *anchor_name

base: &base
  name: Everyone has same name

foo: &foo
  <<: *base
  age: 10

bar: &bar
  <<: *base
  age: 20
```

::::::::::::::::::::::::::::::::::::: callout 

The `<<` allows you to merge the items in a dereferenced anchor. Both `bar` and `foo` will have a `name` key.

::::::::::::::::::::::::::::::::::::::::::::::::

# What CI/CD tools use YAML

The following are just some of the CI/CD tools that use YAML:
- [Native GitHub Actions YAML Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Native GitLab CI/CD YAML Docs](https://docs.gitlab.com/ee/ci/yaml/)
- [Circle CI YAML Docs](https://circleci.com/docs/introduction-to-yaml-configurations/)
- [Bamboo YAML Docs](https://confluence.atlassian.com/bamboo/bamboo-yaml-938844479.html)
- [Travis CI YAML Docs](https://docs.travis-ci.com/user/customizing-the-build/)
- [CodeShip YAML Docs](https://docs.cloudbees.com/docs/cloudbees-codeship/latest/pro-builds-and-configuration/services)
- [CodeFresh YAML Docs](https://codefresh.io/docs/docs/pipelines/what-is-the-codefresh-yaml/)

Understanding YAML, like exit codes, is foundational and a general skill for almost all CI/CD tools.




::::::::::::::::::::::::::::::::::::: keypoints 

- YAML is a plain-text format, similar to JSON, useful for configuration
- YAML is used in many CI/CD tools and solutions

::::::::::::::::::::::::::::::::::::::::::::::::