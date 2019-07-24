.. _building_documentation:

-------------------------------------------------------------------------------
Building documentation
-------------------------------------------------------------------------------

Tarantool documentation is built using a simplified markup system named ``Sphinx``
(see http://sphinx-doc.org). You can build a local version of this documentation
and you can contribute to Tarantool's version.

You can either install all necessary software to build the documentation on your
local machine or in docker container by using our preinstalled building
environment.

===============================================================================
                          Building in docker container
===============================================================================

You need to install these packages:


  * ``docker``
  * ``git`` (optional)

Then you need to clone a git repository with Tarantool documentation source codes
to your local folder:

.. code-block:: console

  $ git clone https://github.com/tarantool/doc.git ~/tarantool-doc

.. NOTE::

    If you don't have ``git`` you can download sources via web-browser - just visit
    `This web page <https://github.com/tarantool/doc>`_ and press ``Clone or download``
    and then ``Download ZIP`` button. In that case you should unzip the archive
    to ~/tarantool-doc or any other desired folder.

Go to the Tarantool documentaition directory on your local system:

.. code-block:: console

  $ cd ~/tarantool-doc

Use provided Dockerfile to build a docker image for building Tarantool
documentation by using following command:

.. code-block:: console

  $ docker build -t doc-builder:2 .

.. NOTE::

    Note that last command has a space followed by a dot ``.`` at the very end.

Now that we have the builder image ready lets start by building a ``Makefile``.

.. code-block:: console

  $ docker run --rm -it -v $(pwd):/doc doc-builder:2 sh -c "cmake ."

Finaly to build the documentaion just use the following command from a folder with
Tarantool documentation source codes:

.. code-block:: console

  $ docker run --rm -it -v $(pwd):/doc doc-builder:2 sh -c "make sphinx-pdf"

.. NOTE::

    Note that after keyword make there is a parameter, defining an output format
    of the documentation building process. To see possible variants of output
    (including localisation and file format)
    you can use the ``make help`` at the end of this command. Alternatively just
    open the generated Makefile in the local Tarantool documentaition folder to
    see all possible build targets.

If you followed up this instructions you should see a process of building requested
documentation. At the end of this process you will see a message about
generated output files. For example:

.. code-block:: console

  Output written on Tarantool.pdf

===============================================================================
                          Building on local machine
===============================================================================

You need to install these packages:

* ``git`` (a program for downloading source repositories)
* ``CMake`` version 2.8 or later (a program for managing the build process)
* ``Python`` version greater than 2.6 -- preferably 2.7 -- and less than 3.0
  (Sphinx is a Python-based tool)
* ``LaTeX`` (a system for document preparation; the installable
  package name usually begins with the word 'texlive' or 'tetex', on Ubuntu
  the name is 'texlive-latex-base')
* ``ImageMagick`` (a system for image conversion; on MacOS install it using
  ``brew``)

You need to install these Python modules:

* `pip <https://pypi.python.org/pypi/pip>`_, any version
* `Sphinx <https://pypi.python.org/pypi/Sphinx>`_ version 1.4.4 or later

  .. NOTE::

      If you encounter the "Missing SPHINX_EXECUTABLE" error message on Mac, manually
      export the PATH variable:

      .. code-block:: console

          export PATH=$PATH:/User/user_name/Library/Python/2.7/bin

* `sphinx-intl <https://pypi.python.org/pypi/sphinx-intl>`_ version 0.9.9

  .. NOTE::

      If you encounter the "Missing SPHINX_INTL_DIR" error message on Mac, manually
      export the SPHINX_INTL_DIR variable:

      .. code-block:: console

          export SPHINX_INTL_DIR=/User/user_name/Library/Python/2.7/bin

* `lupa <https://pypi.python.org/pypi/lupa>`_ -- any version

.. NOTE::

    You should specify ``--user`` flag on Mac while installing Python modules for correct
    installation.

See more details about installation in the :ref:`build-from-source <building_from_source>`
section of this documentation.

1. Use ``git`` to download the latest source code of this documentation from the
   GitHub repository ``tarantool/doc``, branch 1.10. For example, to download to a local
   directory named ``~/tarantool-doc``:

   .. code-block:: console

       $ git clone https://github.com/tarantool/doc.git ~/tarantool-doc

2. Use ``CMake`` to initiate the build.

   .. code-block:: console

       $ cd ~/tarantool-doc
       $ make clean         # unnecessary, added for good luck
       $ rm CMakeCache.txt  # unnecessary, added for good luck
       $ cmake .            # initiate

3. Build a local version of the documentation.

   Run the ``make`` command with an appropriate option to specify which
   documentation version to build.

   .. code-block:: console

       $ cd ~/tarantool-doc
       $ make sphinx-html           # multi-page English version
       $ make sphinx-singlehtml     # one-page English version
       $ make sphinx-html-ru        # multi-page Russian version
       $ make sphinx-singlehtml-ru  # one-page Russian version
       $ make all                   # all versions plus the entire web-site

   Documentation will be created in subdirectories of ``/output``:

   * ``/output/en`` (files of the English version)
   * ``/output/ru`` (files of the Russian version)

   The entry point for each version is the ``index.html`` file in the appropriate
   directory.

4. Set up a web server.

   * One way is to say ``make sphinx-webserver``.
     This will set up and run the web server on port ``8000``:

     .. code-block:: console

         $ cd ~/tarantool-doc
         $ make sphinx-html       # as an example, build the multi-page English documentation
         $ make sphinx-webserver  # set up and run the web server

     In case port ``8000`` is already in use, you can specify any other port
     number that is bigger than ``1000`` in the ``tarantool-doc/CMakeLists.txt``
     file (search it for the ``sphinx-webserver`` target) and rebuild cmake
     files:

     .. code-block:: console

         $ cd ~/tarantool-doc
         $ git clean -qfxd        # clean up old cmake files
         $ cmake .                # rebuild cmake files
         $ make sphinx-html       # as an example, build the multi-page English documentation
         $ make sphinx-webserver  # set up and run the web server on the custom port

     Or you can release the port:

     .. code-block:: console

         $ sudo lsof -i :8000  # get the process ID (PID)
         COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
         Python 19516 user 3u IPv4 0xe7f8gc6be1b43c7 0t0 TCP *:irdmi (LISTEN)
         $ sudo kill -9 19516  # kill the process

   * The other way is to run the built-in web server in Python.
     Make sure to run it from the documentation ``output`` folder:

     .. code-block:: console

         $ cd ~/tarantool-doc/output
         $ python -m SimpleHTTPServer 8000

     In case port ``8000`` is already in use, you can specify any other port
     number that is bigger than ``1000``.

5. Open your browser and enter ``127.0.0.1:8000/en/doc/1.10/`` into the address
   box (or ``127.0.0.1:8000/ru/doc/1.10/`` if you built the Russian documentation).
   Mind the trailing slash "/" in the address string.

   If your local documentation build is valid, the manual will appear in
   the browser.

===============================================================================
                      Common guidelines for documentation
===============================================================================

To contribute to documentation, use the
`REST <http://docutils.sourceforge.net/docs/user/rst/quickstart.html>`_
format for drafting and submit your updates as a
`pull request <https://help.github.com/articles/creating-a-pull-request/>`_
via GitHub.

To comply with the writing and formatting style, use the
:ref:`guidelines <documentation_guidelines>` provided in the documentation,
common sense and existing documents.

.. NOTE::

    * If you suggest creating a new documentation section (a whole new
      page), it has to be saved to the relevant section at GitHub.

    * If you want to contribute to localizing this documentation (for example into
      Russian), add your translation strings to ``.po`` files stored in the
      corresponding locale directory (for example ``/locale/ru/LC_MESSAGES/``
      for Russian). See more about localizing with Sphinx at
      http://www.sphinx-doc.org/en/stable/intl.html

