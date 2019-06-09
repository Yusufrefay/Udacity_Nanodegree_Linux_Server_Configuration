# Udacity_Nanodegree_Linux_Server_Configuration

Project 3 : Linux server configuration

# Server Details

* Public ip address : 52.59.221.156

* Application URL : http://52.59.221.156

* SSH Port : changed from ~~**22**~~ to **2200**

* HTTP Port :**80**

* NTP Port :**123**

## Connection to the Instance from the Ubuntu Terminal

1. Download the instance's private key by navigating to the Amazon Lightsail 'Account page'

2. Click on '**Download default key**'

3. A file with extension `.pem` will be downloaded; open it.

4. Copy the text and put it in a file called `lightrail_key.rsa` (or any other name to help you could recall anytime) in the local machine `~/.ssh/` directory

5. Run `chmod 600 ~/.ssh/lightrail_key.rsa` to give permissions to access the file

6. `ssh -i ~/.ssh/lightrail_key.rsa ubuntu@aa.aa.aa.aa`, and aa.aa.aa.aa is the public IP. to login to the instance

## Update and Upgrade existing packages
`apt-get update` - to update the packages existing

`apt-get upgrade` - to upgrade the installed packages


### UFW Configuration (Uncompiled Firewall) :
1.    Change the SSH port from 22 to 2200 (open up the /etc/ssh/sshd_config) file :
      * `sudo nano /etc/ssh/sshd_config`

2.    Find the line which contains port number and change number to 2200 , then restart SSH :
      * `sudo service ssh restart`

3.    Set the UFW to block everything coming in :
      * `sudo ufw default deny incoming`

4.    Set the UFW to allow everything outgoing :
      * `sudo ufw default allow outgoing`

5.    Set the UFW to allow SSH :
      * `sudo ufw allow ssh`

6.    Allow all tcp connections for port 2200 (SSH) :
      * `sudo ufw allow 2200/tcp`

7.    Set the UFW to allow a basic HTTP server
      * `sudo ufw allow www`

8.    Set the UFW to allow NTP (port 123) :
      * `sudo ufw allow 123/udp`

9.    Deny port 22 (as SSH will be configured to port 2200 instead)
      * `sudo ufw deny 22`

9.    Deny port 22\tcp (as SSH will be configured to port 2200 instead)
      * `sudo ufw deny 22\tcp`

10.   Enable UFW
      * `sudo ufw enable`

11.   Then you can check the status of the UFW and open ports :
      * `sudo ufw status`

12.   Then we need to update the firewall configuration on the lightsail AWS instance and change the same ports as configured above

13.   You can now login through new terminal window :
      * `ssh -i ~/.ssh/light_key.rsa -p 2200 ubuntu@52.59.221.156`


### Create grader User :

1.    Add user :
      * `sudo adduser grader`

2.    After the user is added we need to give it sudo permissions :
      * `sudo visudo`

3.    Add the following line after to the ones that look same :grader ALL=(ALL:ALL) ALL

4.    Then we need to allow grader to login to VM :

5.    Open another Gitbash window on your local machine and run

6.    `ssh-keygen` and choose name for this key as grader_key

7.    Then from the VM instance navigate to the grader home directory
      * `cd /home/grader`

8.    Then create a directory '.ssh' and a file 'authorized_keys' inside it
      * `sudo mkdir .ssh`
      * `sudo touch authorized_keys`

9.    Then copy the content of the key generated on the local machine to the autorized_keys file on the VM
10.   Run `chmod 700 .ssh` on the VM
11.   Run `chmod 644 .ssh/authorized_keys` on the VM
12.   Run `chown -R grader:grader .ssh `on the VM
13.   Make sure key-based authentication is forced (log in as grader, open the /etc/ssh/sshd_config file, by running `sudo nano /etc/ssh/sshd_config file` and find the line that says, '# Change to no to disable tunnelled clear text passwords'; if the next line says, 'PasswordAuthentication yes', change the 'yes' to 'no'; save and exit the file; `run sudo service ssh restart`
14.   Then login to the VM machine through your local machine :
      * `ssh -i ~/.ssh/grader_key.rsa -p 2200 grader@52.59.221.156`

### Change timezone to UTC
1. Check the timezone with the `date` command. This will display the current timezone after the time.
  If it's not UTC change it with this command: `sudo timedatectl set-timezone UTC`
## Install git and clone the catalog project
1.    Install Apache web server:
      * `sudo apt-get install apache2`
2.    Install mod_wsgi :
      * `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3.    Restart apache server :
      * `sudo service apache2 restart`



### Create a new PostgreSQL for catalog

1. PostgreSQL creates a Linux user with the name `postgres` during installation; switch to this user by running sudo `su - postgres`.

1. Connect to psql (the terminal for interacting with PostgreSQL) by running psql

2. Create the catalog user by running `CREATE ROLE catalog WITH LOGIN;` (Make sure to enter `;` without which it may NOT understand the statement)

3. Next, give the catalog user the ability to create databases: `ALTER ROLE catalog CREATEDB;` (Make sure to enter `;` without which it may NOT understand the statement)

4. Finally, give the catalog user a password by running `\password catalog`

5. Check to make sure the catalog user was created by running `\du`

6. A table will come up showing all the roles added, check on `catalog role` if its able to `create DB`

7. Exit psql by running `\q`

8. Switch back to ubuntu user through `exit`

## Create a Linux user called `catalog`

1. Create a new Linux user called catalog:

    * `sudo adduser catalog`
    * fill out information for catalog

2. Give the catalog user sudo permissions:

    * run sudo visudo
    * add catalog ALL=(ALL:ALL) ALL
    * save and close the visudo file

3. To verify that catalog has sudo permissions, login to catalog (run sudo su - catalog), and run `sudo -l`

```
 User catalog may run the following commands on
 	ip-XX-XX-XX-XX.ec2.internal:
     (ALL : ALL) ALL
```
4. Create a database called catalog by running `createdb catalog`

5. Run `psql` and from there run `\l` to see that the new database has been created

6. Switch back to the ubuntu user by running `exit`
#### Clone the app from GitHub:
1.    Install git:
      * `sudo apt-get install git`
2. Create a directory called **itemCatalog** and clone the [catalog project](https://github.com/blurdylan/item-catalog.git) by running `sudo -u www-data git clone https://github.com/blurdylan/item-catalog.git itemCatalog`
          > Note that the _itemCatalog_ at the end is to stop git from adding the folder item-catalog because Apache doesn't like hyphens very much

3. Change the ownership of the 'itemCatalog' directory to www-data by running (while in /var/www): `sudo chown -R www-data:www-data itemCatalog/`

4. Go to the `cd /var/www/itemCatalog/itemCatalog` directory

5. run `mv main.py __init__.py` to change the name of the `main.py` file to **`__init__.py`**

6. Open `__init__.py` file and go to the last line, change from `app.run(host='0.0.0.0', port=5000)` to `app.run()`


## Adding Google Auth
1. Create a new project on the Google API Console

1. Go on credential tabs, create an auth and add http://52.59.221.156 as authorized JavaScript origins

5. Edit the login.html and base.html with any text editor of your choice

5. Search for the words `change this` in `login.html` and `base.html`

5. add your clientID on the `meta tag`



### Install the required and Programs libraries
```
`sudo apt-get install python-pip`
`sudo apt-get install python-virtualenv`
<ve> is the Virtual Environment
apt-get install python-psycopg2 python-flask
apt-get install python-sqlalchemy python-pip //pip and sql alchemy
pip install --upgrade oauth2client //oauth for login
pip install requests
pip install httplib2
pip install flask-seasurf
sudo apt-get install libpq-dev

```

### Setup apache service
1. Create a configuration file (`itemCatalog.conf`) in `/etc/apache2/sites-available/` type this into it:
```
<VirtualHost *:80>
		ServerName XX.XX.XX.XX
		ServerAdmin youremail@domain.com
		WSGIScriptAlias / /var/www/itemCatalog/itemCatalog.wsgi
		<Directory /var/www/itemCatalog/itemCatalog/>
			Order allow,deny
			Allow from all
			Options -Indexes
		</Directory>
		Alias /static /var/www/itemCatalog/itemCatalog/static
		<Directory /var/www/itemCatalog/itemCatalog/static/>
			Order allow,deny
			Allow from all
			Options -Indexes
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```


## Disable the apache site
1. Run `sudo a2dissite 000-default.conf`
2. Restart the server with sudo `service apache2 reload`

## Sources
[Configuring linux servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299) - Udacity course

[Command line basics](https://www.udacity.com/course/linux-command-line-basics--ud595) - Udacity course

[Deploying a flask application](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps) - Digital Ocean Tutorials

[Ubuntu forum](askubuntu.com) - Ask ubuntu for questions on commands

[WSGI setup](https://code.google.com/archive/p/modwsgi/wikis/ConfigurationDirectives.wiki) - Parameters for setting up the WSGI configuration files

[Stack Overflow](stackoverflow.com) and [Udacity Forum](https://discussions.udacity.com) Posts: system restart required [issue](http://askubuntu.com/questions/258297/should-i-always-restart-the-system-when-i-see-system-restart-required);[Postgresql](https://discussions.udacity.com/t/psycopg2-operationalerror-postgresql-error/202640/3) error;
[hyphens and apache](http://stackoverflow.com/questions/18427766/apache2-and-mod-wsgi-target-wsgi-script-cannot-be-loaded-as-python-module)

* https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* https://askubuntu.com/
* [rrjoson Readme](https://github.com/rrjoson/udacity-linux-server-configuration/)
* [stueken Readme](https://github.com/stueken/FSND-P5_Linux-Server-Configuration/)
* [blurdylan Readme](https://github.com/blurdylan/linux-server-configuration/)
* [ahmedheema95 Readme](https://github.com/ahmedheema95/Linux-Server-Configuration/blob/master/README.md)
