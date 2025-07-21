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

#### pl_02_deploy_users.yml
-----------------------------
Playbook 02 deploys the users from provided CSV file to the hosts and sets their specifics according to the instructions by:
- loading the data from provided CSV file (default ./files/users.csv)
- creates users on the host
- deploys RSA keys for users which have it already defined in the file
- creates folder to store the keys for functional users and generates the keys
- sets the local ssh config file to allow the functional users connect without providing the path to their keys
- authorize these keys on the hosts
- locks and expires the users from the list without keys or not being funcional
- disallows the connection through the password
- restarts sshd service to activate the changes

#### pl_03_set_hosts.yml 
-----------------------------
Playbook pl_03_set_hosts set up the host machines to know each others by hostname and executes ping to each other to test it with following actions:
- if the hostname on the host machine is just 'localhost' it changes it to better identification
- extracts hosts IPs and hostnames and saves it into temporary file
- slurps the data and decodes them for inserting into /etc/hosts
- pings the hosts by their hostnames except itself (this one is skipped)
- provides the ping result with the debug message for user to check
