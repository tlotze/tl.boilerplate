===================================================================================================
tl.boilerplate - Keeping up-to-date with best practices for project layout and software development
===================================================================================================

Rationale
=========

It is common to create software repositories and projects by tools such as ``uv init`` or by using templates
instantiated by, e.g., `cookiecutter <https://github.com/cookiecutter/cookiecutter>`_. These tools and
templates implement an opinionated snapshot at that point in time of the best practices for how to layout a
repository, configure tools and write program files.

Usually, these mechanisms are meant to be applied only once in the beginning of a project. While they may
evolve after that to keep reflecting current best practices, they are not expected to be applied to an
existing project again. It is instead up to the project maintainer to manually integrate updates to the best
practices into the repository. While there are tools to help with this (such as `cruft
<https://github.com/cruft/cruft/>`_ or `Copier <https://github.com/copier-org/copier>`_), I'm not aware at the
time of writing of any that leverage ``git``'s merge capabilities to help with merging changes made by the
project with changes due to the evolution of the template (or, more generally, the mechanism used to create
the boilerplate).

Concept
=======

The basic idea of the ``tl.boilerplate`` project is to have the two lines of evolution happen on two branches
within the same ``git`` repository: As usual, the ``main`` branch starts out with boilerplate generated at the
time of project creation (plus configuration of the boilerplate management) and then accumulates all the
project development. A second branch, by default named ``boilerplate``, starts off the empty root commit,
receives a commit with all the boilerplate, is merged to the ``main`` branch, and later gets all the
boilerplate updates to be merged to ``main``.

Whenever changes to the tools and templates used for generating the original boilerplate are to be integrated,
the mechanism is run to create a new project directory, configured but otherwise empty except for the new
boilerplate. This is committed to the ``boilerplate`` branch, which can then be inspected for updates and
merged into ``main`` in turn. The boilerplate project directory is temporary and will be deleted after
committing if everything goes well. This is achieved using a ``git`` workspace.

Configuration of the update process (including the branch name, default commit message and a script to invoke
the boilerplate generation) as well as any configuration for tools and templates is kept in a directory named
``.boilerplate`` in the repository root. This directory exists on the ``main`` branch but not the
``boilerplate`` branch and changes to it are meant to be committed just before or after merging the updated
boilerplate branch. Thus the developer can work on the configuration and test it without having to touch the
temporary workspace and the ``.boilerplate`` directory will not interfere with the generated content.

Example config
==============

A ``create.sh`` that instantiates a copier template for a python project, moves the answers file to where it
can be version controlled without conflict, and has ``uv`` add its lock file:

.. code:: sh

    copier copy -a .boilerplate/copier-answers.yml gh:tlotze/copier-template-tl-python-package .
    mv .copier-answers.yml .boilerplate/copier-answers.yml
    uv lock
