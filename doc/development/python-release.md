# Python Release Process

This document describes the general way in which Python packages are released. 

## Prerequistes: 

- Optionally create a new virtual environment for the `bumpversion`  package, and other release tools
- Install the bumpversion package , `pip install bumpversion`
- Following git-flow process
- Running Travis in the repo, i.e. `.travis.yml` exists and has the `deploy` section defined, (see below)
- bumpversion.cfg exists, example below

```yaml
[bumpversion]
current_version = 0.2.16
commit = True
tag = True

[bumpversion:file:setup.py]
search = version='{current_version}'
replace = version='{new_version}'

[bumpversion:file:squid_py/__init__.py]
search = __version__ = '{current_version}'
replace = __version__ = '{new_version}'

[bumpversion:file:docs/source/conf.py]
search = {current_version}
replace = {new_version}
```



## Procedure

- Create a new local feature branch, e.g. `git checkout -b feature/bumpversion-to-v0.2.5`
- Use the `bumpversion` to bump the project version. You can execute the script using {major|minor|patch} as first argument to bump the version accordingly:
  - To bump the patch version: `bumpversion patch`
  - To bump the minor version: `bumpversion minor`
  - To bump the major version: `bumpversion major`
- Commit the changes to the feature branch.
- Push this feature branch to GitHub.
- Make a pull request from the just-pushed branch.
- Wait for all the tests to pass!
- Merge the pull request into the `develop` branch.
- To make a GitHub release (which creates a Git tag):
  - Go to the repository Releases section in GitHub page
  - Click "Draft a new release".
  - For tag version, put something like `v0.2.5`
  - For release title, put the same value (like `v0.2.5`).
  - For the target, select the `develop` branch, or the just-merged commit.
  - Describe the main changes. (In the future, these will come from the changelog.)
  - Click "Publish release".
- Travis will detect the release (a new tag) and run the deployment section of `.travis.yml`, i.e.

  ```yaml
  deploy:
  provider: pypi
  distributions: sdist bdist_wheel
  user: ${PYPI_USER}
  password: ${PYPI_PASSWORD}
  on:
    tags: true
    repo: oceanprotocol/squid-py
    python: 3.6
  ```

- Go to Travis and check the Travis job. It should deploy a new release to PyPI.
- Check PyPI for the new release
