.. _ci:

Continuous Integration Tools
============================

Travis
------

`Travis <https://travis-ci.org/>`_ is a so-called "continuous integration" suite. Usually, that's
fancy speak for "builds/tests your code when you push to Github".


In order to set up Travis to automatically build/test your code, simply make an
account `on their website <https://travis-ci.org/>`_ and follow the instructions
to link this with your Github account.

You can then simply copy the ``.travis.yml`` file from this package into your
own git repo.

.. note::

    In order for the provided ``travis.yml`` file to work, you'll either need to
    follow the instructions in the section on :ref:`automatically pushing to
    PyPI <auto-pypi>` below, or simply delete the "deploy" section of the
    ``yml`` file for now.

By default, this ``travis.yml`` file simply runs ``pytest``.

.. _versioneer:

Versioneer
----------

My favorite way to keep track of versions is using the `Versioneer
package <https://github.com/warner/python-versioneer>`_.

To get versioneer working, simply copy the ``[versioneer]`` section of this
project's ``setup.cfg`` file, and change the "prefix" (i.e. if you want your git
tags to look like "v-1.2.3" instead of "pte-v1.2.3", change "pte-v" to "v-") and
change the folder the _version.py file should go into to your package's
directory.

Now run

.. code-block:: bash

    $ versioneer install

at your project's top level.

Modify your setup.py to contain the lines

.. code-block:: python

    import versioneer
    setup(...
            version=versioneer.get_version(),
            cmdclass=versioneer.get_cmdclass(),
            ...)

and make sure that any lines like :code:`__version__ = 'X.Y.Z'` have been
removed from your main ``__init__.py`` file. Then commit the results.

You can now update your package's version in all the relevant places (there's
like 2-6, depending on how complicated your package is) all at once by simply
tagging the relevant git commit with the new version number.

See below for detailed instructions.

.. _auto-pypi:

Automatically Pushing to PyPI
-----------------------------

Once you have Versioneer setup, the .travis.yml file included in this directory
will already be setup to push your code to PyPI any time you "tag" a git commit
with an new version number.

If you've never used git tags before, simply make a commit and then execute

.. code-block:: bash

    $ git tag  -a TAG-STRING -m "TAG DESCRIPTION"

so for example the commit with this documentation page in it was tagged

.. code-block:: bash

    $ git tag -a pte-v0.0.1 -m "Added pytest, travis, and relevant docs."

You can then make sure the tags are pushed (so that travis sees them when
building your commit), by pushing with the extra flag for tags

.. code-block:: bash

    $ git push --tags

The only change to the ``.travis.yml`` file required is to follow the
`instructions on the Travis website
<https://docs.travis-ci.com/user/deployment/pypi/>`_ for encrypting your PyPI
password in the Travis configuration file (you'll see mine encrypted there by
default, under ``deploy -> password -> secure``.

Automatically Deploying Documentation
-------------------------------------

This is covered in the :ref:`Sphinx Autodoc <auto-doc>` section.
