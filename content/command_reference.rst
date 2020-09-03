Command Reference
*****************

Towalink Controller installation
================================

The Towalink Installer is used to install the Towalink Controller. Doing so is the first step in creating a new Towalink installation.

Environment
-----------

The Towalink Installer can be used on Debian Linux or Alpine Linux. Debian Linux is preferred since only a limited feature set is supported on Alpine. Use a Debian 10 Buster installation. Installation on Ubuntu might work as well but this is not tested so that issues can be expected.

The Towalink Controller can be run on top of any virtual machine (or physical server), be it within an on-premise installation or in the public cloud (using AWS EC2, Google Compute Engine, Azure Virtual Machines, etc.).

The machine needs to have Internet access to be able to download needed software. Later on, the Internet access is needed to connect with the Towalink Nodes on the sites to be connected with each other.

Installation
------------

Run the following command as user with root privileges to quickly and conveniently install the Towalink Controller:

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/)

Optionally, you may add parameters to control what's happening and to already provide answers to questions that are asked later (to thus avoid interactive install).

.. code-block:: shell

   bash <(wget -qO- https://install.towalink.net/controller/) [<parameters...>]

The script is a wrapper around an Ansible playbook used to install the Controller. Before executing the playbook, Ansible and git are installed as prerequisites, and the environment is prepared.

The mentioned parameters can be any command line arguments that are available for an Ansible playbook. The following variables can be provided with the "-e" parameter:

* | "-e input_welcomeprompt=skip"
  | Do not show the welcome prompt.
* | "-e input_monitoring=y" or "-e input_monitoring=n"
  | Specifies whether monitoring shall be installed or not. If not specified, it is asked interactively whether to install or not.
* | "-e python_venv="
  | Do not install within a Python virtual environment. The installation is done in "/opt/towalink/venv" by default.
* | "-e tlm_path=/home/towalink/controller_tlm"
  | Do not install the tlm tool from PyPi but install it locally from the path specified. This is used for development and testing.

"tlm" tool on the Towalink Controller
=====================================

"tlm" is the interface for configuring your Towalink installation. You can use it to modify the configuration files and to trigger actions.

Listing entities
----------------

Listing Sites
^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm list sites
    
Parameters:

   no parameters

Example:

.. code-block:: shell

   tlm list sites

Listing Nodes
^^^^^^^^^^^^^
    
Command:
    
.. code-block:: shell

   tlm list nodes all|<sitename>

Parameters:

   <sitename>: Name of the Site whose Nodes shall be shown; "all" for Nodes of all Sites

Examples:

.. code-block:: shell

   tlm list nodes all
   tlm list nodes mysite1


Showing the configuration
-------------------------

Showing the global configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm show global
    
Parameters:

   no parameters

Example:

.. code-block:: shell

   tlm show global

Showing the Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm show site <sitename>
    
Parameters:

   <sitename>: Name of the Site whose configuration shall be displayed

Example:

.. code-block:: shell

   tlm show site mysite1

Showing the Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm show node <nodeid>|<nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:

.. code-block:: shell

   tlm show node 15
   tlm show node primary.mysite1

Showing the complete Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm show_all site <sitename>
    
Parameters:

   <sitename>: Name of the Site whose complete configuration shall be displayed

Example:

.. code-block:: shell

   tlm show_all site mysite1

Showing the complete Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm show_all node <nodeid>|<nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:

.. code-block:: shell

   tlm show_all node 15
   tlm show_all node primary.mysite1


Creating and removing entities
------------------------------

Creating a Site
^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm add|create site <sitename>
    
Parameters:

   <sitename>: Name of the Site that shall be added

Example:

.. code-block:: shell

   tlm add site mysite1

Creating a Node
^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm add|create node <nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

.. code-block:: shell

   tlm add node primary.mysite1

Removing a Site
^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm del|remove site <sitename>
    
Parameters:

   <sitename>: Name of the Site that shall be added

Example:

.. code-block:: shell

   tlm del site mysite1

Removing a Node
^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm remove node <nodeid>|<nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:

.. code-block:: shell

   tlm del node 15
   tlm del node primary.mysite1


Modifying the configuration
---------------------------

Setting the global configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm set global <attr> <value>
    
Parameters:

   <attr>: Name of the attribute that shall be set
   <value>: New value for the attribute; special value "empty" to remove the attribute

Example:

.. code-block:: shell

   tlm set global wg_keepalive 25

Setting the Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm set site <sitename> <attr> <value>
    
Parameters:

   <sitename>: Name of the Site whose configuration shall be changed
   <attr>: Name of the attribute that shall be set
   <value>: New value for the attribute; special value "empty" to remove the attribute

Example:

.. code-block:: shell

   tlm set site mysite1 wg_keepalive 25

Setting the Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm set node <nodeid>|<nodename.sitename> <attr> <value>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)
   <attr>: Name of the attribute that shall be set
   <value>: New value for the attribute; special value "empty" to remove the attribute    

Example:

.. code-block:: shell

   tlm set node 15 wg_keepalive 25
   tlm set node primary.mysite1 wg_keepalive 25


Config change management
------------------------

List Nodes with changed configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm list changed
    
Parameters:

   no parameters

Example:

.. code-block:: shell

   tlm list changed

Commit configuration of all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm commit all
    
Parameters:

   no parameters

Example:

.. code-block:: shell

   tlm commit all

Commit the configuration of a Site's Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm commit site <sitename>
    
Parameters:

   <sitename>: Name of the Site whose configuration shall be committed

Example:

.. code-block:: shell

   tlm commit site mysite1

Commit the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm commit node <nodeid>|<nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

.. code-block:: shell

   tlm commit node 15
   tlm commit node primary.mysite1


Activate Node configuration
---------------------------

Activate the latest configuration on all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm activate all
    
Parameters:

   no parameters

Example:

.. code-block:: shell

   tlm activate all

Activate the configuration on all Nodes of a Site
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm activate site <sitename> [<version>]
    
Parameters:

   <sitename>: Name of the Site whose configuration shall be activated
   <version>: Version number of the configuration; "latest" is default

Examples:

.. code-block:: shell

   tlm activate site mysite1
   tlm activate site mysite1 v5

Activate the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm activate node <nodeid>|<nodename.sitename> <version>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)
   <version>: Version number of the configuration; "latest" is default    

Example:

.. code-block:: shell

   tlm activate node 15
   tlm activate node 15 v5
   tlm activate node 15 latest    
   tlm activate node primary.mysite1
   tlm activate node primary.mysite1 v5
   tlm activate node primary.mysite1 latest

Pairing Nodes
-------------

Attach a Node configuration to a physical device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm attach node <nodeid>|<nodename.sitename>
    
Parameters:

   <nodeid>: numeric node identifier
   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

.. code-block:: shell

   tlm attach node primary.mysite1


Setting the debug level
-----------------------

You may configure the verbosity of the command by setting the log level parameter to the desired value.

.. code-block:: shell

   tlm --loglevel <loglevel> ...

Parameters:

   <loglevel>: Valid values are "debug", "info", "warning", and "error".

Example:

.. code-block:: shell

   tlm --loglevel debug list sites
