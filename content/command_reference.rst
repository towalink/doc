.. _chapter_commandreference:

Command Reference
*****************

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

   <sitename>: name of the Site whose Nodes shall be shown; "all" for Nodes of all Sites

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

   <sitename>: name of the Site whose configuration shall be displayed

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

   <sitename>: name of the Site whose complete configuration shall be displayed

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

   <sitename>: name of the Site that shall be added

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

   <sitename>: name of the Site that shall be added

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

   <attr>: name of the attribute that shall be set

   <value>: new value for the attribute; special value "empty" to remove the attribute

Example:

.. code-block:: shell

   tlm set global wg_keepalive 25

Setting the Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm set site <sitename> <attr> <value>
    
Parameters:

   <sitename>: name of the Site whose configuration shall be changed

   <attr>: name of the attribute that shall be set

   <value>: new value for the attribute; special value "empty" to remove the attribute

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

   <attr>: name of the attribute that shall be set

   <value>: new value for the attribute; special value "empty" to remove the attribute    

Example:

.. code-block:: shell

   tlm set node 15 wg_keepalive 25
   tlm set node primary.mysite1 wg_keepalive 25
   tlm set node main.hubsite groups '[ "hubs" ]'


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

   tlm [-m <message>] commit all
    
Parameters:

   <message>: commit message

Example:

.. code-block:: shell

   tlm -m "Change debug level" commit all

Commit the configuration of a Site's Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm [-m <message>] commit site <sitename>
    
Parameters:

   <message>: commit message

   <sitename>: name of the Site whose configuration shall be committed

Example:

.. code-block:: shell

   tlm -m "Change debug level" commit site mysite1

Commit the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm [-m <message>] commit node <nodeid>|<nodename.sitename>
    
Parameters:

   <message>: commit message

   <nodeid>: numeric node identifier

   <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

.. code-block:: shell

   tlm -m "Change debug level" commit node 15
   tlm -m "Change debug level" commit node primary.mysite1


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

   <sitename>: name of the Site whose configuration shall be activated

   <version>: version number of the configuration; "latest" is default

Examples:

.. code-block:: shell

   tlm activate site mysite1
   tlm activate site mysite1 v5

Activate the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm activate node <nodeid>|<nodename.sitename> <version>
    
Parameters:

   <nodeid>: numeric node identifier

   <nodename.sitename>: name of the Node within the given Site (identified by its name)

   <version>: version number of the configuration; "latest" is default    

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

Executing Ansible for Node(s)
-----------------------------

Executing Ansible for all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible all <ansible arguments...>
    
Parameters:

   <ansible arguments...>: arguments for Ansible

Example:

.. code-block:: shell

   tlm ansible all all -a whoami

Executing Ansible for all Nodes of a Site
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible site <sitename> <ansible arguments...>
    
Parameters:

   <sitename>: name of the Site for whose Nodes Ansible shall be called

   <ansible arguments...>: arguments for Ansible

Examples:

.. code-block:: shell

   tlm ansible site mysite1 all -a whoami

Executing Ansible for a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible node <nodeid>|<nodename.sitename> <ansible arguments...>
    
Parameters:

   <nodeid>: numeric node identifier

   <nodename.sitename>: name of the Node within the given Site (identified by its name)

   <ansible arguments...>: arguments for Ansible

Example:

.. code-block:: shell

   tlm ansible node 15 all -a whoami
   tlm ansible node primary.mysite1 all -a whoami

Executing Ansible Playbook for Node(s)
--------------------------------------

Executing Ansible Playbook for all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible_playbook all <ansible_playbook arguments...>
    
Parameters:

   <ansible_playbook arguments...>: arguments for Ansible Playbook

Example:

.. code-block:: shell

   tlm ansible_playbook all myplaybook.yml -e test_variable="test"

Executing Ansible Playbook for all Nodes of a Site
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible_playbook site <sitename> <ansible_playbook arguments...>
    
Parameters:

   <sitename>: name of the Site for whose Nodes the Ansible Playbook shall be executed

   <ansible_playbook arguments...>: arguments for Ansible Playbook

Examples:

.. code-block:: shell

   tlm ansible_playbook site mysite1 myplaybook.yml -e test_variable="test"

Executing Ansible Playbook for a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

.. code-block:: shell

   tlm ansible_playbook node <nodeid>|<nodename.sitename> <ansible_playbook arguments...>
    
Parameters:

   <nodeid>: numeric node identifier

   <nodename.sitename>: name of the Node within the given Site (identified by its name)

   <ansible_playbook arguments...>: arguments for Ansible Playbook

Example:

.. code-block:: shell

   tlm ansible_playbook node 15 myplaybook.yml -e test_variable="test"
   tlm ansible_playbook node primary.mysite1 myplaybook.yml -e test_variable="test"

Executing git commands
----------------------

Run git executable
^^^^^^^^^^^^^^^^^^

You may execute any git command for the local repository that contains Towalink's configuration files.


Command:

.. code-block:: shell

   tlm git <git arguments...>
    
Parameters:

   <git arguments...>: arguments for git command

Example:

.. code-block:: shell

   tlm git branch new-branch


Setting the debug level
-----------------------

You may configure the verbosity of the command by setting the log level parameter to the desired value.

.. code-block:: shell

   tlm --loglevel <loglevel> ...

Parameters:

   <loglevel>: valid values are "debug", "info", "warning", and "error"

Example:

.. code-block:: shell

   tlm --loglevel debug list sites
