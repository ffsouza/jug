===========================================
Jug: A Task-Based Parallelization Framework
===========================================

What is Jug?
------------

Jug allows you to write code that is broken up into tasks and run different
tasks on different processors.

It currently has two backends. The first uses the filesystem to communicate
between processes and works correctly over NFS, so you can coordinate processes
on different machines. The second is based on redis so the processes only need
the capability to connect to a common redis server.

Jug also takes care of saving all the intermediate results to the backend in a
way that allows them to be retrieved later.



Examples
--------

Short Example
~~~~~~~~~~~~~

Here is a one minute example. Save the following to a file called ``primes.py``::

    from jug import TaskGenerator
    from time import sleep

    @TaskGenerator
    def is_prime(n):
        sleep(1.)
        for j in xrange(2,n-1):
            if (n % j) == 0:
                return False
        return True

    primes100 = map(is_prime, xrange(2,101))

Of course, this is only for didactical purposes, normally you would use a
better method. Similarly, the ``sleep`` function is so that it does not run too
fast.

Now type ``jug status primes.py`` to get::

    Task name                     Waiting       Ready    Finished     Running
    -------------------------------------------------------------------------
    primes.is_prime                     0          99           0           0
    .........................................................................
    Total:                              0          99           0           0


This tells you that you have 99 tasks called ``primes.is_prime`` ready to run.
So run ``jug execute primes.py &``. You can even run multiple instances in the
background (if you have multiple cores, for example). After starting 4
instances and waiting a few seconds, you can check the status again (with ``jug
status primes.py``)::

    Task name                     Waiting       Ready    Finished     Running
    -------------------------------------------------------------------------
    primes.is_prime                     0          63          32           4
    .........................................................................
    Total:                              0          63          32           4


Now you have 32 tasks finished, 4 running, and 63 still ready. Eventually, they
will all finish and you can inspect the results with ``jug shell primes.py``.
This will give you an ``ipython`` shell. The `primes100` variable is available,
but it is an ugly list of `jug.Task` objects. To get the actual value, you call
the `value` function::

    In [1]: primes100 = value(primes100)

    In [2]: primes100[:10]
    Out[2]: [True, True, False, True, False, True, False, False, False, True]

More Examples
~~~~~~~~~~~~~

There is a worked out example in the `tutorial`_, and another, fully functioning in
the `examples/` directory.

Links
-----

- `Mailing list <http://groups.google.com/group/jug-users>`__
- `Github <http://github.com/luispedro/jug>`__
- `Freshmeat <http://freshmeat.net/projects/jug>`__
- `Documentation <http://packages.python.org/Jug/>`__
- `Homepage <http://luispedro.org/software/jug>`__

How do I get Jug?
-----------------

You can either get the git repository at

`http://github.com/luispedro/jug <http://github.com/luispedro/jug>`__

Or download the package from PyPI_. You can use `easy_instal jug` or `pip
install jug` if you'd like.

.. _PyPI: http://pypi.python.org/pypi/Jug

Documentation Contents
----------------------

.. toctree::
    :maxdepth: 2

    tutorial.rst
    decrypt-example.rst
    text-example.rst
    segmentation-example.rst
    subcommands.rst
    types.rst
    tasks.rst
    tasklets.rst
    invalidate.rst
    idioms.rst
    barrier.rst
    compound.rst
    utilities.rst
    mapreduce.rst
    backends.rst
    configuration.rst
    bash.rst
    faq.rst
    why.rst
    writeabackend.rst
    api.rst

What's New
----------

version **0.9.7** (Tue Feb 18 2014)

- Fix use of numpy subclasses
- Fix redis URL parsing
- Fix ``shell`` for newer versions of IPython
- Correctly fall back on non-sqlite ``status``
- Allow user to call set_jugdir() inside jugfile


version **0.9.6** (Tue Aug 6 2013)

- Faster decoding
- Add jug-execute script
- Add describe() function
- Add write_task_out() function

version **0.9.5** (May 27 2013)

- Added debug mode
- Even better map.reduce.map using blocked access
- Python 3 support
- Documentation improvements

version **0.9.4** (Apr 15 2013)

- Add CustomHash wrapper to set __jug_hash__
- Print traceback on import error
- Exit when no progress is made even with barrier
- Use Tasklets for better jug.mapreduce.map
- Use Ipython debugger if available (patch by Alex Ford)
- Faster --aggressive-unload
- Add currymap() function

version **0.9.3** (Dec 2 2012)

- Fix parsing of ports on redis URL (patch by Alcides Viamontes)
- Make hashing robust to different orders when using randomized hashing
  (patch by Alcides Viamontes)
- Allow regex in invalidate command (patch by Alcides Viamontes)
- Add ``--cache --clear`` suboption to status
- Allow builtin functions for tasks
- Fix status --cache`` (a general bug which seems to be triggered mainly by
  ``bvalue()`` usage).
- Fix ``CompoundTask`` (broken by earlier ``__jug_hash__`` hook introduction)
- Make ``Tasklets`` more flexible by allowing slicing with ``Tasks``
  (previously, slicing with tasks was **not** allowed)

See the file ``ChangeLog`` for the full history

What do I need to run Jug?
---------------------------

It is a Python only package. I have tested it with Python 2.5 and 2.6.
I do not expect Python 2.4 or earlier to work (this is not a priority).
Python 3.0 will not work either (this is expected to change in the 
future---patches are welcome).

How does it work?
-----------------

Read the tutorial_.

.. _tutorial: tutorial.html

What's the status of the project?
---------------------------------

Since version 1.0, jug should be considered stable.

Indices and tables
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
