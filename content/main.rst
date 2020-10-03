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

The "complete configuration" of a Site or Node that is obtained by evaluating the configuration hierarchy can be displayed using the tlm tool, too. Use the "show_all" command to see the complete attribute-value pairs to be applied on the respective level:

.. code-block:: shell

   tlm show_all site <sitename>
   tlm show_all node <nodeid>|<nodename.sitename>

See the :ref:`chapter_commandreference` for details.

Generated Configuration
^^^^^^^^^^^^^^^^^^^^^^^

A "generated configuration" is created and updated automatically and stored in "/etc/towalink/generated/config.toml". It only contains attribute-value pairs, no service configuration files. For instance, it stores encryption keys for the VPN links to be created between Nodes. You can modify this configuration, but there is usually no need to do so.

Effective Configuration
^^^^^^^^^^^^^^^^^^^^^^^

The "effective configuration" is created for each node based on the "complete configuration" (obtained by evaluating the configuration hierarchy) and the "generated configuration". It is stored below "/etc/towalink/effective". This is the configuration that gets provisioned towards the individual Nodes.

For each Node, a yaml file with attribute-value pairs is stored. By convention, it contains some generic attributes and the needed attributes for each service. For instance, attributes for the Bird routing daemon are prefixed by "bird\_". Additionally, the service configuration files (from the complete configuration) are available. All service configuration files that are ".jinja" template files are processed using the yaml file with the attribute-value pairs.

The "effective configuration" is versioned, i.e. there can be multiple config versions for each Node. Each time, "tlm commit..." is executed for a Node and there are configuration changes, a new config version is created.

.. code-block:: shell

   tlm commit all
   tlm commit site <sitename>
   tlm commit node <nodeid>|<nodename.sitename>

All config versions of a Node are mirrored to the respective Node. Note that commiting the configuration and mirroring it to the Node does not activate the new configuration on the Node. This is done separately.

Using the "tlm activate..." command, one of the available configuration versions can be activated on one or many Nodes. For instance, the following command activated the latest configuration on each Node:

.. code-block:: shell

   tlm activate all

See the :ref:`chapter_commandreference` for details and information on more fine-grained control of configuration activation.

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

The Towalink Controller can be run on top of any virtual machine (or physical server), be it within an on-premise installation or in the public cloud (using AWS EC2, Google Compute Engine, Azure Virtual Machines, or other vendors). Running it in a container is currently not supported since WireGuard support on the host would be required which cannot be installed automatically.

The machine needs to have Internet access to be able to download needed software. Later on, the Internet access is needed to connect with the Towalink Nodes on the sites to be connected with each other.

Installation
------------

Run the following command as user with root privileges to quickly and conveniently install the Towalink Controller. It downloads and executes a bash script. The script is a wrapper around an Ansible playbook used to install the Controller. Before executing the playbook, Ansible and git are installed as prerequisites, and the environment is prepared.

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/)

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

Parameters used for development and testing purposes:

* | "-e pip_test=yes"
  | Do install the Towalink module from the PyPi test repository and not from the main repository.
* | "-e tlm_path=/home/towalink/controller_tlm"
  | Do not install the tlm tool from PyPi but install it locally from the path specified.


Using the Towalink Controller
=============================

The Towalink Controller is used for managing your Towalink installation conveniently from a central place.

The tlm Tool
------------

The "tlm" tool is used to manage your Towalink installation. It was installed during the installation of the Towalink Controller as described above.

In case you installed using the default parameters, the "tlm" tool was installed in a Python virtual environment. Therefore you need to enter this environment before using the "tlm" tool. This is done using the following command:

.. code-block:: shell

   source /opt/towalink/venv/bin/activate

Afterwards, you can call the "tlm" tool with the parameters needed. Here is the generic usage - but just see the next sections on practical examples:

.. code-block:: shell

   tlm [-?|--help] [-l|--loglevel debug|info|error] <operation> <entity> [<arguments...>]

Creating Site and Node Configuration
------------------------------------

Use the "tlm" tool to create new Sites as needed using the "add site" operation:

.. code-block:: shell

   tlm add site mysite1

Similarly, you can create Nodes within the Sites as needed using the "add node" operation:

.. code-block:: shell

   tlm add node primary.mysite1

Each Node gets a Node identifier assigned. Use either this Node identifier (<nodeid>) or the notation "<nodename>.<sitename>" to identify a Node when using the "tlm" tool.

Attaching Nodes
---------------

New Nodes need the be "paired" with the Towalink Controller. This is called "Node attachment". This is a one-time process for each new Node. Once the attachment is done, the Node maintains a WireGuard tunnel to the Controller. Via this tunnel, the Node is managed by the controller in a secure manner.

Firewall
^^^^^^^^

The "tlm" tool starts a web server on port 8000 to receive configuration requests from the unconfigured  Nodes. This web server needs to be reachable for the Nodes. The port 8000/tcp only needs to be open during the Node attachment process.

Server Certificate
^^^^^^^^^^^^^^^^^^

The web server of the "tlm" tool that is used for attaching the nodes requires an SSL certificate. The private key is expected in "/etc/towalink/certs/key.pem", the public key in "/etc/towalink/certs/server.pem".

A certificate can be created using openssl, for instance:

.. code-block:: shell
   openssl req -new -x509 -keyout /etc/towalink/certs/key.pem -out /etc/towalink/certs/server.pem

Note that the common name of the certificate needs to match the hostname of the Towalink Controller.

Be aware that the Nodes need to be able to verify the certificate chain for a successful attach. In case of a self-signed certificate, you thus need to provide the CA certificate or the server certificate to the Nodes.

Name Resolution
^^^^^^^^^^^^^^^

You need to make sure that the DNS name resolution is working properly on your Towalink Controller. This means that the Controller's hostname needs to resolve to the Controller's IP address (entry in "/etc/hosts" for the hostname is not just 127.0.0.1 but the interface IP address). In addition, the Controller needs to be able to resolve the hostnames of the Nodes to be attached to the Nodes' IP addresses using which the Nodes can be reached.

.. attention::
   If name resolution is not working properly, you won't be able to attach Nodes successfully!

Attachment Process
^^^^^^^^^^^^^^^^^^

The attachment process is initiated by the following command:

.. code-block:: shell

   tlm attach node <nodeid>|<nodename.sitename>

Once started, the "tlm" tool starts the web server and collects Nodes' connection attempts for 20 seconds. Afterwards you select which of these Nodes to configure or interrupt the process.

The next connection attempt of the selected Node provides data on the Node and is answered with needed configuration information to establish the WireGuard management tunnel for that Node. Once the data has been exchanged, the "tlm" tool configures the WireGuard management tunnel endpoint on the Controller and waits for the management tunnel to come up.

Once the management tunnel is working, the "tlm" tool prepares the new Node using an Ansible playbook. Ansible connects securely via ssh over the WireGuard management tunnel. The "tlm" tool prints the Ansible playbook command that is used to prepare the Node so that you can reissue the command in the case of problems.

After the Node has been prepared using Ansible, the attachment process is finished. You now can manage the Node using the "tlm" tool.

Managing Nodes
--------------

The Node configuration is managed directly using the "tlm". Any other management tasks regarding the Nodes are done using Ansible. You can use any Ansible functionality (Ansible and Ansible Playbooks) to do this.

Depending on the "tlm" command, either a single Node, all Nodes of a Site, or the whole installation can be targeted. See the :ref:`chapter_commandreference` for details.


Using Ansible
^^^^^^^^^^^^^

Ansible and Ansible Playbooks can be called using the following commands:

.. code-block:: shell

   tlm ansible node <nodeid>|<nodename.sitename> <ansible arguments...>

.. code-block:: shell

   tlm ansible_playbook node <nodeid>|<nodename.sitename> <ansible_playbook arguments...>
   
   
"tlm ansible" and "tlm ansible_playbook" are just wrappers around Ansible and Ansible Playbooks, respectively. These wrappers add inventory information so that the Node(s)/Site(s) as specified are targeted.

Preparing and Mirroring Node Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The "tlm commit" updated the effective configuration. In case the configuration changed compared to the previous one, a new configuration version is created. All available configuration versions are then mirrored to the respective Node so that the configuration becomes available there. Note that a changed configuration is not applied with this command.

.. code-block:: shell

   tlm commit node <nodeid>|<nodename.sitename>

Activating Node Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use the "tlm activate" operation to apply a certain configuration version:

.. code-block:: shell

   tlm activate node <nodeid>|<nodename.sitename> [<version>]

If no version is specified, the latest configuration version is activated.

A symbolic link to the requested config version is set on the Node. Afterwards the changed configuration files are copied to their needed location. Finally, all services with changed configuration files are reloaded or restarted. Then the requested config version is active on the Node.

Preparing Towalink Nodes
========================

New Towalink Nodes can be easily prepared for central management by your Towalink Controller.

Environment
-----------

There are currently two operating systems that are tested and known to work:

* Alpine Linux (at time of writing v3.12 is current)
* Raspberry Pi OS (previously called "Raspbian") (based on Debian v10 Buster)

Alpine Linux uses the apk packet manager, Raspberry Pi OS the apt packet manager. Distributions based on the yum package manager (e.g. CentOS) are not yet supported.

Preparation
-----------

You need to make sure that the operating system is properly installed and that you have working Internet access on the primary network interface.

Check that the Node's hostname is properly set:

.. code-block:: shell

  hostname -f

This command must return the fully qualified domain name of the Node. If this is not yet the case in your installation, you usually need to correct the "/etc/hosts" file.

It is important that name resolution works properly. This means that DNS hostnames need to be resolvable towards their corresponding IP address. Make sure that the hostname of the Node itself resolves to the Node's (public) IP address and not to localhost (127.0.0.1).

Bootstrap Script
----------------

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/node/) -v -c <hostname/IP of controller>:8000

as root



In case you do not have a shell that supports this notation (like ash on Alpine), wrap it into a bash command like this:

.. code-block:: shell

   apk add bash
   bash -c "bash <(wget -qO- https://install.towalink.net/node/) -v -c <hostname/IP of controller>:8000"


Certificate Chain
^^^^^^^^^^^^^^^^^

Certificate on Node expected in
    /etc/towalink/bootstrap/cacert.pem
    otherwise: Bootstrap config download failed with http response 00060. Ignoring and continuing

    
Frequently Asked Questions
==========================

The following sections provide answers to questions that have been raised towards the Towalink project community.

Why is this project called "Towalink"?
--------------------------------------

"towa" is Japanese for "forever", "permanent". "link" relates to the English network term: "having a link", "being connected".
The "Towalink" therewith relates to its target: providing reliable connectivity.
