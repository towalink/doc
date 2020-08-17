Command Reference
*****************

x

"tlm" Tool on the Towalink Controller
=====================================


Listing entities
----------------

Listing Sites
^^^^^^^^^^^^^

Command:

    tlm list sites
    
Parameters:

    no parameters

Example:

    tlm list sites

Listing Nodes
^^^^^^^^^^^^^
    
Command:
    
    tlm list nodes all|<sitename>

Parameters:

    <sitename>: Name of the Site whose Nodes shall be shown; "all" for Nodes of all Sites

Examples:

    tlm list nodes all
    tlm list nodes mysite1


Showing the configuration
-------------------------

Showing the global configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm show global
    
Parameters:

    no parameters

Example:

    tlm show global

Showing the Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm show site <sitename>
    
Parameters:

    <sitename>: Name of the Site whose configuration shall be displayed

Example:

    tlm show site mysite1

Showing the Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm show node <nodeid>|<nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:

    tlm show node 15
    tlm show node primary.mysite1

Showing the complete Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm show_all site <sitename>
    
Parameters:

    <sitename>: Name of the Site whose complete configuration shall be displayed

Example:

    tlm show_all site mysite1

Showing the complete Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm show_all node <nodeid>|<nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:

    tlm show_all node 15
    tlm show_all node primary.mysite1


Creating and removing entities
------------------------------

Creating a Site
^^^^^^^^^^^^^^^

Command:

    tlm add|create site <sitename>
    
Parameters:

    <sitename>: Name of the Site that shall be added

Example:

    tlm add site mysite1

Creating a Node
^^^^^^^^^^^^^^^

Command:

    tlm add|create node <nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

    tlm add node primary.mysite1

Removing a Site
^^^^^^^^^^^^^^^

Command:

    tlm del|remove site <sitename>
    
Parameters:

    <sitename>: Name of the Site that shall be added

Example:

    tlm del site mysite1

Removing a Node
^^^^^^^^^^^^^^^

Command:

    tlm remove node <nodeid>|<nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Examples:
    tlm del node 15
    tlm del node primary.mysite1


Modifying the configuration
---------------------------

Setting the global configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm set global <attr> <value>
    
Parameters:

    <attr>: Name of the attribute that shall be set
    <value>: New value for the attribute; special value "empty" to remove the attribute

Example:

    tlm set global wg_keepalive 25

Setting the Site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm set site <sitename> <attr> <value>
    
Parameters:

    <sitename>: Name of the Site whose configuration shall be changed
    <attr>: Name of the attribute that shall be set
    <value>: New value for the attribute; special value "empty" to remove the attribute

Example:

    tlm set site mysite1 wg_keepalive 25

Setting the Node configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm set node <nodeid>|<nodename.sitename> <attr> <value>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)
    <attr>: Name of the attribute that shall be set
    <value>: New value for the attribute; special value "empty" to remove the attribute    

Example:

    tlm set node 15 wg_keepalive 25
    tlm set node primary.mysite1 wg_keepalive 25


Config change management
------------------------

List Nodes with changed configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm list changed
    
Parameters:

    no parameters

Example:

    tlm list changed

Commit configuration of all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm commit all
    
Parameters:

    no parameters

Example:

    tlm commit all

Commit the configuration of a Site's Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm commit site <sitename>
    
Parameters:

    <sitename>: Name of the Site whose configuration shall be committed

Example:

    tlm commit site mysite1

Commit the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm commit node <nodeid>|<nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

    tlm commit node 15
    tlm commit node primary.mysite1


Activate Node configuration
---------------------------

Activate the latest configuration on all Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm activate all
    
Parameters:

    no parameters

Example:

    tlm activate all

Activate the configuration on all Nodes of a Site
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm activate site <sitename> [<version>]
    
Parameters:

    <sitename>: Name of the Site whose configuration shall be activated
    <version>: Version number of the configuration; "latest" is default

Examples:

    tlm 

Activate the configuration of a single Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Command:

    tlm activate node <nodeid>|<nodename.sitename> <version>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)
    <version>: Version number of the configuration; "latest" is default    

Example:

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

    tlm attach node <nodeid>|<nodename.sitename>
    
Parameters:

    <nodeid>: numeric node identifier
    <nodename.sitename>: name of the Node within the given Site (identified by its name)

Example:

    tlm attach node primary.mysite1


Setting the debug level
-----------------------

You may configure the verbosity of the command by setting the log level parameter to the desired value.

    tlm --loglevel <loglevel> ...

Parameters:

    <loglevel>: Valid values are "debug", "info", "warning", and "error".

Example:

    tlm --loglevel debug list sites
