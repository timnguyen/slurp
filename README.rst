=====
slurp
=====

.. image:: https://secure.travis-ci.org/bninja/slurp.png?branch=master

Slurp iterates over "entries" in log files (sources), parsed them into
something structured and passes them along to something else (sinks). A log
file is something that:

- is created
- has strings appended to it
- is then possibly deleted.

If a file does not conform to this lifestyle it is not suitable for use with
slurp.

In the slurp world files are mapped to consumers which are python dictionaries
describing:

- what files are associated with the consumer
- how to identify raw "entry" strings in them
- how to parse those "entries" to something structured
- where to send those parsed entries

The motivating use-case for slurp is feeding entries streamed to centralized
syslog spool(s) to elastic search and other data mining tools.

Dependencies
------------

- `Python <http://python.org/>`_ >= 2.5, < 3.0
- `pyinotify <https://github.com/seb-m/pyinotify>`_ >= 0.9.3
- `lockfile <http://code.google.com/p/pylockfile/>`_  >= 1.9
- `python-daemon <pypi.python.org/pypi/python-daemon/>`_ >= 1.5

Install
-------

Simply::

    $ pip install slurp
    
or if you prefer::
    
    $ easy_install slurp

Usage
=====

Slurp has both programming and command-line interfaces.

To use the programming interface import it and read doc strings::

    $ python
    >>> import slurp

To use the command-line interface run the slurp script::

    $ slurp --help
    Usage: 
    slurp s|seed path-1 .. path-n [options]
    slurp m|monitor path-1 .. path-n [options]
    slurp e|eat path-1 .. path-n [options]
    
    Options:
      -h, --help            show this help message and exit
      -s STATE_PATH, --state-path=STATE_PATH
      -c CONSUMERS, --consumer=CONSUMERS
      -l LOG_LEVEL, --log-level=LOG_LEVEL
      --enable-syslog       
      --disable-stderrlog   
      -d, --daemonize       
      --disable-locking     
      --lock-timeout=LOCK_TIMEOUT
      --disable-tracking    
      --pid-file=PID_FILE   
      --sink=SINK           
      --batch-size=BATCH_SIZE

Another common use case is to run the slurp script as a monitor daemon. See
extras/slurp.init for an example init script.

Seed
----

Slurp does what it does using three functions: seed, eat and monitor. Seed is
used to initialize offset tracking information for files. These offsets tell
slurp where to resume eating from within the file. This is automatically done
by monitor.

Eat
---

Eat tells slurp to consume any newly added entries appended to tracked files. 

Monitor
-------

Monitor sets up a watch on files and directories and consumes any newly added
entries appended in response to change events trigger by the watch. Slurp uses
pyinotify to watch.

Examples
========

Check it out::

    $ cd ~/code
    $ git checkout git://github.com/bninja/slurp.git
    $ cd slurp
    $ mkvirtualenv slurp
    (slurp)$ python setup.py develop


And run the example::

    $ workon slurp
    (slurp)$ cd ~/code/slurp/extras
    (slurp)$ slurp eat access.log -c example.py -l debug --disable-locking --disable-tracking
