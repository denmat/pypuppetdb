##########
pypuppetdb
##########

pypuppetdtb is a library to work with PuppetDB's REST API. It is implemented
using the requests library.

This library is a thin wrapper around the REST API providing some convinience
functions and objects to request and hold data from PuppetDB.

Installation
============

Since this is still under heavy development there's currently no setup.py
available nor is this package pushed to PyPi. For now, you'll have to install
from source.

.. code-block:: bash

   $ git clone https://github.com/nedap/pypuppetdb
   $ pip install -r requirements.txt

Usage
=====

Once you have pypuppetdb installed you can configure it to connect to PuppetDB
and take it from there.

Connecting
----------

The first thing you need to do is to connect with PuppetDB:

.. code-block:: python

   >>> from pypuppetdb import connect
   >>> db = connect()


Nodes
-----

.. code-block:: python

   >>> nodes = db.nodes()
   >>> for node in nodes:
   >>>   print(node)
   host1
   host2
   ...

This will return a generator object yielding Node objects for every returned
node from PuppetDB.

To query a single node the singular `node()` can be used:

.. code-block:: python

    >>> node = db.node('hostname')
    >>> print(node)
    hostname

Node scope
~~~~~~~~~~

The Node objects are a bit more special in that they can query for facts and
resources themselves. Using those methods from a node object will automatically
add a query to the request scoping the request to the node.

.. code-block:: python

   >>> nodes = db.node('hostname')
   >>> print node.fact('osfamily')
   osfamily/hostname

Facts
-----

.. code-block:: python

   >>> facts = db.facts('osfamily')
   >>> for fact in facts:
   >>>   print(fact)
   osfamily/host1
   osfamily/host2

That queries PuppetDB for the 'osfamily' fact and will yield Fact objects,
one per node this fact is known for.

Resources
---------

.. code-block:: python

   >>> resources = db.resources('file')

Will return a generator object containing all file resources you're managing
across your infrastructure. This is probably a bad idea if you have a big
number of nodes as the response will be huge.

Getting Help
============
This project is still very new so it's not inconceivable you'll run into
issues.

For bug reports you can file an `issue`_. If you need help with something
feel free to hit up `@daenney`_ by e-mail or find him on IRC. He can usually
be found on `IRCnet`_ and `Freenode`_ and idles in #puppet.

.. _issue: https://github.com/nedap/pypuppetdb/issues
.. _@daenney: https://github.com/daenney
.. _IRCnet: http://www.ircnet.org
.. _Freenode: http://freenode.net

Documentation
=============
API documentation is automatically generated from the docstrings using
Sphinx's autodoc feature. 

Documentation will automatically be rebuilt on every push thanks to the
Read The Docs webhook. You can `find it here`_.

.. _find it here: https://pypuppetdb.readthedocs.org/en/latest/

You can build the documentation manually by doing:

.. code-block:: bash

   $ cd docs
   $ make html

Doing so will only work if you have Sphinx installed, which you can acheive
through:

.. code-block:: bash

   $ pip install -r requirements/docs.txt

Contributing
============

We welcome contributions to this library. However, there are a few ground
rules contributors should be aware of.

License
-------
This project is licensed under the Apache v2.0 License. As such, your
contributions, once accepted, are automatically covered by this license.

Commit messages
---------------
Write decent commit messages. Don't use swear words and refrain from
uninformative commit messages as 'fixed typo'.

The preferred format of a commit message:

::

    docs/quickstart: Fixed a typo in the Nodes section.

    If needed, elaborate further on this commit. Feel free to write a
    complete blog post here if that helps us understand what this is
    all about.

    Fixes #4 and resolves #2.

If you'd like a more elaborate guide on how to write and format your commit
messages have a look at this post by `Tim Pope`_.

.. _Tim Pope: http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

Tests
-----
Commits are expected to contain tests or updates to tests if they add to or
modify the current behaviour.

The test suite is powered by `pytest`_ and requires `pytest`_, `pytest-pep8`_,
`httpretty`_ and `pytest-httpretty`_ which will be installed for you if you
run:

.. code-block:: bash

   $ pip install -r requirements/tests.txt

.. _pytest: http://pytest.org/latest/
.. _pytest-pep8: https://pypi.python.org/pypi/pytest-pep8
.. _httpretty: https://pypi.python.org/pypi/httpretty/
.. _pytest-httpretty: https://github.com/papaeye/pytest-httpretty

To run the unit tests (the ones that don't require a live PuppetDB):

.. code-block:: bash
   
   $ py.test -v -m unit

To run the intergration tests:

.. code-block:: bash

   $ py.test -v -m integration

The integration tests require a running instance of PuppetDB listening without
SSL on http://localhost:8080 with the provided test data.

Have a look at the ``puppetdb_travis.sh`` in ``ext/`` on how we do this. The
script will probably work with little modifications for you locally if you have
`Java`_ and `Leiningen`_ installed and found within your ``$PATH``.

If the tests pass, you're golden. If not we'll have to figure out why and
fix that. Feel free to ask for help on this.

.. _Java: http://www.java.com/en/
.. _Leiningen: https://github.com/technomancy/leiningen/wiki/Packaging