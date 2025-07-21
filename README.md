# ANSIBLE HOMEWORK

In this scenario I used the roles for getting the thing done. Specifically: 
- Install LAMP stack
- Deploy users from CSV file
- Set hosts so they can see each others by hostname

Since the tasks were divided to roles now there is only one (another one for rollback) playbook.

### What these playbook do:

### roles_playbook.yml:
-----------------------------
Installs the LAMP stack by taking following actions:
- determine which OS family is used on the host node to choose correct child playbook accordingly
- updates cache for having the latest repos
- installs apps needed for LAMP stack (Apache, MariaDB, PHP)
- starts Apache and MariaDB services
- opens ports for http (80) and https (443)  <- this is implemented so far only for RHEL since this can vary for each customer configuration; therefore I'd rather got customer config before implementing this
- creates new index page (index.php) in /var/www/html/ and remove the default one to not being prioritized

Deploys the users from provided CSV file to the hosts and sets their specifics according to the instructions by:
- loading the data from provided CSV file (default ./files/users.csv)
- creates users on the host
- deploys RSA keys for users which have it already defined in the file
- creates folder to store the keys for functional users and generates the keys
- sets the local ssh config file to allow the functional users connect without providing the path to their keys
- authorize these keys on the hosts
- locks and expires the users from the list without keys or not being funcional
- disallows the connection through the password
- restarts sshd service to activate the changes

Sets up the host machines to know each others by hostname and executes ping to each other to test it with following actions:
- if the hostname on the host machine is just 'localhost' it changes it to better identification
- extracts hosts IPs and hostnames and saves it into temporary file
- slurps the data and decodes them for inserting into /etc/hosts
- pings the hosts by their hostnames except itself (this one is skipped)
- provides the ping result with the debug message for user to check

*! PLAYBOOK ALSO CREATES A BACKUP FILES FOR EASY ROLLBACK !*

##### HOW TO ROLLBACK?
Easily. Just run this ROLLBACK playbook:
- ROLLBACK_roles_playbook.yml

### How to run these playbooks?
*Before first run of the playbooks is recommended to run the file 'script_00.sh' which installs ansible galaxy collections to be sure that you have all required modules. When it's installed you don't have to run it again.*

##### NOTICE: Since you don't run the particular-tasks playbooks, you don't have the option to specify the CSV file for deploying users. Therefore if you want to have some other data you will have to edit the defualt file ./files/users.csv.
