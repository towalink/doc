Detailled Documentation
***********************


Towalink Overview
=================

The Towalink multi-site connectity solution consists of two parts: the Controller and the Nodes.

The Nodes are the router devices located at the different sites that shall be interconnected with each other. The interconnection is done securely using WireGuard tunnels via any available access. The interconnection topology can be configured flexibly as needed, full-mesh is the default. The Nodes run the Bird routing daemon to provide dynamic routing using BGP. Broken links are detected quickly using BFD. Built around such proven technology, Towalink delivers a powerful but still simple connectivity solution.

The Controller manages all the Nodes conveniently from a central place. It is securely connected to the Nodes via WireGuard links. Node configuration is managed by the Controller using Ansible and rsync (securely via ssh over WireGuard).

Autonomous Operation
--------------------

In most SDN solutions, the Controller acts as the "brain" of the installation and the individual devices are dependent on it. In such a set-up, the Controller needs to be highly-available and needs to be implemented in a cluster or other special set-up to avoid single points of failures.

Towalink is different. The Towalink Controller configures and monitors the individual devices (the Nodes). But apart from that, Nodes operate autonomously. The Nodes store the configuration locally and can operate based on this configuration. Since the configuration is stored persistently, it is still available on Node restart. Since the Controller is thus only needed for Node management, especially performing configuration changes, and monitoring, it does not need to be highly available. This makes operation of the installation way simpler compared to a HA set-up needed for other solutions.

Configuration
-------------

One of the main purposes of Towalink is to provide a convenient way to centrally manage a fleet of routers, especially WireGuard links and Bird on the routers. Towalink has various means to ease the task.

Basically, there are to kinds of configuration on the Controller: configuration files with attribute-value pairs and service configuration files.
* "Configuration files with attribute-value pairs" are files in the "toml" or "yaml" format in which attribute values are assigned to attribute names. Both "toml" and "yaml" are widely used configuration file formats.
* "Service configuration files" are the usual configuration files of services used. They can be customized using Jinja templating. For instance, the main service configuration file for the Bird routing daaemon is "bird.conf.jinja", i.e. the naming convention is the usual service configuration filename with the suffix ".jinja". The attribute-value pairs from the "configuration files with attribute-value pairs" are available for use in the Jinja templates.

Configuration Hierarchy
^^^^^^^^^^^^^^^^^^^^^^^

The configuration is stored under the path "/etc/towalink". Global configuration is stored directly in that folder. Attribute-value pairs are stored in a file named "config.toml" on the Controller. Other files are service configuration files. The Bird configuration is stored in files with the naming pattern "bird*.conf".

Using the Jinja templating, one would already be able to configure individual Nodes flexibly using "if" statements and other means. But this would become more and more confusing with an increasing number of Nodes. Therefore, hierarchical configuration is available.

For each Site, there is a subfolder "/etc/towalink/site_<sitename>". It may contain the same files as the global configuration in "/etc/towalink". Any files present in the subfolder replace files of the global configuration with the same name. Any attribute-value pairs in "config.toml" within the subfolder extend or replace the attribute-value pairs in the "config.toml" of the global configuration.

Similarly, there is a subfolder within each Site folder for each Node: "/etc/towalink/site_<sitename>/node_<nodename>". These subfolders contain a "config.toml" and service configuration files that override attribute-value pairs and files of Site level and global level.

Instead of looking into the "config.toml" files to see the attribute-value pairs you can use the "show" command of the tlm tool on the Controller to display them:

.. code-block:: shell

   tlm show global
   tlm show site <sitename>
   tlm show node <nodeid>|<nodename.sitename>

The complete configuration of a Site or Node that is obtained by evaluating the configuration hierarchy can be displayed using the tlm tool, too. Use the "show_all" command to see the complete attribute-value pairs to be applied on the respective level:

.. code-block:: shell

   tlm show_all site <sitename>
   tlm show_all node <nodeid>|<nodename.sitename>

See the :ref:`chapter_commandreference` for details.



Routing Configuration
---------------------

Each Node runs the Bird routing daemon to implement dynamic routing via BGP and optionally also Babel. It is proven software that is in use by ISPs and other companies all over the world. You have full flexibility to configure Bird as needed. The Bird configuration can be modularized into several files. Using Towalink, each file can either be used globally for all Nodes or provide configuration for specific Sites or specific Nodes. This way you can implement whatever needed while minimizing effort and avoiding copy & paste errors.

Default Routing Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Towalink comes with a default configuration that is providing fault-tolerant dynamic routing for IPv4 and IPv6. Adapt it as desired in case it does not suit your needs.

The default Bird configuration is designed and implemented as follows:

* Each Site represents an own autonomous system.
* Only BGP is used, there is no IGP (this is an unusual configuration but it has some benefits like simplicity).
* Direct eBGP peering is used across WireGuard link between Sites.
* The WireGuard links are supervised using BFD (Bidirectional Forwarding Detection) to detect and act upon broken links.
* BGP peerings are done using a Node-individual IPv4 loopback address (also used as BGP router id) that is also configured on WireGuard point-to-point links.
* BGP peerings between Sites are done directly over the WireGuard links, no BGP multihop.
* All addresses of local interfaces are announced via BGP.
* This already ensures IPv4 reachability. Note that certain IPv4 loopback address become unreachable on link failures. You thus should not do reachability tests with the loopback address as the source address or destination address.
* For IPv6, the WireGuard links only have link local addresses.
* Bird has static IPv6 routes configured to make Node-individual IPv6 loopback addresses reachable via the direct WireGuard links. Note that certain IPv6 loopback address become unreachable on link failures. You thus should not do reachability tests with the loopback address as the source address or destination address.
* "bird.conf" includes multiple other "bird_*.conf" configuration files to make the configuration more modular.
* You may add additional include files as needed (the naming convention "bird_*.conf" needs to be used, however).
* You may overwrite global "bird_*.conf" configuration files on Site level or on Node level as needed.
* You may modify times or any other configuration as needed.
* You may create a completely different configuration if desired (the naming convention "bird_*.conf" needs to be used, however).

Towalink Controller Installation
================================

The Towalink Installer is used to install the Towalink Controller. Doing so is the first step in creating a new Towalink installation.

Environment
-----------

The Towalink Installer can be used on Debian Linux. Use a Debian 10 Buster installation. Installation on Ubuntu might work as well but this is not tested so that issues can be expected.

The Towalink Controller can be run on top of any virtual machine (or physical server), be it within an on-premise installation or in the public cloud (using AWS EC2, Google Compute Engine, Azure Virtual Machines, or other vendors).

The machine needs to have Internet access to be able to download needed software. Later on, the Internet access is needed to connect with the Towalink Nodes on the sites to be connected with each other.

Installation
------------

Run the following command as user with root privileges to quickly and conveniently install the Towalink Controller. It downloads and executes a bash script. The script is a wrapper around an Ansible playbook used to install the Controller. Before executing the playbook, Ansible and git are installed as prerequisites, and the environment is prepared.

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/)

In case you do not have a shell that supports this notation (like ash on Ansible), wrap it into a bash command like this:

.. code-block:: shell

   bash -c "bash <(wget -qO- https://install.towalink.net/controller/)"

Optionally, you may add parameters to control what's happening and to already provide answers to questions that would be asked later (to thus avoid interactive install).

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/) [<parameters...>]

The mentioned parameters can be any command line arguments that are available for an Ansible playbook. The following variables can be provided with the "-e" parameter:

* | "-e input_welcomeprompt=skip"
  | Do not show the welcome prompt.
* | "-e input_monitoring=y" or "-e input_monitoring=n"
  | Specifies whether monitoring shall be installed or not. If not specified, it is asked interactively whether to install or not.
* | "-e python_venv="
  | Do not install within a Python virtual environment. The installation is done in "/opt/towalink/venv" by default.
* | "-e tlm_path=/home/towalink/controller_tlm"
  | Do not install the tlm tool from PyPi but install it locally from the path specified. This is used for development and testing.
