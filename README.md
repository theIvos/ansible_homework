# ANSIBLE HOMEWORK

There are 3 playbooks, each performs particular actions and 3 ROLLBACK playbooks reverting the changes to the previous state.

### What these playbooks do:

#### pl_01_install_lamp.yml
-----------------------------
This playbook installs the LAMP stack by taking following actions:
- determine which OS family is used on the host node to choose correct child playbook accordingly
- updates cache for having the latest repos
- installs apps needed for LAMP stack (Apache, MariaDB, PHP)
- starts Apache and MariaDB services
- opens ports for http (80) and https (443)  <- this is implemented so far only for RHEL since this can vary for each customer configuration; therefore I'd rather got customer config before implementing this
- creates new index page (index.php) in /var/www/html/ and remove the default one to not being prioritized


