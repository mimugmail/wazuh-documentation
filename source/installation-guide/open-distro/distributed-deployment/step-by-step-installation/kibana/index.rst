.. Copyright (C) 2020 Wazuh, Inc.

.. meta:: :description: Learn how to install Elastic Stack for using Wazuh on Debian

.. _kibana:


Kibana
======

Kibana is a flexible and intuitive web interface for mining and visualizing the events and archives stored in Elasticsearch. 

.. note:: Root user privileges are required to run all the commands described below.

Prerequisites
~~~~~~~~~~~~~

Before installing Kibana, some extra packages must be installed:

.. include:: ../../../../../_templates/installations/elastic/common/before_installation_kibana.rst

Adding the Wazuh repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~

This step is required only if Kibana will be installed on a separate host where Elasticsearch was installed.

.. tabs::

  .. group-tab:: Yum


    .. include:: ../../../../../_templates/installations/wazuh/yum/add_repository.rst



  .. group-tab:: APT


    .. include:: ../../../../../_templates/installations/wazuh/deb/add_repository.rst



  .. group-tab:: Zypp


    .. include:: ../../../../../_templates/installations/wazuh/zypp/add_repository.rst



Kibana installation and configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Install the Kibana package:

    .. tabs::

        .. group-tab:: Yum


            .. include:: ../../../../../_templates/installations/elastic/yum/install_kibana.rst



        .. group-tab:: APT


            .. include:: ../../../../../_templates/installations/elastic/deb/install_kibana.rst



        .. group-tab:: Zypp


            .. include:: ../../../../../_templates/installations/elastic/zypp/install_kibana.rst



#. Download the Kibana configuration file:

    .. include:: ../../../../../_templates/installations/elastic/common/configure_kibana.rst


#. Update the ``optimize`` and ``plugins`` directories permissions:

    .. code-block:: console
    
      # chown -R kibana:kibana /usr/share/kibana/optimize
      # chown -R kibana:kibana /usr/share/kibana/plugins    


#. Install the Wazuh Kibana plugin:

    The installation of the plugin must be done from the Kibana home directory:

    .. code-block:: console

        # cd /usr/share/kibana
        # sudo -u kibana bin/kibana-plugin install https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.0.3_7.9.1-1.zip

#. The next step involves the certificates placement. This guide assumes that a copy of ``certs.tar`` is placed in the root home folder (~/):

    .. include:: ../../../../../_templates/installations/elastic/common/generate_new_kibana_certificates.rst


#. Link Kibana's socket to privileged port 443:

    .. code-block:: console

        # /sbin/setcap 'cap_net_bind_service=+ep' /usr/share/kibana/node/bin/node


#. Enable and start the Kibana service:

    .. include:: ../../../../../_templates/installations/elastic/common/enable_kibana.rst


#. Access the web interface: 

  .. code-block:: none

      URL: https://<kibana_ip>
      user: admin
      password: admin    


Upon the first access to Kibana, the browser shows a warning message stating that the certificate was not issued by a trusted authority. An exception can be added in the advanced options of the web browser or,  for increased security, the ``root-ca.pem`` file previously generated can be imported to the certificate manager of the browser.  Alternatively, a certificate from a trusted authority can be configured. 

It is highly recommended to change Elasticsearch’s default passwords for the users found at the ``/usr/share/elasticsearch/plugins/opendistro_security/securityconfig/internal_users.yml`` file. More information about this process can be found :ref:`here <change_elastic_pass>`.

With the first access attempt, the Wazuh Kibana plugin may prompt a message that indicates that it cannot communicate with the Wazuh API. To solve this issue edit the file ``/usr/share/kibana/optimize/wazuh/config/wazuh.yml`` and replace the ``url`` by the Wazuh server's address: 

.. code-block:: yaml

  hosts:
    - default:
       url: https://localhost
       port: 55000
       username: wazuh
       password: wazuh

Once Kibana is running it is necessary to assign each user its corresponding role. To learn more visit the :ref:`Setting up the Wazuh Kibana plugin <connect_kibana_app>` section. 

To uninstall Kibana, visit the :ref:`uninstalling section <uninstall_kibana>`.

Next steps
~~~~~~~~~~

Once the Wazuh environment is ready, a Wazuh agent can be installed in every endpoint to be monitored. The Wazuh agent installation guide is available for most operating systems and can be found :ref:`here<installation_agents>`.
