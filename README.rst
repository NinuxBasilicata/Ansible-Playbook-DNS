Ansible General Services
=================================

Clonare con::

    git clone https://github.com/NinuxBasilicata/Ansible-Playbook-DNS.git

Aggiornare con::

    git pull
    git submodule update --init --recursive

Roles
-----

Il Playbook contiene le seguenti roles:

- common
  This role contain all the common package/ configuration and utils needed by ANY system

- bind
  Server DNS con TLD .nnxx preconfigurato e zona di reverse /16

- database
  Installazione di MariaDB

- NamedManager
  Installazione di NamedManager: gestione di un server BIND tramite interfaccia Web.

- webserver
  Installazione di Apache2 e PhpMyAdmin



Inventory
---------

Define groups between brackets:

::

    # file: hosts

    [<type>]

Add below the group you created your host with IP address or hostname. If you have hosts that use a non-standard SSH port, you can put the port number after the hostname with a colon:

::

	# file: hosts

	[<type>]
	192.168.78.15:22022
	example.com

Defining playbooks
------------------

On file namedmanager.yml every group have its own subset of roles that defines what kind of task ansible must execute for every host

::

    # file: namedmanager.yml

    - hosts: DNS
      become: "{{ sudo | default('yes') }}"
      roles:
        - common
        - bind
        - database
        - apache2
      vars:
        # common
        common_ipv4_forward: 1
        common_ssh_port: 2400
        # variabili per ruolo common
        users:
          - name: michele
            authorized:
              - ./keys/michele.pub


Every role has a directory where tasks, templates, handlers and variables are defined. For more informations see https://docs.ansible.com/index.html

Gotta deploy 'em all!
---------------------

Install ansible on Debian Jessie:

::

  $ sudo apt-get install software-properties-common
  $ sudo echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" >> /etc/apt/sources.list
  $ sudo apt-get update
  $ sudo apt-get install ansible

Deploy of a new server:

::

  ansible-playbook -i hosts namedmanager.yml -ku <user> -e "sudo=no" -l <server-name>

Just run tasks with specific tag:

::

  ansible-playbook -i hosts namedmanager.yml -t "<tag1>,<tag2>,...,<tagN>"

Or run task without certain tasks

::

  ansible-playbook -i hosts namedmanager.yml --skip-tags "<tag1>,<tag2>,...,<tagN>"
