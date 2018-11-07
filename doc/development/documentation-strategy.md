# Documentation Strategy

## Principles

* Keep documentation about the specifics of Software X (e.g. squid-py) in the same repository as the source code of Software X. That way, when changes are made to Software X, the corresponding documentation can be updated in the same pull request.
* Try to have all the docs about Software X in one directory (e.g. `docs/`) of the Software X repository, not scattered around, so it's easy to find.
* When writing documentation manually, use [GitHub-flavored Markdown](https://help.github.com/articles/about-writing-and-formatting-on-github/). In the future, we might add a requirement to add some simple YAML "front matter" at the top, such as:

```yaml
title: This is the Title in Title Case
slug: title-for-urls
---
Markdown content begins here.
```

* All documentation source files will be pulled from their source repositories into one online documentation site. Markdown and HTML can be used easily.
* It's _okay_ to auto-generate some documentation, such as API documentation. Just render it to Markdown or HTML _and include the rendered documentation in the repository_, so that it can be handled akin to manually-written documentation.
