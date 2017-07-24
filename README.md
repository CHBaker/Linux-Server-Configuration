## Udacity Linux Server Configuration Project

> This project is meant to serve the [Restaurant Database Application](https://github.com/CHBaker/Restaurant-Catalog-DB-Driven) using Amazon Lightsail Ubuntu to serve a wsgi file that handles a flask application with python and postgresql

# Basic Info:

	
	Public IP: 52.34.254.190

	SSH Port: 2200

	Website URL: (http://ec2-52-34-254-190.us-west-2.compute.amazonaws.com/)[http://ec2-52-34-254-190.us-west-2.compute.amazonaws.com/]


# Configuration:

	# 1] - Set Up Amazon Lightsail


		1) create a Lightsail Instance using Ubuntu, download the default ssh key, and log in remote as root user

			resource: [Amazon Lightsail Start Page](https://amazonlightsail.com)

		2) Update Ubuntu: `$ sudo apt-get update`
						  `$ sudo apt-get upgrade`

		3) Configure timezone to UTC:

			a) Open time configuration dialog and set it to UTC with: $ sudo dpkg-reconfigure tzdata.
			b) Select 'None of the above' on the first page, then, on the second page select 'UTC'

		resources: (how to change timezone ubuntu)[https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt]


	# 2] - Create New User


		1) Log into the remote VM as root user through ssh: `$ ssh root@52.34.254.190`

		2) Add a new user called grader (with password 'grader'): `$ sudo adduser grader`

		3) Create a new file in the suoders directory: `$ sudo nano /etc/sudoers.d/grader`

		4) Edit the file using sudo and add this line to give grader sudo abilities:
		   "grader ALL=(ALL:ALL) ALL" don't forget to save it.

		resources: (Udacity Linux Security add user course)[https://classroom.udacity.com/nanodegrees/nd004/parts/00413454014/modules/357367901175461/lessons/4331066009/concepts/48010894680923]


	# 3] - Create SSH Keys for User Grader Authentication


		1) Generate a key pair on your local machine with: `$ ssh-keygen -f ~/.ssh/graderAccess`

		2) Log in remotely as root user through ssh and create the following file: 
		   `$ touch /home/grader/.ssh/authorized_keys`

		3) Copy the contents of graderAccess.pub from your local machine to the `/home/grader/.ssh/authorized_keys`
		   file you just created on the lightsail instance.

		4) Change the permissions on the files:
		   `$ sudo chmod 700 /home/grader/.ssh.`
           `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`

        5) User grader can ssh with the following command: `$ ssh -i ~/.ssh/graderAccess grader@52.34.254.190.`

        resources: (Udacity Linux Security generating key pairs)[https://classroom.udacity.com/nanodegrees/nd004/parts/00413454014/modules/357367901175461/lessons/4331066009/concepts/48010894770923]

        6) Enforce key based authentication, by disabling password authentication:

        	a) open the sshd_config file, find the password authentication line, and change it from yes to no:
        	   `$ sudo nano /etc/ssh/sshd_config`
        	b) restart ssh service: `$ sudo service ssh restart`


    # 4] - Change Port from Default


    	1) Find the Port line and change it to 2200: `$ sudo nano /etc/ssh/sshd_config`

    	2) restart ssh service: `$ sudo service ssh restart`

    	3) Now you can only log in using port 2200: '$ ssh -i ~/.ssh/graderAccess -p 2200 grader@52.34.254.190'

    	resources: (Ubuntu Forums)[https://ubuntuforums.org/showthread.php?t=1591681]


    # 5] - Disable SSH for Root User


    	1) Open the sshd_config file: `$ sudo nano /etc/ssh/sshd_config`

    	2) Find the PermitRootLogin line and change it to no, then restart ssh:
    	   `$ sudo service ssh restart`


   	# 6] - Configure Uncomplicate Fire Wall, and Match External Fire Wall


   		1) Allow connections based on project requirements:
		   `$ sudo ufw allow 2200/tcp.
		   $ sudo ufw allow 80/tcp.
		   $ sudo ufw allow 123/udp.
		   $ sudo ufw enable.`

		2) In the Network tab of the Lightsail instance, match the external firewall
		   with the settings of the ufw


	# 7] - Install Apache2 and mod_wsgi to serve projecgt


		1) Install apache: `$ sudo apt-get install apache2.`

		2) Check that server is running by visiting the public ip `52.34.254.190`

		3) Install mod_wsgi: `$ sudo apt-get install libapache2-mod-wsgi`

		4) Enable mod_wsgi by restarting apache2: `$ sudo /etc/init.d/apache2 restart`

		*note: at this point, the apach test page will no longer show up on the public ip,
			   to display a test page, configure a mod_wsgi file

		resources: (Web Application Servers installing apache)[https://classroom.udacity.com/nanodegrees/nd004/parts/00413454014/modules/357367901175461/lessons/4340119836/concepts/48189486140923]


	# 8] - Install Git


		1) Install Git: `$ sudo apt-get install git`

		2) Set username: `$ git config --global user.name <username>`

		3) Set email: `$ git config --global user.email <email>`

		resources: (git)[https://git-scm.com/book/en/v2/Getting-Started-Installing-Git]


	# 9] - Clone Restaurant Database Repo


		1) Create a directory for the repo called catalog: `$ sudo mkdir /var/www/catalog`

		2) Navigate insed the catalog folder: `$ cd /var/www/catalog`

		3) Clone the catalog repo to the folder from Github:
		   `$ sudo git clone https://github.com/CHBaker/Restaurant-Catalog-DB-Driven.git`

		resources: (GitHelp)[https://help.github.com/articles/cloning-a-repository/]

		4) Create a catalog.wsgi file to serve the application over the mod_wsgi

			a) go to the html folder: `$ cd /var/www/html`
			b) Create the file: `$ sudo touch catalog.wsgi`
			c) Edit the file: `$ sudo nano catalog.wsgi`
			d) Insert these lines:
		       `import sys
			   import logging
			   logging.basicConfig(stream=sys.stderr)
			   sys.path.insert(0, "/var/www/catalog/")

			   from catalog import app as application`

		*note: the .git folder will be inaccessible from the web by default
		except the folder static assets



	# 10] - Install Project Dependencies


		1) Install pip in order to install Python packages: `$ sudo apt-get install python-pip`

		2) Install Flask using pip: `$ pip install Flask`

		3) Install other dependencies:
		   `$ pip install bleach httplib2 request oauth2client sqlalchemy python-psycopg2`

		4) Install a virtual environment: `$ sudo pip install virtualenv`

		5) Move to the catalog directory: `$ cd /var/www/catalog`

		6) Create the virtual environment: `$ sudo virtualenv venv`

		7) Start the virtual environment: `$ source venv/bin/activate`

		*note: python is already installed on Ubuntu

		resources: (Python Pip)[https://docs.python.org/3/installing/index.html]
				   (Python Virtual Environments)[http://python-guide-pt-br.readthedocs.io/en/latest/dev/virtualenvs/]


	# 11] - Configure + Enable Virtual Host


		1) Create the config file for VH: `$ sudo nano /etc/apache2/sites-available/catalog.conf`

		2) Copy and paste to file:
		   `<VirtualHost *:80>
			    ServerName 52.34.254.190
			    ServerAlias ec2-52-34-254-190.us-west-2.compute.amazonaws.com
			    ServerAdmin admin@52.34.254.190
			    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
			    WSGIProcessGroup catalog
			    WSGIScriptAlias / /var/www/catalog.wsgi
			    <Directory /var/www/catalog/>
			        Order allow,deny
			        Allow from all
			    </Directory>
			    Alias /static /var/www/catalog/catalog/static
			    <Directory /var/www/catalog/static/>
			        Order allow,deny
			        Allow from all
			    </Directory>
			    ErrorLog ${APACHE_LOG_DIR}/error.log
			    LogLevel warn
			    CustomLog ${APACHE_LOG_DIR}/access.log combined
			</VirtualHost>`

		3) Enable VH: `$ sudo a2ensite catalog`

		4) Restart apache2: `$ sudo service apache2 reload`

		resources: (DigitalOcean - skip through)[https://www.digitalocean.com/community/tutorials/how-to-run-django-with-mod_wsgi-and-apache-with-a-virtualenv-python-environment-on-a-debian-vps]


	# 12] - Install + Configure Postgresql


		1) Install python packages to work with psql: `$ sudo apt-get install libpq-dev python-dev`

		2) Install psql: `$ sudo apt-get install postgresql postgresql-contrib`

		3) Change to postgres user: `$ sudo su - postgres`

		4) Connect to psql: `$ psql`

		5) Create new user called catalog: `# CREATE USER catalog WITH PASSWORD 'catalog';`

		6) Give user catalog CREATEDB ability: `# ALTER USER catalog CREATEDB;`

		7) Create 'catalog' database for user: `# CREATE DATABASE catalog WITH OWNER catalog;`

		8) Connect to db: `# \c catalog;`

		9) Revoke all other rights: `# REVOKE ALL ON SCHEMA public FROM public;`

		10) Only let user catalog create tables: `# GRANT ALL ON SCHEMA public TO catalog;`

		11) Log out of psql: `# \q` 

		12) Go back to user grader: `$ exit`

		13) Inside the Flask app, change connection:
			`engine = create_engine('postgresql://catalog:sillypassword@localhost/catalog')`

		14) Set Database: `$ python /var/www/catalog/catalog/setup_database.py`

		15) Prevent remote access, open pg_hba.conf: `$ sudo nano /etc/postgresql/9.3/main/pg_hba.conf` 

		16) Make sure it looks like this:
			`local   all             postgres                                peer
			local   all             all                                     peer
			host    all             all             127.0.0.1/32            md5
			host    all             all             ::1/128                 md5`

		resources:  (DigitalOcean - install psql)[https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04]
					(DigitalOcean - secure psql)[https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps]

	# 13] - Launch App

		1) restart apache: `sudo service apache2 restart`

		2) Visit page at (http://ec2-52-34-254-190.us-west-2.compute.amazonaws.com/)[http://ec2-52-34-254-190.us-west-2.compute.amazonaws.com/]



