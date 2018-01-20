# Python virtualenv - The why and how.

If something is worth doing, it's worth doing well.  These words have stuck with me since first hearing them in my 6th grade shop class.  Spoken by a man missing a few digits on his left hand and repeatedly arguing against the use of seat-belts.  This contradiction of wisdom  mixed with stubbornness was one of the fundamental realizations of my childhood that made me start questioning the adults around me.  Maybe they don't have it all figured out like I thought.  Nonetheless, it's a great statement and has carried a lot of weight into my adulthood and shaped much of my work ethic.

If you're building a new python project, it's worth your time to create an environment that will not touch the python packages on your workstation while you're developing it.  When it comes time to ship that code to production, it is very much so worth it to keep the new code's dependencies as isolated from existing code already running on that production server.  If you have the resources available, consider even going as far as putting it in it's own container, such as docker.

Using a virtual environment saves you the headache of ending up with 100 different pip packages strewn across your system and no intelligible way of knowing what goes where and how updating project X dependencies will affect project Y.  It will also let you easily keep track of what packages are being used for individual projects and output them to requirements.txt files that you will save alongside your code for the next person to use to install everything they need to make your project work and nothing extra.

## Installation
Installation is fairly quick and painless.  I'll go over how to install and configure python virtual environment for a few systems that I work with on a day to day basis.

### Arch Linux

Install using pacman

    pacman -S python-virtualenv python-virtualenvwrapper

Add the following to your .bashrc

    export WORKON_HOME=~/.virtualenvs
    source /usr/bin/virtualenvwrapper.sh

### Ubuntu 16.04

Install using apt-get

    apt-get install python-virtualenv virtualenvwrapper

Add the following to your .bashrc

    export WORKON_HOME=~/.virtualenvs
    source /usr/share/virtualenvwrapper/virtualenvwrapper.sh

### Mac OSX

Install using brew


## Basic usage

Create virtual env defaulting to python3

    mkvirtualenv my_env

Create virtual env using python2.7 
     
     mkvirtualenv -p /usr/bin/python2.7 my_env

Activate the virtual environment

    workon my_env 

Install packages in your activated env (ex. django)

    (my_env)$ pip install django

Write requirements.txt file containing all current packages that were installed using pip.

    (my_env)$ pip freeze > requirements.txt

Deactivate virtual environment

    (my_env)$ deactivate

List all virtual environments 

    lsvirtualenv
    > my_env
    > ======

Delete an environment 
    
    rmvirtualenv my_env 
