.. Copyright 2024 Intel Corporation
   SPDX-License-Identifier: Apache 2.0

==========================
Running OVSP4RT Unit Tests
==========================

.. contents::
   :depth: 3

Building the Unit Tests
=======================

Full build
----------

The ovsp4rt unit tests are automatically built along with the rest of P4
Control Plane. If you're doing a full build, no other steps are necessary.

Directed build
--------------

If you're working on ovsp4rt or its tests, you probably don't want to
do a full build every time you make a change. In this case, you can build
just ovsp4rt and its dependencies.

Configuring cmake
~~~~~~~~~~~~~~~~~

The first step is to configure the build. If you've already done this step,
there's generally no need to repeat it.

There are a number of ways to configure cmake. We're using a combination
of environment variables (SDE_INSTALL, DEPEND_INSTALL) and command-line
parameters.

.. code-block:: bash

   cmake -B build -DTDI_TARGET=dpdk -DOVS_INSTALL_DIR=ovs/install

cmake can also be configured using a preload file, a preset, or a helper
script such as ``make-all.sh``.

Building the tests
~~~~~~~~~~~~~~~~~~

The second step is to build the ovsp4rt unit tests.

.. code-block:: bash

   cmake --build build -j5 --target ovsp4rt-unit-tests

Note the use of the ``ovsp4rt-unit-tests`` build target.

CMake will build the ovsp4rt unit tests, with their dependencies.

Running the Unit Tests
======================

Tests are executed via the ``ctest`` command, which must be run from the
build directory. We're going to do this in a nested subshell.

.. code-block:: bash

   (cd build; ctest -L ovsp4rt --output-on-failure)

The options we're using here are:

``-L ovsp4rt``
  By default, ctest runs all the tests defined within the project,
  which in this case would include the krnlmon tests. We use the
  ``-L`` option to specify a *test label* (``ovsp4rt``), instructing
  ctest to run only those tests that have that label.

``--output-on-failure``
  Normally, ctest writes a summary of the tests run, with an indication
  of whether each test passed or failed. If a test fails, you will have
  to locate the logfile to obtain any details. This option tells ctest
  to dump the log of the failing test to the console.

Measuring Test Coverage
=======================

TBD
