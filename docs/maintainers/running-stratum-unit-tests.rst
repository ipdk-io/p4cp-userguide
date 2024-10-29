==========================
Running Stratum Unit Tests
==========================

Install Bazel
-------------

The Stratum unit tests run under control of the Bazel build system. You
will need to install the correct version of Bazel in order to run them.

.. code-block:: bash

   sudo apt install bazel-4.2.3

For maximum performance, you will want to do unit testing on a system
with sufficient resources (cores, memory, disk space) to support
multiple threads and a large Bazel cache, or a build farm with a large
remote cache.

Install SDE(s)
--------------

Build and install the SDEs for the targets you wish to test. You will
need to install all three SDEs (Tofino, DPDK, ES2K) in order to run the
full suite of tests.

You will need to make the following modification to the ES2K SDE after
you install it.

.. code-block:: bash

   echo "ES2K" > path-to-es2k-sde/share/TARGET

The Bazel build uses this file to verify that the SDE pointed to by the
environment variable is for ES2K.

Define environment variable(s)
------------------------------

You will need to define an environment variable that provides the path
to the install directory for the SDE.

========== =====================================================
**Target** **Environment Variable**
========== =====================================================
Tofino     SDE_INSTALL=path-to-tofino-sde (*or*)
           SDE_INSTALL_TAR=path-to-tarball-containing-tofino-sde
DPDK       DPDK_INSTALL=path-to-dpdk-sde (*or*)
           SDE_INSTALL=path-to-dpdk-sde
ES2K       ES2K_INSTALL=path-to-es2k-sde (*or*)
           SDE_INSTALL=path-to-es2k-sde
========== =====================================================

Source code
-----------

P4 Control Plane (networking-recipe)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are running unit tests on the full P4 Control Plane, you will
need to change to the Stratum root directory.

.. code-block:: bash

   git clone --recursive https://github.com/ipdk-io/networking-recipe.git recipe
   cd recipe/stratum/stratum

You can confirm that you are in the correct directory by checking for a
file named WORKSPACE.

.. code-block:: bash

   doctor@tardis:~/stratum$ ls WORKSPACE
   WORKSPACE

Standalone Stratum
~~~~~~~~~~~~~~~~~~

You can run the unit tests on a standalone instance of the Stratum
repository.

.. code-block:: bash

   git clone https://github.com/ipdk-io/stratum-dev.git stratum
   cd stratum

Run tests (no target specified)
-------------------------------

Single test
~~~~~~~~~~~

To execute a single test, issue a **bazel test** command specifying the
target test you wish to run:

.. code-block:: bash

   bazel test //stratum/hal/lib/p4:p4_table_mapper_test

For example:

.. code-block:: bash

   nemo@nautilus:~/recipe/stratum/stratum$ bazel test //stratum/hal/lib/p4:p4_table_mapper_test
   INFO: Analyzed target //stratum/hal/lib/p4:p4_table_mapper_test (2 packages loaded, 4105 targets configured).
   INFO: Found 1 test target...
   Target //stratum/hal/lib/p4:p4_table_mapper_test up-to-date:
     bazel-bin/stratum/hal/lib/p4/p4_table_mapper_test
   INFO: Elapsed time: 10.517s, Critical Path: 9.27s
   INFO: 5 processes: 2 internal, 3 linux-sandbox.
   INFO: Build completed successfully, 5 total actions

   //stratum/hal/lib/p4:p4_table_mapper_test                     PASSED in 0.5s

   INFO: Build completed successfully, 5 total actions

All tests in a directory
~~~~~~~~~~~~~~~~~~~~~~~~

You can run all the tests in a directory by specifying the target name **all**:

.. code-block:: bash

   bazel test //stratum/hal/lib/p4:all

For example:

.. code-block:: bash

   bilbo@bag_end:~/recipe/stratum/stratum$ bazel test //stratum/hal/lib/p4:all
   INFO: Analyzed 30 targets (0 packages loaded, 26 targets configured).
   INFO: Found 22 targets and 8 test targets...

   (progress messages)

   INFO: Elapsed time: 13.132s, Critical Path: 12.08s
   INFO: 56 processes: 8 internal, 48 linux-sandbox.
   INFO: Build completed successfully, 56 total actions
   //stratum/hal/lib/p4:p4_table_mapper_test (cached)            PASSED in 0.5s
   //stratum/hal/lib/p4:p4_action_mapper_test                    PASSED in 0.2s
   //stratum/hal/lib/p4:p4_config_verifier_test                  PASSED in 0.4s
   //stratum/hal/lib/p4:p4_info_manager_test                     PASSED in 0.2s
   //stratum/hal/lib/p4:p4_match_key_test                        PASSED in 0.5s
   //stratum/hal/lib/p4:p4_static_entry_mapper_test              PASSED in 0.2s
   //stratum/hal/lib/p4:p4_write_request_differ_test             PASSED in 0.3s
   //stratum/hal/lib/p4:utils_test                               PASSED in 0.3s
   INFO: Build completed successfully, 56 total actions

All tests in or below a directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can select a directory and its subdirectories by using an ellipsis
(**...**):

.. code-block:: bash

   bilbo@bag_end:~/stratum$ bazel test //stratum/glue/...
   INFO: Analyzed 21 targets (0 packages loaded, 33 targets configured).
   INFO: Found 15 targets and 6 test targets...

   (progress messages)

   INFO: Elapsed time: 9.293s, Critical Path: 8.81s
   INFO: 36 processes: 7 internal, 29 linux-sandbox.
   INFO: Build completed successfully, 36 total actions
   //stratum/glue/gtl:cleanup_test                                PASSED in 0.0s
   //stratum/glue/gtl:map_util_test                               PASSED in 0.0s
   //stratum/glue/net_util:absl_test                              PASSED in 0.1s
   //stratum/glue/net_util:bits_test                              PASSED in 0.9s
   //stratum/glue/net_util:ipaddress_test                         PASSED in 0.1s
   //stratum/glue/status:status_test                              PASSED in 0.0s
   INFO: Build completed successfully, 36 total actions

Run tests
---------

Target-specific tests
~~~~~~~~~~~~~~~~~~~~~

If any of the tests you wish to run is in one of the following
directories, you will need to specify the target to use:

-  //stratum/hal/lib/common
-  //stratum/hal/lib/tdi
-  //stratum/hal/lib/tdi/dpdk
-  //stratum/hal/lib/tdi/es2k
-  //stratum/hal/lib/tdi/tofino

You do this by appending the following to the end of the command line:

.. code-block:: bash

   --define target=<target>

where <target> is one of "dpdk", "es2k", or "tofino".

For example:

.. code-block:: bash

   bazel test //stratum/hal/lib/tdi/dpdk:dpdk_chassis_test --define target=dpdk

All tests in a file
~~~~~~~~~~~~~~~~~~~

You can put a list of targets in a text file:

**tofino-tests.txt**

.. code-block:: text

   //stratum/hal/lib/tdi/tofino:tofino_hal_test
   //stratum/hal/lib/tdi/tofino:tofino_switch_test

And run them using the command:

.. code-block:: bash

   xargs -a tofino-tests.txt bazel test --define target=tofino

For example:

.. code-block:: bash

   homer@springfield:~/stratum$ xargs -a tofino-tests.txt bazel test --define target=tofino
   INFO: Build option --define has changed, discarding analysis cache.
   DEBUG: /home/homer/recipe/stratum/stratum/stratum/hal/lib/tdi/tofino/tofino.bzl:29:10:
   Detected SDE version: 9.11.0.
   INFO: Analyzed 4 targets (1 packages loaded, 12274 targets configured).
   INFO: Found 2 targets and 2 test targets...
     (progress messages omitted)
   INFO: Build completed successfully, 49 total actions
   //stratum/hal/lib/tdi/tofino:tofino_hal_test (cached)          PASSED in 0.4s
   //stratum/hal/lib/tdi/tofino:tofino_switch_test (cached)       PASSED in 0.4s

   INFO: Build completed successfully, 49 total actions

ES2K Tests
~~~~~~~~~~

The following is a suggested set of unit tests for ES2K:

**es2k-tests.txt**

.. code-block:: text

   //stratum/glue/...
   //stratum/hal/lib/p4/...
   //stratum/lib/...
   //stratum/public/...
   //stratum/hal/lib/tdi:all
   //stratum/hal/lib/tdi/es2k:es2k_chassis_manager_test
   //stratum/hal/lib/tdi/es2k:es2k_hal_test
   //stratum/hal/lib/yang...

These tests should be run against the ES2K SDE:

.. code-block:: bash

   xargs -a es2k-tests.txt bazel test --define target=es2k

DPDK Tests
~~~~~~~~~~

The following is a suggested set of unit tests for DPDK:

**dpdk-tests.txt**

.. code-block:: text

   //stratum/glue/...
   //stratum/hal/lib/p4/...
   //stratum/lib/...
   //stratum/public/...
   //stratum/hal/lib/tdi:tdi_action_profile_manager_test
   //stratum/hal/lib/tdi:tdi_counter_manager_test
   //stratum/hal/lib/tdi:tdi_packetio_manager_test
   //stratum/hal/lib/tdi:tdi_pipeline_utils_test
   //stratum/hal/lib/tdi:tdi_pre_manager_test
   //stratum/hal/lib/tdi:tdi_table_manager_test
   //stratum/hal/lib/tdi:utils_test
   //stratum/hal/lib/tdi/dpdk:dpdk_chassis_manager_test
   //stratum/hal/lib/yang/...

These tests should be run against the DPDK SDE:

.. code-block:: bash

   xargs -a dpdk-tests.txt bazel test --define target=dpdk

Flaky Tests
~~~~~~~~~~~

Some of the unit tests are known to be flaky (they fail intermittently).
These tests have been flagged as flaky by specifying tags = ["flaky"] to
the stratum_cc_test rule.

To run a test suite excluding flaky tests:

.. code-block:: bash

xargs -a dpdk-tests.txt bazel test --define target=dpdk --test_tag_filters=-flaky

Test results
------------

Stratum unit tests use the Google Test framework, so it is possible to
generate XML or JSON output by specifying the --gtest_output parameter
on the test command line.

I have not found a way to do this through the bazel test command.

.. _measure-code-coverage-1:

Measure code coverage
---------------------

Run and measure test
~~~~~~~~~~~~~~~~~~~~

Unit test code coverage can be measured by means of the **bazel coverage**
command:

.. code-block:: bash

   bilbo@bag_end:~/recipe/stratum/stratum$ bazel coverage \
      --combined_report=lcov \
      --javabase=@bazel_tools//tools/jdk:remote_jdk11 \
      //stratum/hal/lib/p4:p4_table_mapper_test
   INFO: Using default value for --instrumentation_filter: "^//stratum/hal/lib/p4[/:]".
     (progress messages omitted)
   INFO: Build completed successfully, 71 total actions
     (results abbreviated)
   //stratum/hal/lib/p4:p4_table_mapper_test                      PASSED in 1.9s

   INFO: Build completed successfully, 71 total actions

Generate coverage report
~~~~~~~~~~~~~~~~~~~~~~~~

To generate an HTML coverage report:

.. code-block:: bash

   bilbo@bag_end:~/recipe/stratum/stratum$ genhtml \
      --output coverage \
      "$(bazel info output_path)/_coverage/_coverage_report.dat"
   Reading data file ...
     (progress messages omitted)
   Writing directory view page.
   Overall coverage rate:
     lines......: 62.3% (938 of 1505 lines)
     functions..: 64.0% (174 of 272 functions)

.. _view-report-1:

View report
~~~~~~~~~~~

To view the coverage report, open **coverage/index.html** in a browser:

|image1|

Click the directory name to see the files page:

|image2|

Measure multiple tests
---------------------------

.. _run-and-measure-tests-1:

Run and measure tests
~~~~~~~~~~~~~~~~~~~~~

To run and measure coverage for all the tests in a directory:

.. code-block:: bash

   milady@dewinter:~/recipe/stratum/stratum$ bazel coverage \
      --combined_report=lcov \
      --javabase=@bazel_tools//tools/jdk:remote_jdk11 \
      //stratum/hal/lib/p4:all
   INFO: Using default value for --instrumentation_filter: "^//stratum/hal/lib/p4[/:]".
     (progress messages omitted)
   INFO: Build completed successfully, 119 total actions
     (results abbreviated)
   //stratum/hal/lib/p4:p4_table_mapper_test (cached)             PASSED in 1.9s
   //stratum/hal/lib/p4:p4_action_mapper_test                     PASSED in 1.9s
   //stratum/hal/lib/p4:p4_config_verifier_test                   PASSED in 2.6s
   //stratum/hal/lib/p4:p4_info_manager_test                      PASSED in 2.0s
   //stratum/hal/lib/p4:p4_match_key_test                         PASSED in 2.2s
   //stratum/hal/lib/p4:p4_static_entry_mapper_test               PASSED in 2.3s
   //stratum/hal/lib/p4:p4_write_request_differ_test              PASSED in 1.7s
   //stratum/hal/lib/p4:utils_test                                PASSED in 1.9s

   INFO: Build completed successfully, 119 total actions

.. _generate-coverage-report-1:

Generate coverage report
~~~~~~~~~~~~~~~~~~~~~~~~

To generate an HTML coverage report:

.. code-block:: bash

   milady@dewinter:~/recipe/stratum/stratum$ rm -fr coverage/
   milady@dewinter:~/recipe/stratum/stratum$ genhtml \
      --output coverage \
      "$(bazel info output_path)/_coverage/_coverage_report.dat"
   Reading data file...
     (progress messages omitted)
   Writing directory view page.
   Overall coverage rate:
     lines......: 86.7% (1393 of 1607 lines)
     functions..: 82.2% (267 of 325 functions)

.. _view-report-2:

View report
~~~~~~~~~~~

To view the coverage report, use a browser to open **coverage/index.html**:

|image3|

Click the **p4** directory to view the files page:

|image4|

.. |image1| image:: images/stratum-p4-mapper-top-page.png
.. |image2| image:: images/stratum-p4-mapper-files-page.png
.. |image3| image:: images/stratum-p4-report-dir-page.png
.. |image4| image:: images/stratum-p4-report-files-page.png

