munin.plone
===========
  
Introduction
------------

This package provides munin plugins for monitoring various aspects of a Plone
instance.

It uses `gocept.munin`_ for plugin registration. Please refer to its
documentation if you want to write new plugins.

Plugins
-------

Currently there is 1 plugin available:

* "contentcreation" - reports content creation and modification taken
  from portal_catalog

How to use it
-------------

* First include the package in your buildout `instance` slot::

    [instance]
    ...
    eggs =
        ...
        munin.plone
    zcml =
        ...
        munin.plone

* To create the pluging helper script you'll also need to include the
  following, additional section and extend your `parts` definition::

    [buildout]
    parts =
        ...
        munin

    [munin]
    recipe = zc.recipe.egg
    eggs = munin.plone
    arguments = http_address='${instance:http-address}', user='${instance:user}', plone='plone'

  The `arguments` option is used to pass configuration values to the generated
  helper script, which is then used as the actual munin plugin (see below).
  Any settings for `ip-address`, `http-address`, `port-base` and `user` given
  in the `instance` section should be repeated here, separated by commas.

    .. |---| unicode:: U+2014  .. em dash

  Please be aware, that the variable names use underscores instead of dashes
  here |---| the following list shows all supported settings and their
  respective default values:

  * ip_address='<ip-address>'    ['localhost']
  * http_address=<http-address>  [8080]
  * port_base=<port-base>        [0]
  * user=<user-credentials>      [n.a.]
  * plone=<plone-site-id>        ['plone']

  Either literal values or references to the `instance` part can be used here,
  i.e. "http_address='${instance:http-address}', user='${instance:user}'".
  Please note that the resulting line will be verbosely copied into the
  generated `bin/munin` script, so the extra quoting is required.

* Now you should be able to call the plugins as follow::

    http://localhost:8080/plone/@@munin.plone.plugins/contentcreation

  Where `contentcreation` is you plugin name.  Please note that for security
  reasons the view requires the `View management screens` permission.

* Next you need to make symlinks from the helper script inside your
  buildout's `bin/` to the munin plugin directory.  The helper script itself
  can assist you with this::

    $ bin/munin install /opt/munin/etc/plugins [<prefix>] [<suffix>]

  This will install the necessary symlinks in the given directory using
  either the provided prefix and suffix or else the hostname and current
  directory to assemble their names (see below).

  Alternatively, you may also install the desired symlinks yourself::

    $ cd /opt/munin/etc/plugins
    $ ln -s ~/zope/bin/munin company_contentcreation_site1

  Here `/opt/munin/etc/plugins` is your munin directory, `~/zope/` is the
  root directory of your buildout, `contentcreation` the name of the plugin
  you want to enable, `company` a placeholder for an arbitrary prefix and
  `site1` the name which will be shown in munin.

* Finally configure the plugin in munin (this step can be skipped if you have
  correctly set up the `arguments` option as described in step 2 above)::

    $ cd /opt/munin/etc/plugin-conf.d/
    $ vi munin.plone.conf
    ... [company_*_site1]
    ... env.AUTH myuser:myuser
    ... env.URL http://localhost:8080/plone/@@munin.plone
    .plugins/%s

  Here `myuser:myuser` are your Zope user credentials and `localhost:8080`
  your site url.  Please check `munin`_ for more information about plugin
  configuration.


References
----------

* `munin.plone`_ at pypi
* `gocept.munin`_ at pypi
* `munin`_ project
* `munin exchange`_

  .. _munin.plone: http://pypi.python.org/pypi/munin.plone/
  .. _gocept.munin: http://pypi.python.org/pypi/gocept.munin/
  .. _munin exchange: http://muninexchange.projects.linpro.no/
  .. _munin: http://munin.projects.linpro.no/

Contact
-------

.. image:: http://www.slowfoodbologna.it/redturtle_logo.png

* | Andrew Mleczko <``andrew.mleczko at redturtle.net``>
  | **RedTurtle Technology**, http://www.redturtle.net/

