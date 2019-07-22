
=========================================
Docker image
=========================================

.. sectnum::

.. contents:: Table of Contents

Introduction
~~~~~~~~~~~~


Ubuntu (18.04)
~~~~~~~~~~~~~~

Prerequisite
------------

Packages  ::

    sudo apt install python3 python3-dev python3-numpy python3-h5py libgsl-dev libhdf5-dev libopenblas-dev libopenmpi-dev libpq




Anaconda3
~~~~~~~~~

Prerequisite
------------

Additional conda packages needed::

    conda install gsl
    conda install -c conda-forge openmpi

Issues
~~~~~~

::

    export OMPI_MCA_btl_vader_single_copy_mechanism=none

