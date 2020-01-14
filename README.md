This is a skeleton project for creating a reproducible, cross-platform
analysis notebook, using Docker.

To use it:

* clone this repo
* copy the files to a new folder
* delete the `.git` subfolder
* init this as a new git repo
* replace this front matter with information about your project;
   * you should probably keep the rest of the contents to help other users of this package
   * but edit the URL of the "quick start" button below to match the location of your new repo
* push it to Github,
* start coding


# Viewing and interacting with notebooks

Notebooks live in the `notebooks/` folder (with an `ipynb`
extension). Github should render these so you can read them, but can
be flakey; an alternative is to post the URL for a notebook into
https://nbviewer.jupyter.org/.

You can view *and interact* with any notebooks in the `notebooks/`
folder by launching the notebook in the free online service,
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ebmdatalab/seb-docker-test/master).

Any changes you make there won't be saved; to do development work,
you'll need to set up a local jupyter server and git repository.


# Folder layout

By convention, all Jupyter notebooks live in `notebooks/`.  When
notebooks look like they will contain more than a few lines of Python,
the Python is separated into a separate module, in `lib/`, and
imported from the notebook.

`config/` contains the configuration required to run the Notebook; you
shouldn't have to touch this.


# Developing notebooks

There are two ways of getting started with a development environment:
with Docker, or using Python virtual environments.

Docker allows you to run identical software on all platforms. In
Windows, in particular, there are challenges ensuring all python
packages are exactly the same as those available on other platforms.

## Docker enviroment

### Installation

Follow installation instructions
[here](https://docs.docker.com/install/). If running on Windows, you
may find it useful first to refer to our own installation notes
[here](https://github.com/ebmdatalab/custom-docker/issues/4).

Windows users who log into an Active Directory domain (i.e. a network
login) may find they lack permissions to start Docker correctly. If
so, follow [these
instructions](https://github.com/docker/for-win/issues/785#issuecomment-344805180).

### Start notebook

On Linux or OS X:

    ./run.sh

On Windows, double-click `run.bat`.

This will start a Jupyter Lab server in a Docker container. You will
be able to access this in your web browser at http://localhost:8888/.
Changes made in the Docker container will appear in your own
filesystem, and can be committed as usual.

## Running without Docker

If you want to execute notebooks without Docker, set up a [virtual
environment](https://docs.python.org/3/tutorial/venv.html) for the
Python version in question (you can infer this from the first line of
the `Dockerfile` in the root of this repo).

Next, install dependencies that are normally automatically included by
Docker:

    pip install jupyter jupytext pip-tools

...and install this notebook's dependencies:

    pip-sync

Finally, run jupyter in the same way it's started in the Docker image:

    PYTHONPATH=$(pwd) jupyter notebook --config=config/jupyter_notebook_config.py


# Development best practices

## Installing new packages

Best practice is to ensure all your python dependencies are pinned to
specific versions. To ensure this, while still supporting upgrading
individual packages in a sane way, we use
[pip-tools](https://github.com/jazzband/pip-tools).

The workflow is:

* When you want to install a new package, add it to `requirements.in`
* Run `pip-compile` to generate a `requirements.txt` based on that file
* Run `pip-sync` to bring your environment in sync with this `requirements.txt`
* Commit both `requirements.in` and `requirements.txt` to your git repo

To *upgrade* a specific package:

    pip-compile --upgrade-package <packagename>

To upgrade everything:

    pip-compile --upgrade

Don't forget to run `pip-sync` after running any upgrade command.

To execute these within your dockerised environment, start a new Bash
console in Jupyter Lab (from the same menu you would create a new
notebook).

You can then run whatever shell commands you like, by prepending a
`!`, and then hitting Shift + Enter to execute (for example,
`!pip-compile`)

## Jupytext and diffing

The Jupyter Lab server is packaged with Jupytext, which automatically
synchronises edits you make in a notebook with a pure-python version
in a subfolder at `notebooks/diffable_python`. This skeleton is also
set up with a `.gitattributes` file which means `ipynb` files are
ignored in Github Pull Requests, making it easier to do code reviews
against changes.
