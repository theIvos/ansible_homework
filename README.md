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
Playbook pl_03_set_hosts sets up the host machines to know each others by hostname and executes ping to each other to test it with following actions:
- if the hostname on the host machine is just 'localhost' it changes it to better identification
- extracts hosts IPs and hostnames and saves it into temporary file
- slurps the data and decodes them for inserting into /etc/hosts
- pings the hosts by their hostnames except itself (this one is skipped)
- provides the ping result with the debug message for user to check

*! ALL PLAYBOOKS CREATES BACKUPS FOR THEIR ACTIONS SO THERE CAN BE EASY ROLLBACK DONE !*

##### HOW TO ROLLBACK?
Easily. Just run these ROLLBACK playbooks:
- ROLLBACK_pl_01_install_lamp.yml
- ROLLBACK_pl_02_deploy_users.yml
- ROLLBACK_pl_03_set_hosts.yml

*You don't have to do it on the order, just be sure that before you run the particular ROLLBACK you executed first its counterpart (for example ROLLBACK_pl_03_set_hosts.yml is a counterpart from pl_03_set_hosts.yml - you can say just be seeing that the name is the same only ROLLBACK is at the beginning)*

### How to run all these playbooks?
*Before first run of the playbooks is recommended to run the file 'script_00.sh' which installs ansible galaxy collections to be sure that you have all required modules. When it's installed you don't have to run it again.*

- First check the inventory file hosts.ini in the folder 'files/hosts.ini' and edit according to required notes and users which have ssh access in.
Now you are ready to run the playbooks:

#### pl_01_install_lamp.yml
from CLI execute command:

`ansible-playbook pl_01_install_lamp.yml`

#### ROLLBACK_pl_01_install_lamp.yml
`ansible-playbook ROLLBACK_pl_01_install_lamp.yml`

#### pl_02_deploy_users.yml
`ansible-playbook pl_02_deploy_users.yml`
*INFO: Without specifying the file for CSV file containing the users to deploy, the default will be taken as 'files/users.csv' so you could edit that file OR specify different one with the following command:*
`ansible-playbook -e csv_file=PATH/TO/FILE pl_02_deploy_users.yml`

#### ROLLBACK_pl_02_deploy_users.yml
`ansible-playbook ROLLBACK_pl_02_deploy_users.yml`
*INFO: Ideally use the same CSV file as when you ran the deploy playbook. If you have specified different file than the default one, specify it also here:*
`ansible-playbook -e csv_file=PATH/TO/FILE pl_02_deploy_users.yml` 

#### pl_03_set_hosts.yml
`ansible-playbook pl_03_set_hosts.yml`

#### ROLLBACK_pl_03_set_hosts.yml
`ansible-playbook ROLLBACK_pl_03_set_hosts.yml`


##### The playbooks doesnt need to be run in order, they are not dependent on each other (only the ROLLBACK counterparts).
