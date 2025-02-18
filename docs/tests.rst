Writing Tests and Examples
========================================

.. contents:: :local:


Writing Tests
-------------

Test scripts live in the :code:`tests/` directory in the top-level repo folder.
Tests are executed automatically on Travis using :code:`py.test`, which will
find all Python files with the word :code:`test` somewhere in their name (side note:
if it's not meant to be a test, don't put :code:`test` in the file name!).
The :code:`py.test` suite specifically checks for cases where an :code:`AssertionError`
is raised, so in your tests you should use :code:`assert` statements to check whether
the output is equal to (or very close to) a benchmarked value.

To write a test, create a directory in :code:`tests/` with a descriptive name
(such as the modules it's meant to test or a specific application of the code).
Inside, add the :code:`.in` files needed to run the test and create a Python
file :code:`test_<TEST_NAME>.py`. This file should have this basic structure:

.. code-block:: python

    from vplot import GetOutput
    import subprocess
    import numpy as np
    import os
    cwd = os.path.dirname(os.path.realpath(__file__))


    def test_something():
        """Brief description of what we're testing."""
        # Run vplanet in the test directory
        subprocess.run(['vplanet', 'vpl.in', '-q'], cwd=cwd)

        # Grab the output from the test direcetory
        output = GetOutput(path=cwd)

        # Run our comparisons
        assert np.isclose(output.log.final.planet.Eccentricity, 0)
        assert np.isclose(output.log.final.system.TotEnergy,
                          output.log.initial.system.TotEnergy)
        assert np.allclose(output.star.Luminosity,
                           np.array([0.0672752 , 0.0672752 , 0.0080845...]))

    if __name__ == "__main__":
        test_something()

In the example above, we're checking three things: that the final planet
eccentricity (from the log file) is zero; that the final system energy
(from the log file) is equal to the initial system energy; and that
the star's luminosity (from the forward file) is equal to some array of
values.

All modules should have unit tests, and ideally all module couplings as well.
To get a sense of what's being tested and what's not, take a look at the
:doc:`coverage` section.


Writing Examples
----------------

Examples live in the :code:`examples/` directory in the top-level repo folder, and,
just like bug reports, there should be one aptly-named folder per example. Include
the input files needed to run the example and a :code:`README.rst` file that
describes the example and its expected output in detail:

.. code-block:: rest

    Example name
    ============

    Overview
    --------

    ===================   ============
    **Date**              MM/DD/YY
    **Author**            AUTHOR_NAME
    **Modules**           `MODULE1_NAME <../src/MODULE1_NAME.html>`_
                          `MODULE2_NAME <../src/MODULE2_NAME.html>`_
    **Approx. runtime**   RUNTIME_IN_SECONDS
    **Source code**       `GitHub <https://github.com/VirtualPlanetaryLaboratory/vplanet-private/tree/master/examples/EXAMPLE_DIR_NAME>`_
    ===================   ============

    DETAILED DESCRIPTION OF THE EXAMPLE

    To run this example
    -------------------

    EXACT INSTRUCTIONS TO REPRODUCE EXAMPLE

    .. code-block:: bash

        vplanet vpl.in
        vplot


    Expected output
    ---------------

    FIGURES SHOWCASING THE EXAMPLE OUTPUT

    .. figure:: UNIQUE_FIGURE_NAME.png
       :width: 600px
       :align: center

       FIGURE CAPTION


When creating an example, run it to figure out the approximate
runtime (see above) and create **low resolution PNG images** (no more
than about 800x800 pixels) with **unique file names** (so they don't
overwrite images from other examples when building the docs) in the
example directory. To commit these images, you'll need to **force add them**
since :code:`png` files are in the `.gitignore` by default:

.. code-block:: bash

    git add -f UNIQUE_FIGURE_NAME.png
