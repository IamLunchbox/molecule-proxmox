***********************
Molecule Proxmox Plugin
***********************

This is an experimental Ansible Molecule Driver plugin to manage instances on a
`Proxmox VE`_ hypervisor cluster.  Only virtual machines are supported at this
time.  Proxmox containers will be supported in a future release.

Requirements
============

* Access to a `Proxmox VE`_ cluster
* One or more virtual machine templates with required setup
* Python package `proxmoxer`_
* Ansible module `community.general.proxmox_kvm`_

The required Python packages are automatically installed when
``molecule-proxmox`` is installed with ``pip``.

The ``proxmox_kvm`` module is included with the Community.General collection
and is automatically installed when Ansible is installed with ``pip``.


Virtual machine template requirements
-------------------------------------

The molecule instances are created by cloning Proxmox virtual machine
templates.  You will need to create one or more templates.

Templates have the following requirements.

* A cloud-init drive if any cloud-init settings are used
* networking configured
* Python installed for Ansible
* qemu-guest-agent installed and enabled in Proxmox
* ssh server installed
* user account for Ansible
* An ssh public key must be added to the ``authorized_keys`` for the Ansible user account.
* If a non-root user is used for the Ansible user (recommended), that user should be
  added to the sudoers. (This is not needed for the driver, but will likely be needed
  for the ``converge`` playbook.)

Installation
============

The ``molecule-proxmox`` plugin may be installed with Python ``pip``. A virtualenv
is recommended.  The following commands install Ansible, Molecule, and the
Molecule Proxmox plugin in a virtualenv called ``venv``.

.. code-block:: bash

    $ python3 -m venv venv
    $ source venv/bin/activate
    $ pip3 install molecule[ansible] molecule-proxmox


Examples
========

.. code-block:: yaml

   driver:
     name: molecule-proxmox
     options:
        api_host: pve01.example.com
        api_user: molecule
        api_password: "********"
        node: pve01
        ssh_user: tester
        ssh_identity_file: /path/to/id_rsa
   platforms:
     - name: test01
       template_name: debian11
     - name: test02
       template_name: alma8

.. code-block:: yaml

   driver:
     name: molecule-proxmox
     options:
        api_host: pve01.example.com
        api_user: molecule
        # Optional: Use an API token for Proxmox authentication.
        api_token_id: "********"
        api_token_secret: "*******************************"
        node: pve01
        ssh_user: tester
        ssh_identity_file: /path/to/id_rsa
        # Optional: The default template name.
        template_name: debian11
        # Optional: Set the hostname after cloning.
        sethostname: yes
        # Optional: Create the VMs in the pool.
        pool: test
   platforms:
     - name: test01
       # Optional: Specify the VM id of the clone.
       newid: 216
     - name: test02
       # Optional: Specify the VM id of the clone.
       newid: 217

.. code-block:: yaml

   driver:
     name: molecule-proxmox
     options:
        # Secrets file may be encrypted with ansible-vault.
        proxmox_secrets: /path/to/proxmox_secrets.yml"
        node: pve01
        ssh_user: tester
        ssh_identity_file: /path/to/id_rsa
        template_name: debian11
   platforms:
     - name: test01
     - name: test02

.. code-block:: yaml

   driver:
     name: molecule-proxmox
     options:
        # Secrets file may be encrypted with ansible-vault.
        proxmox_secrets: /path/to/proxmox_secrets.yml"
        node: pve01
        ssh_user: tester
        ssh_identity_file: /path/to/id_rsa
        template_name: debian11
   platforms:
     - name: test01
       template_name: debian11
       # See https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_kvm_module.html
       # for cloud-init options.
       ciuser: some_user
       cipassword: some_password
       ipconfig:
         ipconfig0: 'ip=192.168.0.2/24,gw=192.168.0.1'
       nameservers:
         - 192.169.0.245

Development
===========

To checkout the source code:

.. code-block:: bash

    $ git clone https://github.com/meffie/molecule-proxmox
    $ cd molecule-proxmox

A `Makefile` is provided to facilitate development and testing. A Python
virtualenv environment may be created with the `init` target.

.. code-block:: bash

    $ make init
    $ source .venv/bin/activate

Export the following shell environment variables to run the unit tests.

.. code-block:: bash

    export PROXMOX_SECRETS=<proxmox secrets yaml file path>
    export PROXMOX_NODE=<proxmox node name>
    export PROXMOX_SSH_USER=<username>
    export PROXMOX_SSH_IDENTITY_FILE=<ssh key file for username>
    export PROXMOX_TEMPLATE_VMID=<template vmid to be cloned in by-vmid scenario>
    export PROXMOX_TEMPLATE_NAME=<template name to be cloned in by-name scenario>

The secrets file should contain the proxmox login credentials, either the
username and password, or a Proxmox API token id and value.  This file should
be encrypted with `ansible-vault`. The ssh user and identity file should match
the user and public key installed when the virtual machine template was
created.

To run the unit tests in verbose mode:

.. code-block:: bash

    $ make test

To run the unit tests in quiet mode:

.. code-block:: bash

    $ make check


Authors
=======

Molecule Proxmox Plugin was created by Michael Meffie based on code from
Molecule.

License
=======

The `MIT`_ License.


.. _`Proxmox VE`: https://www.proxmox.com/en/proxmox-ve
.. _`proxmoxer`: https://pypi.org/project/proxmoxer/
.. _`community.general.proxmox_kvm`: https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_kvm_module.html
.. _`MIT`: https://github.com/meffie/molecule-proxmox/blob/master/LICENSE
