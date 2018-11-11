# Documentation Strategy

## Principles

* Keep documentation about the specifics of Software X (e.g. squid-py) in the same repository as the source code of Software X. That way, when changes are made to Software X, the corresponding documentation can be updated in the same pull request.
* Try to have all the docs about Software X in one directory (e.g. `docs/`) of the Software X repository, not scattered around, so it's easy to find.
* When writing documentation manually, use [GitHub-flavored Markdown](https://help.github.com/articles/about-writing-and-formatting-on-github/).
* Some docs files (Markdown files) will be used as the source for pages in the public-facing docs at docs.oceanprotocol.com. Those docs files have a few extra content requirements, listed in [the main README.md file in the oceanprotocol/docs repository](https://github.com/oceanprotocol/docs/blob/master/README.md).
* It's okay to auto-generate some documentation, such as API documentation. Just render it to Markdown or HTML _and include the rendered documentation in the repository_, so that it can be handled akin to manually-written documentation. We'll figure out how to handle auto-generated docs on a case-by-case basis.
