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
  Installazione/Configurazione di base per il sistema operativo

- bind
  Server DNS con TLD .nnxx preconfigurato e zona di reverse /16

- database
  Installazione di MariaDB (non ha password di root), preferibile eseguire successivamente il comando
~~~
mysql_secure_installation
~~~

- webserver
  Installazione di Apache2, PHP5 e PhpMyAdmin base.

- NamedManager
  Installazione di NamedManager: gestione di un server BIND tramite interfaccia Web.



Inventory
---------

Define groups between brackets:

::

    # file: hosts

    [<type>]

Esempio

::

	# file: hosts

        [DNS]
        test.nnxx ansible_user=michele ansible_port=2400 ansible_host=10.27.0.120


Defining playbooks
------------------

::

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

Se sul server hai gia' installato sudo ed hai una chiave ssh installata puoi usare

::

  ansible-playbook -i hosts namedmanager.yml -l DNS
