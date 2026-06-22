.. Copyright 2024 Intel Corporation
   SPDX-License-Identifier: Apache 2.0

===================
Infrap4d User Guide
===================

.. contents::
   :depth: 3

infrap4d is the P4 Control Plane server daemon. It consists of:

- a P4Runtime server, which configures the P4 packet processing pipeline
- a gNMI server, which performs port configuration
- a Kernel Monitor, which listens for network events and updates tables
  in the P4 pipeline.

Syntax
======

.. code-block:: text

   infrap4d \
       [--help | --helpshort | --helpon MODULE] \
       [--[no]detach] \
       [--disable-krnlmon] \
       [other options]

infrap4d is located in the ``sbin`` directory of the install tree.


Command-Line Flags
==================

Flag prefix
-----------

A flag may be prefixed with either one or two hyphens.
``-help`` and ``--help`` are equivalent.

Underscores vs. hyphens
-----------------------

Underscores and hyphens may be used interchangeably within the name
of a flag. ``--disable_krnlmon`` and ``--disable-krnlmon`` are equivalent.

Boolean values
--------------

A Boolean value of **true** may be expressed as ``true``, ``yes``, ``on``,
or ``1``, or by specifying the flag without a value. For example:

.. code-block:: text

   -detach
   -detach=true
   -detach yes

A Boolean value of **false** may be expressed as ``false``, ``no``, ``off``,
or ``0``, or by prefixing the flag name with ``no``. For example:

.. code-block:: text

   -nodetach
   -detach=no
   -detach 0

Documentation
-------------

Infrap4d uses the Google ``gflags`` library to process command-line flags.
Technical documentation is available in
`How to Use Gflags <https://gflags.github.io/gflags/>`_.
This document is intended for developers using the library, but it
has some information on Special Flags as well.

Help Options
============

infrap4d provides several ways to display lists of supported flags.
The most useful of these are:

``--helpshort``
  Displays just the flags that are specific to the main module
  (``infrap4d_main``).

``--helpon MODULE``
  Displays just the flags that are defined by the named module.

  MODULE is the name of the source file in which the flags are defined,
  without the ``.cc`` suffix; for example, ``logging`` or ``tdi_hal_flags``.

``--help``
  Displays the flags for all modules. The list is long.

The help flags are defined by the ``gflags_reporting`` module.

.. code-block:: bash

   infrap4d --helpon gflags_reporting

Infrap4d Options
================

These flags influence the way infrap4d starts up.
They are defined by the ``infrap4d_main`` module.

``--detach``
  Run infrap4d in detached mode. type: Boolean. default: true.

  In detached mode, infrap4d runs as a separate process, as a daemon.
  In non-detached mode, it runs in the current process, as an
  application.

``--disable-krnlmon``
  Do not start the Kernel Monitor. type: Boolean. default: false.

Other Options
=============

See the :ref:`infrap4d_flags` for a complete list of flags supported by
infrap4d.

Verifying Settings
==================

gflags processes all the flags on the command line before it displays
``help`` output. The help output includes the current value of any
flags that have been set, as the following example demonstrates.

.. code-block:: text

   $ ./install/sbin/infrap4d -disable-krnlmon -helpshort
   infrap4d: P4Runtime server for P4 Control Plane.

   Flags from /home/dfoster/work/latest/infrap4d/infrap4d_main.cc:
     -detach (Run infrap4d in detached mode) type: bool default: true
     -disable_krnlmon (Run infrap4d without krnlmon support) type: bool
      default: false currently: true

gflags doesn't always do what you expect. This is a way to see the effect
of the flags on the command line.
