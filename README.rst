Django-mmc
==========

.. image:: https://api.travis-ci.org/LPgenerator/django-mmc.png?branch=master
    :alt: Build Status
    :target: https://travis-ci.org/LPgenerator/django-mmc
.. image:: https://pypip.in/v/django-mmc/badge.png
    :alt: Current version on PyPi
    :target: https://crate.io/packages/django-mmc/
.. image:: https://pypip.in/d/django-mmc/badge.png
    :alt: Downloads from PyPi
    :target: https://crate.io/packages/django-mmc/


What's that
-----------
App for monitoring management commands on Django.


Quick installation
------------------
1. Using pip:

.. code-block:: bash

    $  pip install django-mmc


2. Add ``mmc`` application to ``INSTALLED_APPS`` in your settings file

3. Inject management classes before apps will be loaded

.. code-block:: python

    from mmc.mixins import inject_management

    inject_management()


4. Sync database (``./manage.py syncdb`` or ``./manage.py migrate``)

5. Enjoy


Local demo installation
-----------------------

.. code-block:: bash

    $ sudo apt-get install virtualenvwrapper
    $ mkvirtualenv django-mmc
    $ git clone https://github.com/LPgenerator/django-mmc.git
    $ cd django-mmc
    $ python setup.py develop
    $ cd demo
    $ pip install -r ../requirements/dev.txt
    $ python manage.py syncdb
    $ python manage.py test_command >& /dev/null
    $ python manage.py test_command_noargs >& /dev/null
    $ python manage.py test_command_error >& /dev/null
    $ python manage.py test_command_killed >& /dev/null
    $ python manage.py runserver >& /dev/null &
    $ xdg-open http://127.0.0.1:8000/admin/mmc/mmclog/


Configuration
-------------

1. Execute the command, and script will be available at http://127.0.0.1:8000/admin/mmc/mmcscript/
2. Configure script options on script settings http://127.0.0.1:8000/admin/mmc/mmcscript/1/
3. Add email addresses for errors notification http://127.0.0.1:8000/admin/mmc/mmcemail/
4. All logs available on Logs page http://127.0.0.1:8000/admin/mmc/mmclog/
5. If you are using sentry, configure sentry, and all errors will be send into sentry too


Usage without inject
--------------------

If you want track only specified commands manually without auto-inject,
follow to examples below:

.. code-block:: python

    # args
    from mmc.mixins import BaseCommand


    class Command(BaseCommand):
        def handle(self, *args, **options):
            print "OK"


    # noargs
    from mmc.mixins import NoArgsCommand


    class Command(NoArgsCommand):
        def handle_noargs(self, *args, **options):
            print "OK"


When you are using auto-inject, you can use ignore flag on Scripts/Hosts settings.
Logs about execution not be stored, but if you got any error on your commands,
you will be notified to emails.


Cron debug
----------
For debug any messages or some errors on app, run commands with example below:

.. code-block:: bash

    SHELL=/bin/bash
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
    MAILTO=root@localhost
    PYTHON_BIN=/home/user/example.com/venv/bin/python
    MANAGE_PY=/home/user/example.com/www/manage.py
    LOG_FILE=/var/log/mmc.cron.log

    # Project commands
    50 2 * * * $PYTHON_BIN $MANAGE_PY clean >> $LOG_FILE 2>&1


Multi-instance lock
-------------------

If you are using "One copy" functionality, and command is run on multiple
servers, you can use Memcached/Redis lock. Just configure it on settings.py:

.. code-block:: python

    MMC_LOCK_TYPE = 'MemcacheLock'

    MMC_MEMCACHED_CONFIG = {
        'servers': ['127.0.0.1:11211'], 'debug': 0
    }


Management commands
-------------------
``mmc_cleanup`` - clean logs by days/date

``mmc_notify`` - notify users, when script is killed by OS (that actual for long tasks and for big databases)


Publications
------------
* `Completely installation and usage on production <http://habrahabr.ru/post/223151/>`_.


Screenshots
-----------
.. image:: /screenshots/log_changelist.jpg
.. image:: /screenshots/script_changelist.jpg
.. image:: /screenshots/email_changelist.jpg
.. image:: /screenshots/hosts_changelist.jpg


Compatibility:
-------------
* Python: 2.6, 2.7
* Django: 1.3, 1.4, 1.5, 1.6, 1.7
