# Ansible NamedManager for Debian

[![N|Solid](http://basilicata.ninux.org/images/Logo_Ninux_Basilicata_600-192.png)](http://basilicata.ninux.org)

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

