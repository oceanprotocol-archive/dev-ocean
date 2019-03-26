# Third-Party Package Licensing Policy

All Ocean Protocol software is licensed under an Apache License, Version 2.0.

Therefore, when developing Ocean Protocol software, developers must be mindful of the licenses on the third-party packages they use/import.

## Licenses that are NOT Allowed

- Any GPL license
- Any AGPL license
- MPL 1.0 (but MPL 2.0 is allowed)
- Any strongly-protective "copyleft" license

## Allowed Licenses

- Public Domain (not really a license but that's irrelevant here)
- "Permissive" licenses
- MIT
- X11
- BSD (any kind)
- Apache 2.0
- ISC
- MPL 2.0 - To understand why, see [Q8 in the MPL 2.0 FAQ](https://www.mozilla.org/en-US/MPL/2.0/FAQ/)
- LGPL - To understand why, see [kennethreitz/requests/issues/3389](https://github.com/kennethreitz/requests/issues/3389)

Note: The above list assumes we include a "Notice" listing all the third-party packages along with information about each such as the license, a link to the license text, and a link to the source code. We _don't_ currently do that but we plan to do that in the future.

## Tools for Checking the Licenses of All Dependencies

### JavaScript

Use [license-checker](https://www.npmjs.com/package/license-checker). (Click that link then follow the instructions on the destination page.)

### Python

Use [pip-licenses](https://pypi.org/project/pip-licenses/) inside a clean virtualenv, i.e. go into the root directory of the repository and then:

```bash
virtualenv -p $(which python3) py3
. py3/bin/activate
pip install .  # i.e. only install production packages
pip install pip-licenses
pip-licenses
```

### Java

Maven has a built-in tool. Go into the root directory of the repository and then:

```bash
mvn clean
mvn license:third-party-report
```

then look in `target/site/third-party-report.html` with your web browser.

### Other Tools

[ScanCode Toolkit](https://github.com/nexB/scancode-toolkit) is open source and thorough but can take a long time to scan a project.

There are businesses that offer automated license auditing and license compliance as a service (integrated with CI/CD), but they tend to be quite expensive. Examples include FOSSA and Whitesource.
