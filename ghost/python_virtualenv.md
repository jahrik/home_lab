# Python virtualenv and wrapper usage on a few common systems

If something is worth doing, it's worth doing well.  These words have stuck with me since first hearing them in my 6th grade shop class.  Spoken by a man missing a few digits on his left hand and repeatedly arguing against the use of seat-belts.  This contradiction of wisdom  mixed with stubbornness was one of the fundamental realizations of my childhood that made me start questioning the adults around me.  Maybe they don't have it all figured out like I thought.  Nonetheless, it's a great statement and has carried a lot of weight into my adulthood and shaped much of my work ethic.

If you're building a new python project, it's worth your time to create an environment that will not touch the python packages on your workstation while you're developing it.  When it comes time to ship that code to production, it is very much so worth it to keep the new code dependencies as isolated from existing code already running on that production server.  If you have the resources available, consider even going as far as putting it in it's own docker container.  If you are using docker it will eliminate the need to use virtualenv and virtualenvwrapper all together [as pointed out](https://www.reddit.com/r/Python/comments/7rw0kn/python_virtualenv_and_wrapper_usage_on_a_few/) by a fellow redditor.

> "with docker, virtualenv is a thing of the past. been using docker for about a year now, i don’t even install virtualenv on my host machine anymore, just pop into docker and pip install directly in there; takes the same amount of time to build (virtualenv whatev-env; source whatev-env/bin/actívate vs docker run -v $(pwd):/code —rm -it python:1.2.3 sh) or just about the same.. BUT it gives you even more power: the ability to not have to bind your core dependencies to your host system, the ability to use any version of not only python, but ANYTHING! is a no brainer...
> virtualenv is dead, long live docker!" - kingbuzzman

With that in mind, if you're not willing to use docker in your dev or production environment you should still be using virtualenv to save you the headache of ending up with 100 different pip packages strewn across your system and no intelligible way of knowing what goes where and how updating project X dependencies will affect project Y.  It will also let you easily keep track of what packages are being used for individual projects and output them to requirements.txt files that you will save alongside your code for the next person to use to install everything they need to make your project work and nothing extra.

Python virtualenv works just fine without the wrapper.  You can read up some more on the features the wrapper adds [here](https://virtualenvwrapper.readthedocs.io/en/latest/).  The main benefit for me being that you can organize and use all virtual environments in one place.

Table of Contents
=================
* [Installation](#installation)
  * [Arch Linux](#arch-linux)
  * [Ubuntu 16.04](#ubuntu-1604)
  * [CentOS 7.2](#centos-72)
  * [Mac OSX](#mac-osx)
    * [System default python](#system-default-python)
    * [Home brew installed python](#home-brew-installed-python)
* [Basic virtualenv usage](#basic-virtualenv-usage)
* [Basic virtualenv wrapper usage](#basic-virtualenv-wrapper-usage)
* [Basic pip usage in the virtual environment](#basic-pip-usage-in-the-virtual-environment)
* [Testing](#testing)
  * [Ubuntu](#ubuntu)
  * [CentOS](#centos)
  * [Test it out on a Mac](#test-it-out-on-a-mac)
    * [System installed python](#system-installed-python)
    * [Homebrew installed python.](#homebrew-installed-python)
    * [Finally build an environment to test that is all works](#finally-build-an-environment-to-test-that-is-all-works)
    * [Clean up the vagrant files](#clean-up-the-vagrant-files)

## Installation
Installation is fairly quick and painless.  I'll go over how to install and configure python virtual environments for a few common systems.  I will show you the basic setup of virtualenv and why you might want to use virtualenv-wrapper to make things a bit easier.

### Arch Linux

Install using pacman

    pacman -S python-virtualenv python-virtualenvwrapper

Add the following to your .bashrc

    export WORKON_HOME=~/.virtualenvs
    source /usr/bin/virtualenvwrapper.sh

### Ubuntu 16.04

Install using apt-get

    apt-get update
    apt-get install python-virtualenv virtualenvwrapper

Add the following to your .bashrc

    export WORKON_HOME=~/.virtualenvs
    source /usr/share/virtualenvwrapper/virtualenvwrapper.sh

### CentOS 7.2

Install pip with curl

    curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
    python get-pip.py

Install virtualenv and wrapper with pip

    sudo pip install virtualenv
    sudo pip install virtualenvwrapper

Update your .bashrc with the following

    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
    export WORKON_HOME=~/virtualenvs
    source /usr/bin/virtualenvwrapper.sh

### Mac OSX

#### System default python

Install with pip

    sudo easy_install pip
    sudo pip install virtualenv
    sudo pip install virtualenvwrapper      

If you get the same six error as I did below

    sudo pip install --ignore-installed six virtualenvwrapper

Update your .bash_profile with the following

    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
    export WORKON_HOME=~/virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh

In one command

    printf '\n%s\n%s\n%s\n%s' '# virtualenv' 'export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python' 'export WORKON_HOME=~/virtualenvs' 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.bash_profile

#### Home brew installed python

Install using pip

    sudo pip install virtualenv
    sudo pip install virtualenvwrapper      

Update your .bash_profile with the following

    export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python
    export WORKON_HOME=~/virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh

In one command

    printf '\n%s\n%s\n%s\n%s' '# virtualenv' 'export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python' 'export WORKON_HOME=~/virtualenvs' 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.bash_profile

## Basic virtualenv usage

Create virtualenv defaulting to system default python (3 in my case)

    virtualenv my_env

Create virtual environment using python 2.7

    virtualenv -p /usr/bin/python2.7 my_env

Activate the virtual environment

    source my_env/bin/activate

Delete an environment 
    
    rm -rf my_env

## Basic virtualenv wrapper usage

Create virtual environment defaulting to system default python (3 in my case)

    mkvirtualenv my_env

Create virtual environment using python2.7 
     
    mkvirtualenv -p /usr/bin/python2.7 my_env

Activate the virtual environment

    workon my_env 

List all virtual environments 

    lsvirtualenv
    > my_env
    > ======

## Basic pip usage in the virtual environment

Install packages in your activated env (ex. django)

    (my_env)$ pip install django

Write requirements.txt file containing all current packages that were installed using pip.

    (my_env)$ pip freeze > requirements.txt

Install all requirements from a file

    (my_env)$ pip install -r requirements.txt

Deactivate virtual environment

    (my_env)$ deactivate

## Testing
I run Arch Linux on my local system, but I'd like to test out the other installations to verify the information I'm trying to share is correct.  I can easily fire up an ubuntu or centos box with a little help from docker.  To test out Mac OS I'll bring up a vm with vagrant and virtualbox.  Know that if you're using docker, you don't really need a virtual environment for python because it should be the only thing running in that container.  I'm using docker here to demonstrate the setup of virtualenv and wrapper.

### Ubuntu
Fire up a docker container running ubuntu 16.04 and test installing python virtualenv and virtualenv-wrapper

    docker run -it ubuntu:16.04 bash 

    root@8cb0c5dc4b40:/#
    root@8cb0c5dc4b40:/# apt-get update
    root@8cb0c5dc4b40:/# apt-get install python-virtualenv virtualenvwrapper
    root@8cb0c5dc4b40:/# export WORKON_HOME=~/.virtualenvs
    root@8cb0c5dc4b40:/# source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
    root@8cb0c5dc4b40:/# mkvirtualenv my_env
    (my_env) root@8cb0c5dc4b40:/# lsvirtualenv
    my_env
    ======
    (my_env) root@8cb0c5dc4b40:/# pip install requests
    Collecting requests
      Downloading requests-2.18.4-py2.py3-none-any.whl (88kB)
        100% |################################| 92kB 1.6MB/s 
    ...
    ...
    (my_env) root@8cb0c5dc4b40:/# pip freeze
    certifi==2018.1.18
    chardet==3.0.4
    idna==2.6
    pkg-resources==0.0.0
    requests==2.18.4
    urllib3==1.22

### CentOS
Once again, using docker to pull in the latest image of CentOS 7.2

    docker run -it centos:centos7.2.1511 bash

    [root@e90d4328b184 /]#
    [root@eb53167579de /]# curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
    [root@eb53167579de /]# python get-pip.py
    [root@eb53167579de /]# pip install virtualenv
    [root@eb53167579de /]# pip install virtualenvwrapper
    [root@eb53167579de /]# export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
    [root@eb53167579de /]# export WORKON_HOME=~/virtualenvs
    [root@eb53167579de /]# source /usr/bin/virtualenvwrapper.sh
    [root@eb53167579de /]# mkvirtualenv my_env
    (my_env) [root@eb53167579de /]# lsvirtualenv
    my_env
    ======
    (my_env) [root@eb53167579de /]# pip install requests
    Collecting requests
      Downloading requests-2.18.4-py2.py3-none-any.whl (88kB)
        100% |################################| 92kB 1.1MB/s
    ...
    ...
    (my_env) [root@eb53167579de /]# pip freeze
    certifi==2018.1.18
    chardet==3.0.4
    idna==2.6
    requests==2.18.4
    urllib3==1.22

### Test it out on a Mac
NOTE: I don't use a Mac at all, but I work with people who do, so It's handy to learn.

For testing this I have the following installed locally:
* [vagrant](https://www.vagrantup.com/)
* [virtualbox](https://www.virtualbox.org/wiki/Downloads)

Initialize a mac box with vagrant

    vagrant init jhcook/macos-sierra

Bring it up

    vagrant up

Login to your newly created mac environment

    vagrant ssh
    Last login: Sat Jan 20 19:44:44 2018 from 10.0.2.2
    This-MacBook-Pro:~ vagrant$

#### System installed python
I'm able to install virtualenv with the version of python that came on this vm, but when I go to install virtualenvwrapper, I'm getting this error `DEPRECATION: Uninstalling a distutils installed project (six) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.`.  You can stop here if all you need is virtualenv and not the wrapper, but I like the convenience that the wrapper provides.

    sudo easy_install pip

    sudo pip install virtualenv

    sudo pip install virtualenvwrapper      
    The directory '/Users/vagrant/Library/Caches/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
    The directory '/Users/vagrant/Library/Caches/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
    Collecting virtualenvwrapper
      Downloading virtualenvwrapper-4.8.2-py2.py3-none-any.whl
    Collecting stevedore (from virtualenvwrapper)
      Downloading stevedore-1.28.0-py2.py3-none-any.whl
    Collecting virtualenv-clone (from virtualenvwrapper)
      Downloading virtualenv-clone-0.2.6.tar.gz
    Collecting virtualenv (from virtualenvwrapper)
      Downloading virtualenv-15.1.0-py2.py3-none-any.whl (1.8MB)
        100% |################################| 1.8MB 683kB/s 
    Requirement already satisfied: pbr!=2.1.0,>=2.0.0 in /Library/Python/2.7/site-packages (from stevedore->virtualenvwrapper)
    Collecting six>=1.10.0 (from stevedore->virtualenvwrapper)
      Downloading six-1.11.0-py2.py3-none-any.whl
    Installing collected packages: six, stevedore, virtualenv-clone, virtualenv, virtualenvwrapper
      Found existing installation: six 1.4.1
        DEPRECATION: Uninstalling a distutils installed project (six) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
        Uninstalling six-1.4.1:
    Exception:
    Traceback (most recent call last):
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/basecommand.py", line 215, in main
        status = self.run(options, args)
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/commands/install.py", line 342, in run
        prefix=options.prefix_path,
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_set.py", line 778, in install
        requirement.uninstall(auto_confirm=True)
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_install.py", line 754, in uninstall
        paths_to_remove.remove(auto_confirm)
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_uninstall.py", line 115, in remove
        renames(path, new_path)
      File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/utils/__init__.py", line 267, in renames
        shutil.move(old, new)
      File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 302, in move
        copy2(src, real_dst)
      File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 131, in copy2
        copystat(src, dst)
      File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 103, in copystat
        os.chflags(dst, st.st_flags)
    OSError: [Errno 1] Operation not permitted: '/tmp/pip-PhG_df-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six-1.4.1-py2.7.egg-info'

I was able to get past this error with the following

    sudo pip install --ignore-installed six virtualenvwrapper

#### Homebrew installed python.

Install [home brew](https://brew.sh/)

    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Install python with homebrew

    brew install python

If running `which python` still shows the system version at `/usr/bin/python`, swap that out with the home brew installed version of python by making `usr/local/bin` show up before `usr/bin` in your path.  Add it to your ~/.bash_profile to make it permanent.

    export PATH=/usr/local/bin:$PATH

Test it.

    which python2.7
    /usr/local/bin/python2.7

In my case, I also added a symlink to `/usr/local/bin/python` so I can leave off the 2.7 bit.  If I wanted to use python3.5 as the default python, I would do the same thing.

    sudo ln -s /usr/local/bin/python2.7 /usr/local/bin/python

Test it.

    which python
    /usr/local/bin/python

#### Finally build an environment to test that is all works

    This-MacBook-Pro:~ vagrant$ mkvirtualenv myenv
    New python executable in /Users/vagrant/virtualenvs/myenv/bin/python
    Installing setuptools, pip, wheel...done.
    virtualenvwrapper.user_scripts creating /Users/vagrant/virtualenvs/myenv/bin/predeactivate
    virtualenvwrapper.user_scripts creating /Users/vagrant/virtualenvs/myenv/bin/postdeactivate
    virtualenvwrapper.user_scripts creating /Users/vagrant/virtualenvs/myenv/bin/preactivate
    virtualenvwrapper.user_scripts creating /Users/vagrant/virtualenvs/myenv/bin/postactivate
    virtualenvwrapper.user_scripts creating /Users/vagrant/virtualenvs/myenv/bin/get_env_details
    (myenv) This-MacBook-Pro:~ vagrant$ lsvirtualenv 
    myenv
    =====

#### Clean up the vagrant files

    vagrant destroy                                                                                       
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
    ==> default: Forcing shutdown of VM...
    ==> default: Destroying VM and associated drives...

    rm -rf .vagrant Vagrantfile
