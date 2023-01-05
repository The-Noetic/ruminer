---
date: 2023-01-03
comments: true
tags:
  - Programming
categories:
  - Programming

---

# Structuring a Python project

Starting a new python project entails deciding the organizational structure
of the project. By structure, I mean organizing the project in a way that
produces clean, efficient code and specifing the dependencies required to
reproduce the project.


In this post I outline a template for setting up new python project that
follows Python guidelines and allows you to produce clean code. Along
the way I also go into what are the essential components that go into
the template.

## Virtual environments

The first step when starting a new project
is to create a new virtual environment. The popular choices are
[conda](https://docs.conda.io/en/latest/), Pip + virtualenv and pipenv.
The community is split over which is the better tool.
As a Machine Learning developer I personally prefer conda.

??? note "Conda vs Pipenv"

    Conda works well for ML development. It's easy to setup environments
    with different Python versions and it resolves dependencies 
    automatically and efficiently.
    Pipenv on the other hand requires additional packages and more steps
    if you want to use a Python version other than the system default.
    Moreover it does not always automatically resolve dependencies
    frequently requiring manual interference.

    If you are interested to learn more read [this](http://technicaldiscovery.blogspot.com/2013/12/why-i-promote-conda.html)

The full conda install is huge (~3GB) and automatically installs over
1500 scientific computing packages. This is overkill for me so I
personally prefer Miniconda which is a lean version of conda.

### Installation

Download the script from Miniconda [website](https://docs.conda.io/en/latest/miniconda.html)
and run `bash <script-name>`. Windows and Mac users can download the
respective installer files available.

### Usage

There are only a handful of conda commands you need to know and these
are listed below:

- `conda create -n <env-name> Python=<version> <additional packages>` Creates a new virtualenv with the specified python version and installs additional packages if listed
- `conda env list` Lists all conda virtual environments
- `conda list` lists all packages installed in the active environment
- `conda activate <env-name>` activates the corresponding virtual environment
- `conda deactivate` deactivates the virtual env
- `conda install <package-name>` installs the package

Here's a [cheatsheet](chrome-extension://gfbliohnnapiefjpjlpjnehglfpaknnc/pages/pdf_viewer.html?r=https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf) for other commonly used conda commands.

## Dependency management

My choice for this is [Poetry](https://python-poetry.org/). Poetry is
reliable, fast and very easy to use and also seems to be [the go-to](https://piptrends.com/compare/poetry-vs-pipenv)
in the Python community. With inbuilt support for [Semantic Versioning](https://semver.org/)
Poetry makes it easy to build and publish packages.

__Installation:__ `conda install -c conda-forge poetry`

### Configuration

Poetry can be used to setup a new project directly by running
```bash
Poetry new <proj-name>
```
This pulls up an interactive prompt that steps you through the
creation process. 
For those who want to avoid this extra effort you can specify
the config in a `pyproject.toml` file. The getting started
[documentation](https://python-poetry.org/docs/basic-usage/) is
neat and easy to follow.

Below is my starting template for all my projects:
```toml
[tool.poetry]
name = "<proj-name>"
version = "0.1.0"
description = ""
authors = ["Rishikanth <r3chandr@ucsd.edu>"]
license = "MIT"
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.9"
click = "*"

[tool.poetry.group.dev.dependencies]
isort = "*"
black = "*"
flake8 = "*"
pytest = "*"
pre-commit = "*"
python-lsp-server = "*"
mkdocs-material = "*"
mkdocstrings = "*"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

[tool.poetry.scripts]
```

The fileds are self-explanatory. Poetry allows you to specify
dependencies in groups. The first group `tool.poetry.dependencies`
specifies the dependencies required to build and install the package.
The `tool.poetry.group.dev.dependencies` specifies the
dependencies required for development of the package. These
dependencies are not essential to use the package.

Once you have configured the `pyproject.toml` running
`Poetry install` will resolve all dependencies and install the
specified packages. It also creates a `poetry.lock` file
which carries the information to reproduce the environent.

!!! tip

    Ensure that you commit the poetry.lock file. When you want
    to update packages simply run `poetry update`. When recreating
    the environment running `poetry install` will refer to this
    lock file and install the exact versions of the packages
    used by your project.

The dev dependencies I use are described below in detail.
Since Poetry will handle the installation we do not need to 
manually install each of these packages. Simply specifiying them
in `pyproject.toml` is sufficient

## Linter
Code linters analyze code statically to flag programming errors,
catch bugs, stylistic errors, and suspicious constructs, using the
abstract syntax tree or AST.
You can expect a linter to warn you of functions whose complexity
needs to be reduced, syntax improvements, code practices that go
against configured or standard conventions, etc.
My choice of linter is [Flake8](https://flake8.pycqa.org/en/latest/).

## Auto-Formatter
![Code quality](/assets/images/code_quality.png)
Writing neat code can save you hours of frustration and your future
version will thank you for doing it. Fortunately there are tools
that can do this automatically allowing you to focus
on writing code rather than formatting. My opinionated choice of
tools are [Black](https://black.readthedocs.io/en/stable/) for formatting and [isort](https://pycqa.github.io/isort/)
for sorting imports.

## Documentation
Writing well documented code is crucial for your own sanity
as well as the potential user base. There are a variety of 
tools that create professional looking documentation with
minimal effort. I go with [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
which produces minimal and crisp looking documentation.
(This site was also built using the same). In addition to
this I also use the [mkdocstrings](https://mkdocstrings.github.io/)
packages to automatically generate documentation from docstrings.
I host my documentation on [GitHub Pages](https://pages.github.com/).

## Testing
Testing is an important part of development. The [pytest] framework is
the most popular tool for this purpose. This tool allows you to build
unit-tests and automatlly runs all test code. Although I have included
this in my configuration I seldom use it. My development
focuses on ML and I use [Click](https://click.palletsprojects.com/en/8.1.x/) 
to generate test binaries that I execute for testing my code.

??? note

    I have added `Click` to the general dependencies as I'll be using
    it to build CLI binaries that will be part of the project


## Automating the pipeline
Now that we have set up all the tools we need to produce clean code
we need a mechanism to automatically run these tools on our codebase.
Here enters [pre-commit](https://pre-commit.com/) a nifty little
tool that automates running the various checks (linter, formatter, tests).
pre-commit attaches a hook to git and automatically runs these checks
everytime you commit code.

`pre-commit` can be configured using a `.pre-commit-config.yaml` file.
Below is my config.

```yaml
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: check-ast
      - id: check-merge-conflict
      - id: check-case-conflict
      - id: check-docstring-first
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: mixed-line-ending
-   repo: local
    hooks:
      - id: isort
        name: isort
        entry: poetry run isort
        language: system
        types: [python]
      - id: black
        name: black
        entry: poetry run black
        language: system
        types: [python]
      - id: flake8
        name: flake8
        entry: poetry run flake8
        language: system
        types: [python]
```

To install the pre-commit hooks after setting up the config
run `pre-commit install`. From here on anytime you commit to
your git repository, pre-commit will automatically run the various checks.
