Building Krnlmon with CMake
===========================

Prerequisites
-------------

Install:

-  CMake (version 3.15 or above)

-  Netlink library (libnl-3)

-  Stratum dependencies
   (`https://github.com/ipdk-io/stratum-deps <https://github.com/ipdk-io/stratum-deps>`__)

-  Target SDE (DPDK or ES2K). Krnlmon does not support Tofino.

Environment Variables
---------------------

You can make things more convenient by defining the following environment
symbols:

``DEP_INSTALL``
  Directory in which the Stratum dependencies for the runtime system are
  installed.

``OVS_INSTALL``
  Directory in which Open vSwitch (OvS) is installed.

``SDE_INSTALL``
  Directory in which the P4 SDE for the target is installed.

Integrated Builds
-----------------

Krnlmon is normally built from the top-level folder (``networking-recipe``),
as part of the P4 Control Plane build.

Integrated builds are usually done using the helper script ``make-all.sh``
(:doc:`/scripts/make-all`).

You will typically want to start by removing artifacts from previous
builds:

.. code-block:: text

   rm -fr build install

Note that these directories are specific to integrated builds. They have
no effect on standalone builds.

Full build
~~~~~~~~~~

To build all of P4 Control Plane, including krnlmon:

.. code-block:: bash

   ./make-all.sh --target=TARGET --rpath

where TARGET is ``dpdk`` or ``es2k``.

Full build (no OVS)
~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   ./make-all.sh --target=TARGET --rpath --no-ovs

This removes the need for ``make-all.sh`` to build Open vSwitch and
enables/disables certain functionality in krnlmon.

Krnlmon only
~~~~~~~~~~~~

To build just krnlmon:

.. code-block:: bash

   ./make-all.sh --target-TARGET --rpath --no-build
   cmake --build build -j4 --target krnlmon

Standalone Builds
-----------------

It is possible to build krnlmon by itself, from within the ``krnlmon/krnlmon``
folder. This is useful when you are working on the krnlmon source code.

.. code: bash

   cd krnlmon/krnlmon

Preparation
~~~~~~~~~~~

You will generally want to begin by removing artifacts from previous
builds:

.. code-block:: bash

   rm -fr build install

Note that these directories are specific to standalone builds. The have
no effect on integrated builds.

DPDK build
~~~~~~~~~~

.. code-block:: bash

   cmake -B build -C dpdk.cmake [options]
   cmake --build build -j4 --target install

``dpdk.cmake`` is a cmake configuration file that selects the DPDK
target, sets the install prefix to ``install``, and enables RPATH. The
SDE install path will taken from the ``SDE_INSTALL`` environment
variable, and the Stratum Dependencies install path will be taken from
the ``DEPS_INSTALL``

You may specify additional options, or override the configuration file,
by defining cmake variables (``-DVARNAME=VALUE``) on the command line.
You can disable a variable by specifying ``-UVARNAME``.

You can also create your own configuration file and use it in place of
``dpdk.cmake`` or ``es2k.cmake``.

ES2K build
^^^^^^^^^^

.. code-block:: bash

   cmake -B build -C es2k.cmake [options]
   cmake --build build -j4 --target install
