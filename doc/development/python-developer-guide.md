# Python Developer Guide

The purpose of this guide is to document best practices, tips, and tricks for Python development.

  * [Templates](#templates)
  * [Publishing libraries to PyPI](#publishing-libraries-to-pypi)
    + [PyPI](#pypi)
    + [Project organization](#project-organization)
    + [Setup.py](#setuppy)
  * [Logging](#logging)
  * [Testing](#testing)
    + [Tox](#tox)
  * [Continuous Integration](#continuous-integration)
    + [Travis CI](#travis-ci)
    + [Bumpversion](#bumpversion)
    + [Testing within PyCharm](#testing-within-pycharm)
  * [Local package management and virtual environments](#local-package-management-and-virtual-environments)
    + [Using pip and virtualenv](#using-pip-and-virtualenv)
    + [Using pipenv](#using-pipenv)
    + [Using Conda](#using-conda)
  * [IDE's and editors](#ide-s-and-editors)
    + [Jupyter Lab](#jupyter-lab)
    + [Atom](#atom)
    + [Spyder](#spyder)
    + [Eclipse + PyDev](#eclipse---pydev)
    + [PyCharm](#pycharm)

## Templates

For scripts and packages ... 

TBD

## Publishing libraries to PyPI

The purpose of this section is provide a guide to follow when you want to configure a Python project and deployed to [PyPI](https://pypi.org/).  

### PyPI
The Python Package Index (PyPI) stores meta-data describing distributions packaged with distutils, as well as package data like distribution files if a package author wishes.
Once your package (and its associate meta-data) has been uploaded to PyPI, others can download and install it using pip.
Pypi is going to take the meta-data from your setup.py file.

If this is the first time this particular package is being uploaded to PyPI, you'll first need to register it:

```
python setup.py register
```
Next times the package is going to be uploaded when you create a new release in github automatically.


### Project organization
To get a better sense of how your files should be organized, here's a simplified structure of a Python project:  

```
name-of-your-project
├── docs
│   ├── imgs
│   └── doc.md
├── name_of_your_project
│   ├── __init__.py
│   └── code.py
├── tests
│   └── test_code.py
├── .gitignore
├── .travis.yml
├── HISTORY.md
├── LICENSE
├── Makefile
├── README.md
├── requirements_dev.txt
├── setup.cfg
├── setup.py
├── tox.ini
```

### Setup.py
Is the file that tells a distribution tool, such as Distutils or Setuptools, how to install and configure the package.  
Here you have an example of how an Ocean setup.py file looks like:
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""The setup script."""

from setuptools import setup, find_packages

with open('README.md') as readme_file:
    readme = readme_file.read()

with open('HISTORY.md') as history_file:
    history = history_file.read()

requirements = [YOUR_REQUIREMENTS]

setup_requirements = ['pytest-runner', ]

test_requirements = ['pytest', ]

setup(
    author="leucothia",
    author_email='devops@oceanprotocol.com',
    classifiers=[
        'Development Status :: 2 - Pre-Alpha',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: Apache Software License',
        'Natural Language :: English',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
    ],
    description=" 🐳 DESCRIPTION_OF_YOUR_PACKAGE",
    install_requires=requirements,
    license="Apache Software License 2.0",
    long_description=readme + + '\n\n' + history,
    long_description_content_type="text/markdown",
    include_package_data=True,
    keywords='REPO_NAME',
    name='REPO_NAME',
    packages=find_packages(include=['YOUR_PACKAGES']),
    setup_requires=setup_requirements,
    test_suite='tests',
    tests_require=test_requirements,
    url='https://github.com/oceanprotocol/REPO_NAME',
    version='0.0.1',
    zip_safe=False,
)

```

### Upload to PyPI

Ensure that `setuptools` is installed in the active environment.

Go to the root directory of your project (i.e. the one containing `setup.py`).

Build the package and the wheel:

```bash
python setup.py sdist bdist_wheel
```

Upload to PyPI:

```bash
twine upload dist/*
```

## Logging

Python `logging` should be employed generously. In the root calling
script, import the logging module and configure to the desired syntax and
handling;
```
from package_dir.log import setup_logging
import logging
setup_logging()
```

Where `setup_logging()` imports and applies the logging configuration from
a YAML configuration if specified, otherwise it defaults to `logging.basicConfig`.

Logging uses the `coloredlogs` package.

An example configuration file is presented below as a starting point;

Example 1) Simple logging to console;
```
version: 1

disable_existing_loggers: False
formatters:
    simple:
        format: "%(name)-5s - %(levelno)-3s - %(module)-20s  %(funcName)-30s: %(message)s"

handlers:
    console:
        class: logging.StreamHandler
        level: DEBUG
        formatter: simple
        stream: ext://sys.stdout

loggers:
    my_module:
        level: ERROR
        handlers: [console]
        propagate: no

root:
    level: DEBUG
    handlers: [console]
```

Note that the Python logging module organizes loggers in a hierarchy, all
loggers are descendants of root.


Example 2) A more complex example, with files and file size limits;

TODO: Update this section with the actual config used throughout Ocean

```yaml
version: 1
disable_existing_loggers: False
formatters:
    simple:
        format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"

handlers:
    console:
        class: logging.StreamHandler
        level: DEBUG
        formatter: simple
        stream: ext://sys.stdout

    info_file_handler:
        class: logging.handlers.RotatingFileHandler
        level: INFO
        formatter: simple
        filename: info.log
        maxBytes: 10485760 # 10MB
        backupCount: 20
        encoding: utf8

    error_file_handler:
        class: logging.handlers.RotatingFileHandler
        level: ERROR
        formatter: simple
        filename: errors.log
        maxBytes: 10485760 # 10MB
        backupCount: 20
        encoding: utf8

loggers:
    my_module:
        level: ERROR
        handlers: [console] # Ignore the file handlers until deployment
        propagate: no

root:
    level: INFO
    handlers: [console, info_file_handler, error_file_handler]
```

## Testing

### Tox
Tox is a tool that provides "standardized testing in Python",and it goes beyond merely running your tests with more than one version of the interpreter. It creates a fully sandboxed environment in which your package and its requirements are installed and tested. If you made a change that works fine when tested directly but the change inadvertently broke your installation, you'll discover that with tox.

## Continuous Integration

### Travis CI
TravisCI is a service that makes continuous integration for GitHub projects  easy. You need to create one simple file:


```
language: python
python:
  - 3.6
  - 3.5
  - 3.4

# Command to install dependencies, e.g. pip install -r requirements.txt --use-mirrors
install: pip install -U tox-travis
script: tox
deploy:
  provider: pypi
  distributions: sdist bdist_wheel
  user: leucothia
  password:
    secure: LEUCOTHIA_PASSWORD_ENCRYPTED
  on:
    tags: true
    branch: master
    repo: oceanprotocol/REPO_NAME
    python: 3.6

```

In order to generate the secure password, you have to run the following command in your project directory:

```
echo -n LEUCOTHIA_PASSWORD | travis encrypt --add deploy.password -r oceanprotocol/REPO_NAME
```

### Bumpversion

Related with the travis CI we are going to integrate Bumpversion to allow as 
to automatically point to the new version when we create a new realese.
[bumpversion](https://github.com/peritus/bumpversion)

### Testing within PyCharm

Create a testing run configuation using PyTest

Ensure correct target script and interpreter

Add the `--log-cli-level info` (or other logging level) argument to the run configuration, to post logging messages into the console.

## Local package management and virtual environments

### Using pip and virtualenv

[virtualenv](https://virtualenv.pypa.io/en/stable/) is used to create a local Python "virtual environment" which uses a particular version of Python and has a particular set of Python packages installed. You can switch between different virtualenvs. Example usage:

```bash
virtualenv -p $(which python3.6) py36  # Create a Python 3.6 virtualenv named py36
source py36/bin/activate  # Make the py36 virtualenv active
python --version  # Check the version of Python in the virtualenv
pip install blahblah  # Install the Python package named blahblah from PyPI
pip freeze  # List the Python packages installed in the virtualenv
deactivate  # Deactivate the current virtualenv (py36)
source other_env/bin/activate  # activate other_env
```

In many of our Python projects:

* `pip install -e .[dev]` is how a Python project developer would install the local Python project (in `.`) in editable mode (`-e`) including all the extra Python packages that a developer needs (`[dev]` which is defined in the `extras_require` dict of `setup.py`).
* `pip install -r requirements_dev.txt` or `pip install -r requirements.txt` will do `pip install -e .[dev]`
* `pip install package_name_on_PyPI` will install only the packages listed in the `install_requires` list in `setup.py`
* `pip install .` will install only the packages listed in the `install_requires` list in `setup.py` in the local directory (`.`) — this is what the Dockerfile does when building a new Docker image.

For more about `pip`, see the [pip User Guide](https://pip.pypa.io/en/stable/). For more about `setup.py`, see [the Python docs about the setup script](https://docs.python.org/3.6/distutils/setupscript.html).

### Using pipenv

The new kid on the block and the current PyPA recommended package manager. [Pipenv](https://github.com/pypa/pipenv) on GitHub.

### Using Conda

We don't currently use Conda but we might in the future.

## Project Directory Cleanup

After working with a Python project for a while, the project directory can get full of artifacts, and sometimes those can cause problems (e.g. conflicts). You can clean those out using:

```bash
# Remove build artifacts
rm -fr build/
rm -fr dist/
rm -fr .eggs/
find . -name '*.egg-info' -exec rm -fr {} +
find . -name '*.egg' -exec rm -f {} +

# Remove Python file artifacts
find . -name '*.pyc' -exec rm -f {} +
find . -name '*.pyo' -exec rm -f {} +
find . -name '*~' -exec rm -f {} +
find . -name '__pycache__' -exec rm -fr {} +

# Remove test and coverage artifacts
find . -name '.pytest_cache' -exec rm -fr {} +
rm -fr .tox/
rm -f .coverage
rm -fr htmlcov/
```

Tip: If you get a permissions error, then give yourself the needed permissions using `sudo chown -R $(whoami):$(whoami) .`

## IDE's and editors
### Jupyter Lab
`pip install ipykernel` in each environment to get kernel switching working!

### Atom

### Spyder
Install Spyder in each venv to ensure the kernel is used. 

### Eclipse + PyDev

### PyCharm
