.. _rsa:

Remote Service Admin
####################

Pelix/iPOPO now includes an implementation of the
`Remote Service Admin OSGi specification <https://osgi.org/specification/osgi.cmpn/7.0.0/service.remoteserviceadmin.html>`_.
It has been contributed by `Scott Lewis <https://github.com/scottslewis>`_,
leader of the `Eclipse Communication Framework <http://www.eclipse.org/ecf/>`_
project.

This feature can be use to let multiple iPOPO and OSGi frameworks share their
services.
Note that Java is not mandatory when used only between iPOPO frameworks.

.. note:: This is a brand new feature, which might still contain some bugs and
   might not work with all versions of Python (especially 2.7).

   As always, feedback is welcome: don't hesitate to report bugs on
   `GitHub <https://github.com/tcalmant/ipopo/issues>`_.

Links to ECF
============

The Remote Service Admin implementation in iPOPO is based an architecture
similar to the Eclipse Communication Framework (implemented in Java).
Most of the concepts have been kept in the Python implementation, it is
therefore useful to check the documentation of this Eclipse project.

* `ECF project page <http://www.eclipse.org/ecf/>`_, the formal project page
* `ECF wiki <https://wiki.eclipse.org/Eclipse_Communication_Framework_Project>`_,
  where most of the documentation can be found
* `ECF blog <http://eclipseecf.blogspot.com/>`_, providing news and description
  of new features

Some pages of the wiki are related to the links between Java and Python worlds:

* `OSGi R7 Remote Services between Python and Java <https://wiki.eclipse.org/OSGi_R7_Remote_Services_between_Python_and_Java>`_
  describes how to share remote services between an iPOPO Framework and an OSGi
  Framework.

Package description
===================

The implementation of Remote Service Admin is provided by the ``pelix.rsa``
package, which is organized as follows (all names must be prefixed by
``pelix.rsa``):

========================== ====================================================
Module / Package           Description
========================== ====================================================
``edef``                   Definition of the EDEF XML endpoint description format
``endpointdescription``    ``EndpointDescription`` beans
``remoteserviceadmin``     Core implementation of RSA
``shell``                  Shell commands to control/debug RSA
``providers.discovery``    Package of discovery providers
``providers.distribution`` Package of transport providers
``topologymanagers.basic`` Basic implementation of a Topology Manager
========================== ====================================================

Providers included with Pelix/iPOPO
===================================

iPOPO includes some discovery and transport providers. More of them will be
added in future releases.

etcd Discovery
--------------

:Bundle: pelix.rsa.providers.discovery.discovery_etcd
:Requires: *none*
:Libraries: `python-etcd <https://github.com/jplana/python-etcd>`_

This discovery provider uses `etcd <http://etcd.readthedocs.io/en/latest/>`_ as
a store of descriptions of endpoints.
It depends on the `python-etcd <https://github.com/jplana/python-etcd>`_
third-party package.

This discovery provider is instantiated immediately as the bundle is
started. The instance configuration must therefore be given as Framework
properties. Another solution is to kill the ``etcd-endpoint-discovery``
component and restart it with custom properties.

This provider can be configured with the following properties:

======================= ===================================================== =========================================
Property                Default value                                         Description
======================= ===================================================== =========================================
``etcd.hostname``       localhost                                             Address of the etcd server
``etcd.port``           2379                                                  Port of the etcd server
``etcd.toppath``        /org.eclipse.ecf.provider.etcd.EtcdDiscoveryContainer Path in etcd where to store endpoints
``etcd.sessionttl``     30                                                    Session Time To Live
======================= ===================================================== =========================================


XML-RPC Distribution
--------------------

:Bundle: pelix.rsa.providers.transport.xmlrpc
:Requires: HTTP Service
:Libraries: *nothing* (based on the Python Standard Library)

The XML-RPC distribution is the recommended provider for inter-Python
communications.
Note that it also supports communications between Python and Java applications.
Its main advantage is that is doesn't depend on an external library, XML-RPC
being supported by the Python Standard Library.

All components of this provider are automatically instantiated when the bundle
starts.
They can be configured using framework properties or by killing and restarting
its components with custom properties.

============================== ============= ==================================
Property                       Default value Description
============================== ============= ==================================
``ecf.xmlrpc.server.hostname`` localhost     Hostname of the HTTP server (``None`` for auto-detection)
``ecf.xmlrpc.server.path``     /xml-rpc      Path to use in the HTTP server
``ecf.xmlrpc.server.timeout``  30            XML-RPC requests timeout
============================== ============= ==================================

Other properties are available but not presented here as they describe constants
used to mimic the Java side configuration.

A sample usage of this provider can be found in the tutorial section:
:ref:`rsa_tutorial_xmlrpc`.

Py4J Distribution
-----------------

:Bundle: pelix.rsa.providers.transport.py4j
:Requires: HTTP Service
:Libraries: `py4j <https://www.py4j.org/>`_,
   `osgiservicebridge <https://github.com/ECF/Py4j-RemoteServicesProvider>`_

.. note:: This provider works only in Python 3

This provider allows to discover and share a Python service with its Py4J
gateway and vice versa.

It can be configured with the following properties:

================================== ============= ==============================
Property                           Default value Description
================================== ============= ==============================
``ecf.py4j.javaport``              25333         Port of the Java proxy
``ecf.py4j.pythonport``            25334         Port of the Python proxy
``ecf.py4j.defaultservicetimeout`` 30            Timeout before gateway timeout
================================== ============= ==============================

A sample usage of this provider can be found in the tutorial section:
:ref:`rsa_tutorial_py4j`.
