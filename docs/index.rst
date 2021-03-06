GeoHealthCheck
==============

.. toctree::
   :maxdepth: 3

Overview
--------

GeoHealthCheck is a Python application to support monitoring OGC services uptime
and availability.  It can be used to monitor overall health of networks
of resources.

Features
---------

- lightweight
- easy setup
- support for numerous OGC resources
- flexible and customizable: look and feel, scoring matrix
- user management
- database agnostic: any SQLAlchemy supported backend


Installation
-------------

Quick and Dirty
^^^^^^^^^^^^^^^

.. code-block:: bash


  virtualenv ghc && cd ghc
  . scripts/activate
  git clone https://github.com/geopython/GeoHealthCheck.git
  cd GeoHealthCheck
  # install paver dependency for admin tool
  pip install Paver
  # setup app
  paver setup
  # create secret key to use for auth
  paver create_secret_key
  # almost there!  Customize config
  vi instance/config.py
  # edit:
  # - SQLALCHEMY_DATABASE_URI
  # - SECRET_KEY  # from paver create_secret_key
  # - GHC_RETENTION_DAYS
  # - GHC_RUN_FREQUENCY
  # - GHC_SELF_REGISTER
  # - GHC_NOTIFICATIONS
  # - GHC_ADMIN_EMAIL
  # - GHC_SITE_TITLE
  # - GHC_SITE_URL
  # - GHC_SMTP  # if GHC_NOTIFICATIONS is enabled
  
  # init database
  python GeoHealthCheck/models.py create
  # start server
  python GeoHealthCheck/app.py  # http://localhost:8000/



Requirements
^^^^^^^^^^^^

GeoHealthCheck is built on the awesome Flask microframework and uses
Flask-SQLAlchemy for database interaction and Flask-Login for authorization.

OWSLib is used to interact with OGC Web Services.

Install
^^^^^^^

.. note::

  it is strongly recommended to install in a Python ``virtualenv``.
  a ``virtualenv`` is self-contained and provides the flexibility to install / 
  tear down / whatever packages without affecting system wide packages or
  settings.

- Download GeoHealthCheck (releases can be found at
  https://github.com/geopython/GeoHealthCheck/releases)

Running
^^^^^^^

Start using the built-in ``mod_wsgi`` server:

.. code-block:: bash

  python GeoHealthCheck/app.py  # http://localhost:8000
  python GeoHealthCheck/app.py 0.0.0.0:8881  # http://localhost:8881
  python GeoHealthCheck/app.py 192.168.0.105:8957  # http://192.168.0.105:8957


To enable in Apache, use ``GeoHealthCheck.wsgi`` and configure in Apache
as per the main Flask documentation.

Core Concepts
-------------

GeoHealthCheck is built with the following concepts in mind:

- `Resource`: a single, unique endpoint, like an OGC WMS, FTP URL, or plain old
  web link.  A GeoHealthCheck deployment typically monitors numerous resources
- `Run`: the execution and scoring of a test against a `Resource`.  A
  `Resource` may have multiple `Runs`


Configuration
-------------

Core configuration is set by GeoHealthCheck in ``GeoHealthCheck/config.py``.
You can override these settings in ``instance/config.py``:

- **SQLALCHEMY_DATABASE_URI**: the database configuration.  See the
  SQLAlchemy documentation for more info
- **SECRET_KEY**: secret key to set when enabling authentication.  Use
  the output of ``paver create_secret_key`` to set this value
- **GHC_RETENTION_DAYS**: the number of days to keep run history
- **GHC_RUN_FREQUENCY**: cron keyword used to indicate frequency of runs
  (i.e. ``hourly``, ``daily``, ``monthly``)
- **GHC_SELF_REGISTER**: allow registrations from users on the website
- **GHC_NOTIFICATIONS**: turn on email notifications
- **GHC_ADMIN_EMAIL**: email address of administrator / contact
- **GHC_SITE_TITLE**: title used for installation / deployment
- **GHC_SITE_URL**: url of the installation / deployment
- **GHC_SMTP**:  configure SMTP settings if **GHC_NOTIFICATIONS** is enabled
- **GHC_RELIABILITY_MATRIX**: classification scheme for grading resource
  reliability (see below)

Customizing the Score Matrix
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GeoHealthCheck uses a simple matrix to provide an indication of overall health
and / or reliability of a resource.  This matrix drives the CSS which displays
a given resource's state with a colour.  The default matrix is defined as
follows:

.. csv-table::
  :header: low,high,score/colour

  0,49,red
  50,79,orange
  80,100,green

To adjust this matrix, edit **GHC_RELIABILITY_MATRIX** in
``instance/config.py``.

Administration
--------------

- create db
- drop db

User Management
---------------

Adding Resources
----------------

Deleting Resources
------------------

Scheduling Runs
---------------

- cron
- interactive

License
-------

.. include:: ../LICENSE
