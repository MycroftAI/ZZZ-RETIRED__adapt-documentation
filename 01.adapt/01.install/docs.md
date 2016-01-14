---
title: Installation
taxonomy:
    category:
        - docs
---

>>>>>><p>Adapt is best installed from the Github source using pip.</p>

### Requirements

The Adapt Intent Parser supports Python 2.7.
 
### Pip Installation
PiP is a package manager used to install and manage software packages written in the Python programming language.  To use Pip you first need to install it.

**Ubuntu Linux:**
```
sudo apt-get install python-pip python-dev
```

### Install Adapt

>>>>>><p>For development on Adapt and Mycroft we use the sandboxing tool virtualenv to keep things clean. This allows for dependencies to be used for local use (as opposed to installing them globally).</p>

#### Ubuntu / Debian / Raspbian

To develop with the latest Adapt, install it via pip into your project's virtualenv

    sudo apt-get install virtualenv
    virtualenv myvirtualenv
    . myvirtualenv/bin/activate
    pip install -e git+https://github.com/mycroftai/adapt#egg=adapt-parser

To develop Adapt itself or try the examples, checkout from git and setup a virtualenv.

    sudo apt-get install virtualenv
    virtualenv myvirtualenv
    . myvirtualenv/bin/activate
    cd 
    git clone https://github.com/mycroftai/adapt
    cd adapt
    pip install -r requirements.txt


Verify installation by running the example code from the Adapt repository.

```
cd ~/adapt
PYTHONPATH=. python examples/multi_intent_parser.py "play some music by the clash"
```

#### OSX

El Capitan:

Xcode must be installed and Apple’s  license agreement accepted.
```
xcode-select --install
```

Install homebrew package manager. Homepage: brew.sh
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Update brew packages.
```
brew update
```

Install Adapt dependencies with brew and python-pip.
```
brew install git python
pip install python virtualenv
```

To develop with the latest Adapt, install it via pip into your project's virtualenv

    virtualenv myvirtualenv
    . myvirtualenv/bin/activate
    pip install -e git+https://github.com/mycroftai/adapt#egg=adapt-parser

To develop Adapt itself or try the examples, checkout from git and setup a virtualenv.

    virtualenv myvirtualenv
    . myvirtualenv/bin/activate
    cd 
    git clone https://github.com/mycroftai/adapt
    cd adapt
    pip install -r requirements.txt


Verify installation by running the example code from the Adapt repository.
```
cd ~/adapt
PYTHONPATH=. python examples/multi_intent_parser.py "play some music by the clash"
```

