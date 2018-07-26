=========================================
AlTar(2.0) Installation Guide (Linux)
=========================================

.. sectnum::

.. contents:: Table of Contents


Prerequisites
~~~~~~~~~~~~~

List of required libraries
--------------------------
To compile altar/pyre, the following software or libraries are required ::

      python3  >= 3.6 (**must**)
      gcc >= gcc6 (**must**)
      gsl
      hdf5
      postgresql 
      openmpi 
    
      openblas (recommended)
      cuda (optional)  
    
It is recommended that you download the source code and compile these packages by your own. (Most systems come with older versions of python3/gcc; they won't be able to catch the pace of pyre or Michael.)

Use SPACK to install required packages 
--------------------------------------

`Spack <https://spack.io/>`_ 


Use EasyBuild to install required packages 
------------------------------------------

Use the `bootstrapping method <http://easybuild.readthedocs.io/en/latest/Installation.html#bootstrapping-procedure/>`_ to install Easybuild, 

If you are a system user, use a ``EASYBUILD_PREFIX=/opt/apps``, otherwise, use ``$HOME/.local/easybuild``. 

The following guides are based on ``foss-2018b`` toolchain (Free and Open Source Software).  
::
      foss-2018b.eb   # including gcc-7, openmpi, openblas 
      Python-3.7.0-foss-2018b.eb
      netCDF-4.6.1-foss-2018b.eb
      GSL-2.4-GCCcore-7.3.0.eb (needs some changes)
      PostgreSQL (needs some changes)
      
To search a package
::
      eb -S package
      
To install packages
::
      eb package(s) -robot 
    
Ubuntu (recommends 18.04)
-------------------------


Download config/pyre/altar
~~~~~~~~~~~~~~~~~~~~~~~~~~
Choose a directory where you plan to put all files, e.g., ``${HOME}/tools``, and define it as ``ALTAR_SRC_DIR`` 
(the following guide assumes ``bash``, please switch to ``bash`` if you are using other shells)
::
      
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

``mm`` is software building tool similar to ``make``, ``scons``. To setup ``mm``,  ::


      #### bash #####
      $ alias mm='python3 ${HOME}/tools/config/make/mm.py'
      #### csh/tcsh #####
      $ alias mm 'python3 ${HOME}/tools/config/make/mm.py'

Create mm config file for pyre
---------------------------------

Create a ``config.def`` file under ``${HOME}/tools/pyre/.mm`` directory ::


      APPS_DIR = /home/geomod/apps/rhel7 
      
      GSL_DIR = $(APPS_DIR)/gsl
      GSL_INCDIR = $(GSL_DIR)/include
      GSL_LIBDIR = $(GSL_DIR)/lib

      HDF5_DIR = $(APPS_DIR)/hdf5
      HDF5_INCDIR = $(HDF5_DIR)/include
      HDF5_LIBDIR = $(HDF5_DIR)/lib

      LIBPQ_DIR = $(APPS_DIR)/postgresql
      LIBPQ_INCDIR = $(LIBPQ_DIR)/include
      LIBPQ_LIBDIR = $(LIBPQ_DIR)/lib

      MPI_DIR = $(APPS_DIR)/openmpi
      MPI_EXECUTIVE = mpirun
      MPI_INCDIR = $(MPI_DIR)/include
      MPI_LIBDIR = $(MPI_DIR)/lib
      MPI_VERSION = openmpi

      PYTHON = python3.6m
      PYTHON_DIR = $(APPS_DIR)/python3
      PYTHON_INCDIR = $(PYTHON_DIR)/include/$(PYTHON)
      PYTHON_LIB = $(PYTHON)
      PYTHON_LIBDIR = $(PYTHON_DIR)/lib
      PYTHON_PYCFLAGS = -b

      CUDA_DIR = /usr/local/cuda
      CUDA_INCDIR = $(CUDA_DIR)/include
      CUDA_LIBDIR = $(CUDA_DIR)/lib64



Please modify this file manually if you have a different version of packages, and/or have them installed in a different directory. 

Compile pyre
------------
*Currently there is a bug in compile process*: before Michael fixes this, please run ::

      $ mkdir -p ${HOME}/tools/pyre/products/modules

at first. 

To compiler pyre, simply go to ``pyre`` directory and run ``mm`` ::

      $ cd ${HOME}/tools/pyre
      $ mm 



Install pyre to tools directory
------------------------------
The compiled pyre package, including python packages, shared libraries, by default, is under ``${HOME}/tools/pyre/products`` directory. It is preferred to install pyre to another directory (e.g., ``${HOME}/tools``) to keep a stable working version ::

      $ cd ${HOME}/tools
      $ rsync -r ${HOME}/tools/pyre/products/* .

To set up environment variables (different paths) for pyre, create a ``${HOME}/tools/altar.rc`` script file as follows (we name it after altar already because altar is going to be installed in ``${HOME}/tools`` as well and one script is enough to load both pyre and altar)  

:content:   `${HOME}/tools/altar.rc <https://github.com/lijun99/altar-install/blob/master/mac/altar.rc>`_
:download link:  `altar.rc <https://raw.githubusercontent.com/lijun99/altar-install/master/mac/altar.rc>`_ 
   

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

Create a file ``${HOME}/tools/altar/.mm/config.mm`` (please change ``APPS_DIR`` and ``pyre.dir`` if neccesary):: 

       
      APPS_DIR = /home/geomod/apps/rhel7 
      gsl.dir = ${APPS_DIR}/gsl
      hdf5.dir = ${APPS_DIR}/hdf5
      mpi.dir = ${APPS_DIR}/openmpi
      openblas.dir = ${APPS_DIR}/openblas 
      pyre.dir = ${HOME}/tools 
      python.dir = ${APPS_DIR}/python3
      python.version = 3.6
      # end of file


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
The ``${HOME}/tools/altar.rc`` script sets up environment variables for both pyre and alter.  Once it is sourced, you may access them from any working directory. 

We use the Gaussian model as a test::

      $ mkdir ${HOME}/test
      $ cp ${HOME}/tools/altar/models/gaussian/examples/gaussian.pfg ${HOME}/test
      $ cd ${HOME}/test
      $ gaussian

If you see the altar running with annealing process reports, congratulations!  If not, please ask for help!


Set up environment for future runs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To run pyre/altar in the future, it will be convenient to *add* the following lines to ``${HOME}/.bashrc`` file so that the environment variables are automatically set once you log in or open a Terminal ::


      ## ~/.bashrc 
      . ${HOME}/tools/altar.rc




