=========================================
AlTar(2.0) Installation Guide (Mac)
=========================================

.. sectnum::

.. contents:: Table of Contents

Prerequisites
~~~~~~~~~~~~~

Install Macports
----------------
Please follow the instructions from https://www.macports.org/install.php. 

The detailed steps are as follows: 

- Install Xcode command line tools. Open a Terminal, and run the following command::

      $ sudo xcode-select --install

  You may receive a warning "Xcode does not appear to be installed" when installing packages: you may neglect these warnings. Alternatively, you can install a full version of Xcode from ``App Store`` and activate it by::
           
      $ sudo xcodebuild -license 

- Download the Macports pkg installer according to your MacOS version from https://www.macports.org/install.php.  (To find out your MacOS version, click the Apple logo at the top left corner of the screen and then click "About This Mac".) The download links for current version of Macports are provided below
   
     - `macOS High Sierra 10.13 <https://distfiles.macports.org/MacPorts/MacPorts-2.4.3-10.13-HighSierra.pkg>`_
     - `macOS Sierra v10.12 <https://distfiles.macports.org/MacPorts/MacPorts-2.4.3-10.12-Sierra.pkg>`_

  Click the downloaded ``.pkg`` file from browser or Finder to install Macports. The default installation location of Macports is ``/opt/local``. To check whether Macports is installed properly, you may run::

      $ which port
      /opt/local/bin/port

If nothing is shown, you may need to set up the right path to Macports by ::

      $ export PATH=/opt/local/bin:$PATH

and run ``which port`` command again. 

- Update Macports repo ::
     
      $ sudo port -v selfupdate

- Some useful ``port`` commands ::
  
      ### search a package name, e.g. gcc7 
      $ sudo port search gcc7
      ### install a package 
      $ sudo port install gcc7 
      ### uninstall a package 
      $ sudo port uninstall gcc7 
      ### select the default/active package from available versions (e.g., gcc)
      $ sudo port select --list gcc
      Available versions for gcc:
              mp-gcc7
              none (active)
      $ sudo port select --set gcc mp-gcc7 #to use gcc7 as the default gcc/g++ compiler
      $ sudo port select --set gcc none #to use the mac clang as the default gcc/g++ compiler
      ### list installed packages 
      $ sudo port installed 
      ### update Macports (run periodically)
      $ sudo port -v selfupdate
      $ sudo port upgrade outdated


Install required Macports packages 
----------------------------------

The required packages for pyre/altar are listed as follows:: 

    gmake
    git +bash_completion
    python36 +readline
    gcc7
    openmpi-gcc7 +fortran +threads
    gsl +gcc7
    openblas +gcc7
    hdf5 +cxx +gcc7 +openmpi
    fftw-3-single
    postgresql10
    gdal +gcc7 +hdf5 +openmpi +sqlite3 +spatialite
   
:download link: `ports.requested <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/ports.requested>`_
   
  ::

      $ curl -L https://raw.githubusercontent.com/lijun99/altar-install/master/mac/ports.requested -o ports.requested

You may install the packages in a batch mode by ::

      $ sudo port -Nu install $(cat ports.requested)

The whole process may take a long time (30mins to an hour). (You may proceed to the following steps while waiting.)  

Some post-installation configurations include, 

- select default compilers ::

     $ sudo port select --set python3 python36
     $ sudo port select --set gcc mp-gcc7 
     $ sudo port select --set mpi openmpi-gcc7-fortran

- To use bash_completion, add the following lines at the end of your .bash_profile (create one if not exist)::

      if [ -f /opt/local/etc/profile.d/bash_completion.sh ]; then
          . /opt/local/etc/profile.d/bash_completion.sh
      fi

- install other common python packages, e.g.,  ::

      $ sudo port install py36-scipy


Download config/pyre/altar
~~~~~~~~~~~~~~~~~~~~~~~~~~
Choose a directory where you plan to put all files, e.g., ``${HOME}/tools`` ::
      
      $ mkdir ${HOME}/tools
      $ cd ${HOME}/tools

Download the config/pyre/altar from their ``github`` repositories::

      $ git clone https://github.com/aivazis/config.git
      $ git clone https://github.com/pyre/pyre.git
      $ git clone https://github.com/AlTarFramework/altar.git

You shall see three directories ``config``, ``pyre``, ``altar`` under ``${HOME}/tools``. 

For developers: the instructions on how to sync with github repos will be provided elsewhere. 

Install pyre
~~~~~~~~~~~~

A detailed guide is available at http://pyre.orthologue.com/install.

Setup ``mm`` environment
----------------------------

``mm`` is software building tool similar to ``make``, ``scons``. To setup ``mm``, download a script file ``mm.rc`` and put it under, e.g., ``${HOME}/tools/`` directory:

:content: `${HOME}/tools/mm.rc <https://github.com/lijun99/altar-install/blob/master/mac/mm.rc>`_
:download link: `mm.rc <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/mm.rc>`_  
      
     ::

      curl -L https://raw.githubusercontent.com/lijun99/altar-install/master/mac/mm.rc -o ${HOME}/tools/mm.rc 


To load the `mm` environment, use the following command in a Terminal ::

      $ . ${HOME}/tools/mm.rc
      ### or #####
      $ source ${HOME}/tools/mm.rc


Create mm config file for pyre
---------------------------------

Create a ``config.def`` file under ``${HOME}/tools/pyre/.mm`` directory. 

:content: `${HOME}/tools/pyre/.mm/config.def <https://github.com/lijun99/altar-install/blob/master/mac/config.def>`_ 
:download link: `config.def <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/config.def>`_ 

  ::

      $ curl -L https://raw.githubusercontent.com/lijun99/altar-install/master/mac/config.def -o ${HOME}/tools/pyre/.mm/config.def


The ``config.def`` contains information (versions, paths) to required packages. Please modify this file manually if you have a different version of packages, and/or have them installed in a different directory (this will be the important step for Linux and other systems).  

Compile pyre
------------
*Currently there is a bug in compile process*: before Michael fixes this, please run ::

      $ mkdir -p ${HOME}/tools/pyre/products/modules

at first. 

To compiler pyre, simply go to ``pyre`` directory and run ``mm`` ::

      $ cd ${HOME}/tools/pyre
      $ mm 

You may experience a ``ConnectionRefusedError: [Errno 61] Connection refused`` error in the end. This is due to that ssh-server is not enabled in your Mac (System Preferences->Sharing->Remote Login). You may safely neglect this.   

Install pyre to tools directory
------------------------------
The compiled pyre package, including python packages, shared libraries, by default, is under ``${HOME}/tools/pyre/products`` directory. It is preferred to install pyre to another directory (e.g., ``${HOME}/tools``) to keep a stable working version ::

      $ cd ${HOME}/tools
      $ rsync -r ${HOME}/tools/pyre/products/* .

To set up environmental variables (different paths) for pyre, create a ``${HOME}/tools/altar.rc`` script file as follows (we name it after altar already because altar is going to be installed in ``${HOME}/tools`` as well and one script is enough to load both pyre and altar)  

:content:   `${HOME}/tools/altar.rc <https://github.com/lijun99/altar-install/blob/master/mac/altar.rc>`_
:download link:  `altar.rc <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/altar.rc>`_ 
   
  ::

      $ curl -L https://raw.githubusercontent.com/lijun99/altar-install/master/mac/altar.rc -o ${HOME}/tools/altar.rc

To load pyre (which is required for compiling altar), use the command::

      $ . ${HOME}/tools/altar.rc


Simple test
-----------
To test ``pyre`` is properly installed, you may try (from any directory):: 

      $ python3
      >>> import pyre
      >>> pyre.version()
      (1, 0, 'bb78330f')      


Install altar(2.0)
~~~~~~~~~~~~~~~~~~

Prepare the mm config file 
-----------------------

Create a file ``${HOME}/tools/altar/.mm/config.mm``. 

:content:   `${HOME}/tools/altar/.mm/config.mm <https://github.com/lijun99/altar-install/blob/master/mac/config.mm>`_
:download link:  `config.m <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/config.mm>`_ 
   
  ::

       $ mkdir ${HOME}/tools/altar/.mm
       $ curl -L https://raw.githubusercontent.com/lijun99/altar-install/master/mac/config.mm -o ${HOME}/tools/altar/.mm/config.mm

Compile altar
-------------
Go to ``altar`` directory and run ``make`` ::

      $ cd ${HOME}/tools/altar
      $ make


Install altar to tools directory
-------------------------------
The compiled altar package is located, by default, at ``${HOME}/tools/altar/builds``. Again, we recommend to install these files to ``${HOME}/tools/`` as well :: 

      $ rsync -r ${HOME}/tools/altar/builds/* ${HOME}/tools


A simple test
-------------
The ``${HOME}/tools/altar.rc`` script sets up environmental variables for both pyre and alter.  Once it is sourced, you may access them from any working directory. 

We use the Gaussian model as a test::

      $ mkdir ${HOME}/test
      $ cp ${HOME}/tools/altar/models/gaussian/examples/gaussian.pfg ${HOME}/test
      $ cd ${HOME}/test
      $ gaussian

If you see the altar running with annealing process reports, congratulations!  If not, please ask for help!


Set up environments for future runs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To run pyre/altar in the future, it will be convenient to *add* the following lines to ``${HOME}/.bash_profile`` file so that the environmental variables are automatically set once you log in or open a Terminal ::


      ## ~/.bash_profile 
      export PATH=/opt/local/bin:$PATH
      . ${HOME}/tools/altar.rc
      . ${HOME}/tools/mm.rc  
      if [ -f /opt/local/etc/profile.d/bash_completion.sh ]; then
          . /opt/local/etc/profile.d/bash_completion.sh
      fi


