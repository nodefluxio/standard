# Python

## Language Rules

### Dependency Management

> Inside the requirements.txt

```shell
tensorflow-gpu==1.4.1
-e git+https://bitbucket.org/verysmartdonkey/tf-object-detector#egg=tfdetector
```

> This way we can install all dependencies with

```shell
pip install -r requirements.txt
```

### Setup Script

> Setup script example.

```python
from setuptools import setup

setup(
  name='nflogger',
  version='0.1.1',
  description='Nodeflux Standard Logger',
  url='https://bitbucket.org/verysmartdonkey/nodeflux-logger-python',
  author='Ilham Imaduddin',
  author_email='ilham@nodeflux.io',
  packages=['nflogger'],
  zip_safe=False)
```

> With this build script, we can easily install the library with `pip`.

```shell
# From local repository
pip install .

# From remote repository
pip install -e git+https://bitbucket.org/verysmartdonkey/nodeflux-logger-python#egg=nflogger
```

When we create a package, we're not only responsible to make the package works, but we also responsible to make the installation process as easy as possible.

The most common way to install python package is `pip`. To make `pip` works, we need to make the setup script in the `setup.py` file. See the details in Python documentation [Writing the Setup Script](https://docs.python.org/3.6/distutils/setupscript.html).

After the setup script is ready, we can install the package with `pip install .` (or `pip install -e .` to make it editable while developing the package).

You **must** to create a setup script for every package that designed to be reusable. An installation with `pip install` **should** installs all package components, including dependencies, extensions and package data.

### Lint

> Run pylint againts your code.

```shell
pylint ./tests
```

[Pylint](https://pylint.readthedocs.io/en/latest/) is a tool for finding bugs and style problems in Python source code. It finds problems that are typically caught by a compiler for less dynamic languages like C and C++. Because of the dynamic nature of Python, some warnings may be incorrect; however, spurious warnings should be fairly infrequent.

You **must** use `pylint` to analize your code.

To make the most of pylint, use an extension that supports pylint on your text editor/IDE:

- Visual Studio Code: [Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
- Sublime Text: [Anaconda](https://packagecontrol.io/packages/Anaconda)

#### Pros

Catches easy-to-miss errors like typos, using-vars-before-assignment, etc.

#### Cons

pylint isn't perfect. To take advantage of it, we'll need to sometimes: a) Write around it b) Suppress its warnings or c) Improve it.

### Formatting

### Compatibility

> An example of `__future__` import to improve Python 2 and 3 compatibility.

```python
from __future__ import print_function

print('Hello, World')
```

> This works on both Python 2 and 3.

## Style Rules

## Best Practice

### Virtual Environment

> Creating a virtual environment with `conda`.

```shell
conda create -n LPR python=3.6
source activate LPR
```

> Creating a virtual environment with `virtualenvwrapper`.

```shell
export WORKON_HOME=~/.envs
mkdir -p $WORKON_HOME
source /usr/local/bin/virtualenvwrapper.sh
mkvirtualenv LPR
```

Installing packages into global `site-packages` is not a good practice. Some common problem we face is version clashing and permission (requires `sudo` to install a package).

The best practice is to use virtual environment for each project. It allows projects to use different version of dependencies. We could also install the package without `sudo`.

#### Pros

- Projects are isolated in their virtual environments.
- Each virtual environment has their own `site-packages`.
- Does not requires `sudo` to install package.

#### Cons

- Required more disk space, for a package could be installed in more than one place at a time.

### Directory Structure

> Example of a package structure.

```shell
packagename
    docs
    examples
    packagename
        __init__.py
        yourmodule.py
        othermodule.py
    tests
        test_yourmodule.py
        test_othermodule.py
    .gitignore
    CHANGELOG.md
    LICENSE
    README.md
    requirements.txt
    setup.py
```

#### Project Properties

| Location         | Purpose                                          |
| ---------------- | ------------------------------------------------ |
| `.gitignore`     | List of file name patterns to be ignored by git. |
| `./CHANGELOG.md` | Lists every major changes on each version.       |
| `./LICENSE`      | Full text of the license of this package.        |
| `./README.md`    | Overview of the package.                         |

#### Modules and Package

| Location          | Purpose                                                             |
| ----------------- | ------------------------------------------------------------------- |
| `./packagename/`  | If there's more than one module, put all modules in this directory. |
| `./yourmodule.py` | If there's only one module, put it directly on root directory.      |

#### Documentations

| Location  | Purpose                          |
| --------- | -------------------------------- |
| `./docs/` | Package reference documentation. |

#### Test Suites

| Location               | Purpose                          |
| ---------------------- | -------------------------------- |
| `./tests/`             | Package reference documentation. |
| `./test_yourmodule.py` |                                  |

#### Dependencies and Setup Script

| Location             | Purpose                                   |
| -------------------- | ----------------------------------------- |
| `./requirements.txt` | List of package development dependencies. |
| `./setup.py`         | Script to build and install the package.  |

#### Examples
