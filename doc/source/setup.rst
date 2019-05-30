.. _setup:

setup.py
========

The new Python documenation for creating a ``setup.py`` file and publishing
code to PyPI is very thorough, and can be found on the `official Python
documentation site
<https://packaging.python.org/guides/distributing-packages-using-setuptools/>`_.

Here, we summarize the steps briefly and highlight the parts that are
likely to be important to someone sharing scientific (e.g. Physics) code.

Setting up the environment
--------------------------

We will assume a POSIX-like environment in what follows, and a ``Bash``-like
shell.

.. code-block:: bash

    $ python -m pip install setuptools wheel twine

on some machines, ``pip`` is not installed by default. One workaround is

.. code-block:: bash

    $ python -m ensurepip --default-pip


Now that the tools are installed, all that's left is to `make an account on PyPI <https://pypi.org/account/register/>`_.

To avoid typing your username all the time you can make a ``~/.pypirc``
file, whose contents need only look like::

    [distutils]
    index-servers=pypi

    [pypi]
    repository = https://upload.pypi.org/legacy/
    username = bbeltr1

    [pypitest]
    repository = https://test.pypi.org/legacy/
    username = bbeltr1

Pushing to PyPI
---------------

Copy the included ``setup.py`` file into your own project, and modify the fields
as instructed in the comments.

Now, all that's needed is to run

.. code-block:: bash

    $ python setup.py sdist
    $ twine upload dist/*

and now anyone can ``pip install`` your package!

.. note::

    Every time you run ``python setup.py sdist``, setuptools creates a
    versioned package file in the folder ``dist``. Twine will complain if
    you try to upload the same package version twice to PyPI, so it's
    more typical to actually run something like

    .. code-block:: bash

        $ python setup.py sdist
        $ twine upload dist/package_name-X.Y.Z*

    to specifically upload the version that was just created.

See the section on `versioning <versioneer>`_ for a quick tutorial on how to make
the actual uploading to PyPI happen automatically whenever you bump
the version number, once this initial setup is working.


Author's note
-------------

A common issue I see among scientists is that people are scared to put
half-baked code onto Github or PyPI, and so they keep putting off publishing
their code there until it never happens at all!

Some code is better than no code, as long as your documentation is clear about
its limitations!

Publish your code!
