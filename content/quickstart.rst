Overview & Quickstart
*********************

The Towalink multi-site connectivity solution consists of two parts: the Controller and the Nodes (router devices).
Towalink is built around proven technology like BGP routing (using Bird) and VPN tunnels (WireGuard) to therewith deliver a powerful but still simple connectivity solution. Node configuration is managed using Ansible (securely via ssh over WireGuard).

The Towalink Controller
=======================

The Towalink Controller is the central place of configuration for your Towalink installation. It is also the central place to collect status information and monitoring information from the individual Nodes.

Apart from that, the Controller is not required in operating your Towalink installation. The connectivity solution itself works fully decentrally so that you don't lose connectivity in case the Controller is not available or cannot be reached for whatever reasons. This eases operation considerably compared to other solutions since the Controller does not need to be highly available. Reliability with simplicity is the mantra here.

The Controller software can either be run on-premises or in the public cloud. It is developed on Debian Linux so that Debian is the preferred distribution to use for the Controller.

The Controller keeps a hierarchical configuration: global-level, Site-level, Node-level. The configuration on each level consists of attribute-value pairs (attribute name and attribute value). There are sensible defaults for almost all values so that you can start right away if desired. Configuration from higher levels is inherited towards lower levels unless explicitly overwritten on lower levels.

* global configuration: configuration data that is used for all Sites and Nodes unless explicitly overwritten at a lower level (i.e. Site level or Node level).

* Site configuration: configuration data that is valid for a given Site, i.e. all Nodes at a given Site. Can be overwritten on Node level.

* Node configuration: configuration data that is valid for a given Node.

The first thing to do for a new Towalink installation is to install the Towalink Controller. See the next subchapter on how to do this.

The Towalink Nodes
==================

Towalink Nodes are the individual routers located at the different sites. The Node software can even be run on low-cost hardware like the Raspberry Pi (version 4 is recommended due to the Gigabit-capable Ethernet interface) while still providing considerable connection bandwidths.

Nodes connect to the Towalink Controller via a secure tunnel (a WireGuard tunnel) to gather configuration updates and to deliver status information and monitoring data.

Nodes also connect to other Nodes via point-to-point VPN tunnels (WireGuard tunnels) to securely transport and deliver user traffic. The topology can be configured as needed. A full-mesh topology is created by default, i.e. each Node directly connects to every other Node unless something else is configured.

Alpine Linux and Raspbian are fully supported as operating system for the Nodes. Debian and Ubuntu might work, too, but this is not tested.

Installing the Towalink Controller
==================================

The Towalink Controller needs to be installed on Debian Linux. Use a Debian 10 Buster installation that has access to the Internet to be able to download needed software.

Run the following command as user with root privileges to quickly and conveniently install the Towalink Controller:

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/)

Configuring your Towalink installation
======================================

On the controller, the "tlm" tool (Towalink Manager) is used to configure Sites and Nodes.
In the following, a simple installation that uses defaults is shown to get you started quickly. Please see the other parts of the documentation for any advanced features and for customizing your installation.

Note that the "tlm" tool is installed in a Python virtual environment by default. Thus you need to activate that environment to be able to call "tlm" by issuing:

.. code-block:: shell

   source /opt/towalink/venv/bin/activate

Adding a Site
-------------

To add your first Site (in the example "mysite1"), issue the following command on the Controller:

.. code-block:: shell

   tlm add site mysite1

If this is your first command executed, this creates the configuration directory "/etc/towalink" and also creates default template files. Afterwards the Site is created as specified.

Adding a Node
-------------

To add your first Node (in the example "primary") on your first Site (in the example "mysite1"), issue the following command:

.. code-block:: shell

   tlm add node primary.mysite1

Committing the configuration
----------------------------

Once you added all Sites and Nodes as needed and - if needed - other configuration is done, commit your changes using the following command:

.. code-block:: shell

   tlm commit all

This creates a new config version that can be provisioned to the Nodes.

Attaching the Nodes
-------------------

Now it's time to pair your Nodes with the Controller. This can be done with the following command and only needs to be done once for each Node:

.. code-block:: shell

   tlm attach node primary.mysite1

The Node needs to be active and requesting a configuration by running the Node bootstrap script (execute "bash <(wget -qO- https://install.towalink.net/node/) -v -c <hostname/IP of controller>:8000" on the new device). Follow the steps shown to finish the pairing.

After successful attachment, a WireGuard tunnel is established between Controller and Node. The Node can then be managed using Ansible.

Activating the configuration
----------------------------

The latest configuration can be sent to all the Nodes with the following command:

.. code-block:: shell

   tlm activate all

Manage your Nodes using Ansible
-------------------------------

You can use Ansible and Ansible Playbooks to manage your Nodes like this:

.. code-block:: shell

   tlm ansible node primary.mysite1 all -a whoami
