<<<<<<< HEAD
# Ansible NamedManager for Debian

.. image:: http://basilicata.ninux.org/images/Logo_Ninux_Basilicata_600-192.png
   :target: http://basilicata.ninux.org

Ansile NamedManager è uno playbook ansible che incoropora varie roles con: 
  - Common (installazione di base)
  - WebServer (apache2 + php5)
  - MySQL (MariaDB + phpMyAdmin)
  - Bind9 (installazione base con un TLD custom per la LAN)
  - NamedManager (gestione di Bind tramite interfaccia web)

# Installazione
Clonare con:
```sh
git clone https://github.com/NinuxBasilicata/Ansible-Playbook-DNS.git
```
Aggiornare con:
```sh
git pull
git submodule update --init --recursive
```
# Inventory
```sh
# file: hosts
[<type>]
```
Esempio:
```sh
# file: hosts
[DNS]
test.nnxx ansible_user=michele ansible_port=2400 ansible_host=10.27.0.120
```

# Defining playbooks
```sh
# file: namedmanager.yml
- hosts: DNS
  become: "{{ sudo | default('yes') }}"
  roles:
    - common
    - bind
    - database
    - apache2
    - namedmanager
  vars:
    # common
    common_ipv4_forward: 1
    common_ssh_port: 2400
    # variabili per ruolo common
    users:
      - name: michele
        authorized:
          - ./keys/michele.pub
    # bind
    tld: 'nnxx'
    hostmaster: 'admin.basilicata.nnxx'
    nameservers_hosts:
       ns1:
         ip: '10.27.0.120'
    # namedmanager
    db_host: "localhost"
    db_name: "namedmanager"
    db_user: "namedmanager"
    db_pass: "ninux"
    api_url: "http://10.27.0.120/namedmanager"
    api_server_name: "test.nnxx"
    api_auth_key: "ninux"
```
Ogni ruolo ha una directory in cui sono definiti i task, i template, handlers e variabili. Per maggiori informazioni vedere  https://docs.ansible.com/index.html

# Gotta deploy 'em all!
=======
Ansible NamedManager Services
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
>>>>>>> parent of 04fe504... esempio di uso

Install ansible on Debian Jessie:
```sh
sudo apt-get install software-properties-common
sudo echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" >> /etc/apt/sources.list
sudo apt-get update
sudo apt-get install ansible
```
Deploy su un nuovo server:

```sh
ansible-playbook -i hosts namedmanager.yml -ku <user> -e "sudo=no" -l <server-name>
```

<<<<<<< HEAD
=======
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
>>>>>>> parent of 04fe504... esempio di uso
