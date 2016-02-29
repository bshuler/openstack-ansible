`Home <index.html>`_ OpenStack-Ansible Developer Documentation

.. _developer-inventory:

OpenStack-Ansible Inventory
===========================

OpenStack-Ansible uses an included script to generate the inventory of hosts
and containers within the environment. This script is called by Ansible
through its `dynamic inventory functionality`_.


Generating the Inventory
------------------------

The script that creates the inventory is located at
``playbooks/inventory/dynamic_inventory.py``.

Execution
^^^^^^^^^

When running an Ansible command (such as ``ansible``, ``ansible-playbook`` or
``openstack-ansible``) Ansible will execute the ``dynamic_inventory.py`` script
and use its output as inventory.

The command can also be run manually as follows:

.. code-block:: bash

    # from the playbooks directory
    inventory/dynamic_inventory.py --config /etc/openstack_deploy/

This invocation is useful when testing changes to the dynamic inventory script.

Inputs
^^^^^^

The ``dynamic_inventory.py`` script takes a single argument, ``--config``. If not
specified, the default is ``/etc/openstack_deploy/``.

.. note:: In all versions prior to Mitaka, this argument was ``--file``.

Within the specified directory, the following files must be present:

    * ``openstack_environment.yml``
    * ``openstack_user_config.yml``

Additionally, the configuration or environment could be spread between two
additional directories:

    * ``conf.d``
    * ``env.d``

These files are used to build a definition of the environment consumable by Ansible.

Outputs
^^^^^^^

Once executed, the script will output an ``openstack_inventory.json`` file into
the directory specified with the ``--config`` argument. This is used as the
source of truth for repeated runs.

.. note::
    The ``openstack_inventory.json`` file is the source of truth for the
    environment. Deleting this in a production environment means that the UUID
    portion of container names will be regenerated, which then results in new
    containers being created. Containers generated under the previous version
    will no longer be recognized by Ansible, even if reachable via SSH.

The same JSON structure is printed to stdout, which is consumed by Ansible as
the inventory for the playbooks.


Inspecting and Managing the Inventory
-------------------------------------

The file ``scripts/inventory-manage.py`` is used to produce human readable
output based on the ``/etc/openstack_deploy/openstack_inventory.json`` file.

The same script can be used to safely remove hosts from the inventory.

Viewing the Inventory
^^^^^^^^^^^^^^^^^^^^^

The ``/etc/openstack_deploy/openstack_inventory.json`` file is read by default.
An alternative file can be specified with ``--file``.

A list of all hosts can be seen with the ``--list-host/-l`` argument

To see a listing of hosts and containers by their group, use
``--list-groups/-g``.

To see all of the containers, use ``--list-containers/-G``.

Removing a Host
^^^^^^^^^^^^^^^

A host can be removed with the ``--remove-item/-r`` parameter.

Use the host's name as an argument.

..  _`dynamic inventory functionality`: http://docs.ansible.com/ansible/intro_dynamic_inventory.html

--------------

.. include:: navigation.txt