Building Krnlmon with Bazel
===========================

Krnlmon has an experimental Bazel buildsystem. It is not integrated with
the main CMake build, and is thus of use only to maintainers as a way to
do test builds or run unit tests.

Prerequisites
-------------

To use the Bazel buildsystem, you will need to install the following
packages on your development platform.

-  Bazel. We have tested with versions 7.1.2 and 7.4.0.

-  Netlink library (libnl-3)

-  Target SDE (DPDK or ES2K). Krnlmon does not support Tofino.

The buildsystem downloads private copies of Abseil, Googletest, and SAI.

Environment Variables
---------------------

The Bazel buildsystem requires that the following environment variables
be defined.

``SDE_INSTALL``
  Path to the SDE install directory.

Build Options
-------------

The buildsystem supports the following command-line options. They may be
specified before or after the target(s).

``--config {dpdk|es2k}``
  TDI target to support. Equivalent to the ``TDI_TARGET={dpdk|es2k}``
  cmake variable or the ``--target={dpdk|es2k}`` helper script option.

``--define target={dpdk|es2k}``
  Alternative to the ``--config`` flag.

``--//flags:ovs={true|false}``
  Whether to support OVSP4RT. Equivalent to the ``WITH_OVSP4RT={true|false}``
  cmake variable or (in the negative) the ``--no-ovs`` helper script option.

  Specify ``yes``, ``true``, or ``1`` to enable support, and ``no``,
  ``false``, or ``0`` to disable it.

Useful Targets
--------------

``:krnlmon``
  Krnlmon library (``libkrnlmon.so``).

``:dummy_krnlmon``
  Dummy application. Used to check for link errors.

Examples
--------

Build krnlmon for DPDK
~~~~~~~~~~~~~~~~~~~~~~

We are using the ``--define`` option and specifying it after the
build target.

.. code-block:: bash

   bazel build //:krnlmon --define target=dpdk

Build for ES2K without OVS
~~~~~~~~~~~~~~~~~~~~~~~~~~

We are using ``--config`` instead of ``define`` and specifying the
options before the target.

.. code-block:: bash

   bazel build --config es2k --//flags:ovs=no //:krnlmon

Build dummy application
~~~~~~~~~~~~~~~~~~~~~~~

Check for link issues
^^^^^^^^^^^^^^^^^^^^^

The ``dummy_krnlmon`` target links the krnlmon library with a dummy
main program. This allows you to check for unresolved external symbols
in the library.

.. code-block:: bash

   bazel build --config dpdk //:dummy_krnlmon

Check for RPATH issues
^^^^^^^^^^^^^^^^^^^^^^

You can check for RPATH problems by issuing the ``ldd`` comand and
looking for missing libraries in its output.

.. code-block:: bash

   ldd bazel-bin/dummy_krnlmon

Run unit tests
~~~~~~~~~~~~~~

To build and run the unit tests for ES2K:

.. code-block:: bash

   bazel test --config es2k //switchlink:all //switchsde:all

Sample output:

.. code-block:: text

   INFO: Analyzed 23 targets (2 packages loaded, 92 targets configured).
   INFO: Found 18 targets and 5 test targets...
   INFO: Elapsed time: 2.372s, Critical Path: 1.79s
   INFO: 34 processes: 13 internal, 21 linux-sandbox.
   INFO: Build completed successfully, 34 total actions
   //switchlink:switchlink_address_test                           PASSED in 0.0s
   //switchlink:switchlink_link_test                              PASSED in 0.1s
   //switchlink:switchlink_neigh_test                             PASSED in 0.0s
   //switchlink:switchlink_route_test                             PASSED in 0.0s
   //switchsde:switchsde_test                                     PASSED in 0.1s

   Executed 5 out of 5 tests: 5 tests pass.
