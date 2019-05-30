.. _doc:

Documenting with Sphinx
=======================

`Sphinx <http://www.sphinx-doc.org>`_ is a tool that helps you easily combine
automatically-generated, docstring-based documentation with custom written
tutorials and example scripts (usually in the form of Jupyter notebooks). It is
the *de facto* standard for Python package documentation.

ReStructuredText
----------------

The markdown language used to combine the various types of documentation into a
cohesive website is `ReStructuredText
<http://docutils.sourceforge.net/docs/user/rst/quickref.html>`_.

RST was originally designed to author complex documents, from books to webpages, and so is has all the complexity of other document creation languages such as LaTeX.

**However**, for the purposes of creating Python documentation, a very simple
subset of the RST language is required.

    1. :ref:`Titles <rst-titles>`
    2. :ref:`Labels & References <rst-labels>`
    3. :ref:`Extension commands (aka "Roles" and "Directives") <rst-directives>`

All other details, such as italics, lists, links, tables, etc. can be easily
looked up in the `RST quickref guide
<http://docutils.sourceforge.net/docs/user/rst/quickref.html>`_.

.. _rst-titles:

Titles
^^^^^^

Titles in Sphinx are handled by simply underlining (and/or overlining) the text with a symbol.
Subsection titles are simply treated by choosing a different symbol.

Sphinx doesn't really care what symbol you use for what section heading level
(it's inferred at document compilation time), but it's good to be consistent.

::

    ==========
    Main title
    ==========

    Intro text goes here.

    Section title
    =============

    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua.

    Subsection title
    ----------------

    Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut
    aliquip ex ea commodo consequat.

    Another Subsection
    ------------------

    Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore
    eu fugiat nulla pariatur.

    Another Section
    ===============

    Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia
    deserunt mollit anim id est laborum.

You can use titles in your docstrings, and when Sphinx includes them in the
documentation, they will be rendered appropriately.


.. _rst-labels:

Labels
^^^^^^

Labels work similarly to LaTeX. They should be unique strings and will be used
within your documentation for cross references, like links.

Two dots, a space, and an underscore mark the start of a label. A colon marks its end.::

    .. _label-text:

    Example title
    -------------

This particular label would be referenced via a link like::

    Anywhere in the text, you can make it so that `this text points to the
    "Example title" above <label-text>`.

Note that any title can be given a label. In particular, this can often be
useful for references specific sections of a long docstring from elsewhere in
the documentation.

.. _rst-directives:

Roles and Directives
^^^^^^^^^^^^^^^^^^^^

Anything that can't be achieved with vanilla restructured text is done via
so-called "directives", which are just extension commands that are understood by
the RST compiler.

The most common examples are things like

- ``image``/``figure``: for including images
- ``code-block``: for including code in various languages with syntax
  highlighting
- ``math``: for including LaTeX-style "display math" using MathJax

The syntax for using a directive is similar to a label. Two dots and space mark
the start of a directive (note no underscore!) and two colons demarcate the end
of the directive's name::

    .. directive-name:: arg1 arg2 arg3
        :named-parameter1: value1
        :named-parameter2: value2

        Text that the directive should be applied to. it
        must be indented. also note the blank link before
        the text starts.

        The text block can span multiple paragraphs, and
        ends when indentation returns to its previous level.

        .. note::

            Directives can be nested like this. Notice the
            simpler syntax for directives that do not
            require any arguments.

The above directive should be thought of as calling a python method with
signature

.. code-block:: python

    def directive_name(a1, a2, a3, named_param1=None, named_param2=None):

In this syntax, the above directive call is equivalent to
:code:`directive_name(arg1, arg2, arg3, named_param1=value1,
named_param2=value2)`. In the RST world, unnamed arguments are called
"arguments" and named arguments are called "options".

There are also inline versions of directives called "Roles". The syntax for
these is much simpler::

    In the middle of a sentence, you can simply use the
    :role-name:`text` syntax for simple things like inline
    code or math.

The most common roles are:

- ``code``: for inline code markup/syntax highlighting
- ``math``: for inline math, analogous to latex's single dollar sign environment
- ``ref`` (Sphinx-specific): for creating links between different documents in
  your documentation. Prefer this over the usual :literal:`link <syntax>`.

The full list of standard RST roles can be found `in the official docs
<http://docutils.sourceforge.net/docs/ref/rst/roles.html>`_.
The extra roles provided by Sphinx are listed `on the Sphinx website
<https://www.sphinx-doc.org/en/master/usage/restructuredtext/roles.html>`_.

The full list of standard RST directives can be found at `in the official
documentation <http://docutils.sourceforge.net/docs/ref/rst/directives.html>`_.
The extensions added by Sphinx are documented `in the Sphinx docs
<https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html>`_.

.. note::

    In addition to forgetting indentation in a directive, another common gotcha
    with RST is that it uses two newlines to separate paragraphs (and also to
    separate different types of markup!). For example, it's easy to forget that
    you need a blank line between a directive name and it's target. Or a blank
    line between the end of a list and the rest of the paragraph.

Sphinx Setup
------------

Canonically, the folder structure of a Python repo looks like

| git-repo-name
|	├── docs
|	│	├── build
|	│	├── make.bat
|	│	├── Makefile
|	│	└── source
|	│...	 ...	├── conf.py
|	│...	 ...	└── index.rst
|	├── LICENSE
|	├── README.md
|	├── requirements.txt
|	└── package_name
|		├── __init__.py
|		├── module.py
|		└── subpackage
|			├── __init__.py
|			└── another_module.py
|

This section will cover how to create and populate the ``docs`` directory.

Thankfully, Sphinx comes with a quick setup tool that makes startup a breeze.

First, :code:`pip install sphinx`. Then, in the top level directory
(``git-repo-name`` above), simply run

.. code-block:: bash

    $ mkdir doc
    $ cd doc
    $ sphinx-quickstart

In response to the prompts:

1. Request a separate "build" and "source" directory to get the folder structure as shown above.
2. Fill in your project's information as requested.
3. When asked what extensions should be installed, I recommended selecting "yes"
   for everything **except** "imgmath". Instead wait for the next option,
   "mathjax", which is basically superior in every way.
4. Everything else can be safely left on the defaults.


Cleaning Up After Sphinx's Auto Setup
-------------------------------------

Sphinx needs the ``conf.py`` file to be able to import your package, so
that it can use the docstrings in its modules' dicts' ``__doc__`` attributes to
create the automatic documentation.

Unfortunately, the ``sphinx-quickstart`` script isn't very smart about this, so
we often need to add a couple of lines near the top of the conf.py script

.. code-block:: python

    import os
    import sys
    sys.path.insert(0, os.path.abspath('..')
    sys.path.insert(0, os.path.abspath('../..')

    import package_name

These paths should point to your package's directory from the perspective of
your "doc" and "source" directories.

I also recommended changing the "version" and "release" strings at the top of
``conf.py`` to point to the appropriate attributes in your module. If you don't
know what that should be, simply use the following (and see the section on
`versioneer <versioneer>`_ for how to set up your git repo to automatically track
version numbers easily (for now, just make sure there's a
:code:`__version__ = 'X.Y.Z'` statement in your package's
``__init__.py`` file.

.. code-block:: python

    version = package_name.__version__
    release = package_name.__version__

Finally, while we're here, let's tell Sphinx what type of docstrings we use, and
ask it to automatically generate an index of all packages, modules, classes,
functions, methods, etc.

.. code-block:: python

    # Autodoc settings
    autosummary_generate = True

    # Napoleon settings
    napoleon_google_docstring = False
    napoleon_numpy_docstring = True

This code can go anywhere below the ``extensions`` definition line in
``conf.py``.

Using Sphinx
------------

Sphinx's build system basically works by eval'ing your ``conf.py`` file,
compiling ``index.rst``, and then looking for any ``.. toc::`` directives and
compiling the rst files that these point to.

For each such ``*.rst`` file we create, Sphinx will create a single webpage.
``index.rst`` will be our default landing page, but other than that, the sky is
the limit in terms of what you can create!

.. note::

    At this point you should be able to follow some examples of using Sphinx,
    for documentation. You can inspect the documentation of any popular Python
    package, like `numpy <https://github.com/numpy/numpy/tree/master/doc>`_, or
    `scipy <https://github.com/scipy/scipy/tree/master/doc>`_, but these
    packages are quite complex. For a more easy to understand example I
    recommend the `seaborn plotting package <https://seaborn.pydata.org/>`_. The
    code for its documentation can be found `on Github
    <https://github.com/mwaskom/seaborn/tree/master/doc>`_.

In what follows, I'll outline the typical structure of my own personal
documentation, which basically boils down to automatically generated API
documentation, and Jupyter notebook (or raw RST pages) that contain tutorials
for different parts of my package. For an example of what this looks like for a
more "realistic" Python project, see `the documentation for the
multi_locus_analysis package <http://multi-locus-analysis.rtfd.io>`_.

Creating a "Page"
^^^^^^^^^^^^^^^^^

While normally label names and reference names are used as described above
(and have no relationship to the name of the file containing the label), Sphinx
finds documentation pages by looking for files whose file name matches a
label referenced by the ``.. toctree::`` directive of your ``index.rst`` file.

In English, what that means is that to add a new page to your documentation
website, you must first add an entry to the toctree, like ``API reference
<api>``, for example. Then you must create a file whose name EXACTLY matches the
label (the bit in angle brackets, so in this case the file must be called
``api.rst``).

Finally, in order for that file to be correctly linked to, make sure that it
has a label on its main title. In the case above, the file would start with
something like::

    .. _api:

    API reference
    =============

Do this for each page you wish to create.

Layout of the website
^^^^^^^^^^^^^^^^^^^^^

I try to use a simple layout for my documentation websites. Namely, my toctree
points to any tutorials that I might have set up for my package, and then
has one final entry for the API documentation. See the code for this website
for an example.

API Docs
^^^^^^^^

The main strength of Sphinx is its ability to understand Python code and
docstrings automagically. However, I would be lying if I didn't admit that most people find the "autosummary module" that comes with sphinx to
be not quite... *auto* ...enough.

But that's no big deal! Some bright people have written an "autoautosummary" extension that is much easier to
use. It's not included in mainline sphinx however, so we'll have to copy
the code into our ``conf.py`` ourselves.

First copy the ``Manual extensions`` section of this package's
``conf.py`` file into your own project, and add ``sphinx.ext.autosummary`` to the
list of required extensions in ``conf.py``. Then, copy the
``doc/source/_templates/autosummary_module.rst`` file from this package into
your own repo.

Now, simply copy the ``api.rst`` file from this repo and create one entry per
module that you want to document!

.. _auto-doc:

ReadTheDocs.org
---------------

Once you have your Sphinx documentation building locally (i.e. ``make html``
works in the ``doc`` directory), then all that's left to get a beautiful website
online for your package is to make an account on `ReadTheDocs.org
<https://readthedocs.org>`_, then follow the instructions to link this account
with your Github account!

