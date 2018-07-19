# Python Developer Guide

The purpose of this guide is to document best practices, tips, and tricks for Python development. 

- [Publishing libraries to PyPI](#publishing-libraries-to-pypi)
  * [PyPI](#pypi)
  * [Project organization](#project-organization)
  * [Setup.py](#setuppy)
- [Testing](#testing)
  * [Tox](#tox)
- [Continuous Integration](#continuous-integration)
  * [Travis CI](#travis-ci)
- [Local package management and virtual environments](#local-package-management-and-virtual-environments)
  * [Anaconda](#anaconda)
  * [pyenv](#pyenv)
  * [pipenv](#pipenv)
- [IDE's and editors](#ide-s-and-editors)
  * [Jupyter Lab](#jupyter-lab)
  * [Atom](#atom)
  * [Spyder](#spyder)
  * [Eclipse + PyDev](#eclipse---pydev)

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

###Bumpversion

Related with the travis CI we are going to integrate Bumpversion to allow as 
to automatically point to the new version when we create a new realese.
[bumpversion](https://github.com/peritus/bumpversion)


## Local package management and virtual environments
### Anaconda
Prefer for handling packages and environments. More capable than pip + virtualenv.

Can use `conda` with `pip` if a package is not on the main channel, but check
[conda-forge](https://anaconda.org/conda-forge) as well, many packages have been
migrated by the community.

### pyenv

### pipenv
The new kid on the block. [Pipenv](https://github.com/pypa/pipenv) on GitHub.

## IDE's and editors
### Jupyter Lab
`pip install ipykernel` in each environment to get kernel switching working!

### Atom

### Spyder
Install Spyder in each venv to ensure the kernel is used. 

### Eclipse + PyDev

### PyCharm
