# LSCPReadMe

#Subjective, a Catalog Project

A program to create, read, update and delete school and subject entries by users. Read is accessible to all, create is accessible to all logged in users, and update and delete are only accessible to creators of subjects and schools.

The program is built using python, Flask and sqlalchemy(PostgreSQL), and can be publicly accessed at [Subjective](http://ec2-54-209-205-198.compute-1.amazonaws.com/)

##SSH DIRECTIONS

The app can be accessed through ssh by using the following steps:

SSH Port: 2200

Public IP Address: 54.209.205.198

On your local machine's terminal, enter:
ssh grader@54.209.205.198 -p 2200 -i ~/graderkey

The graderkey is copied into the 'Notes to Reviewer' accompanying project submission


##APP SETUP

The app was made live using AWS LightSail (Ubuntu on a Linux remote server) using the following steps:

##Sign up and instance set up for AWS

Sign up for [AWS](https://lightsail.aws.amazon.com/)

Create an instance

Choose Ubuntu

Rename instance

SSH into the instance using the 'Connect using SSH' button

##Updates

Run the following commands to update and upgrade packages

sudo apt-get update

sudo apt-get upgrade

##Installation

Run the following commands to install packages needed for the project:

sudo apt-get install apache2

sudo apt-get install libapache2-mod-wsgi

sudo apt-get install postgresql

sudo apt-get install postgresql postgresql-contrib

sudo apt-get install python-setuptools python-dev build-essential

sudo easy_install pip

sudo pip install --upgrade virtualenv

sudo pip install Flask

sudo pip install sqlalchemy

sudo apt-get install finger

sudo apt-get install python-oauth2client

sudo pip install requests

sudo apt-get install git

sudo apt-get install ntp

sudo apt-get install python-psycopg2

##Setting up Uncomplicated Firewall and access ports

sudo ufw allow ssh

sudo ufw allow 2200/tcp

sudo ufw allow www

sudo ufw enable

sudo ufw allow http

sudo ufw allow 80

sudo ufw allow ntp

sudo ufw allow 123/tcp

sudo ufw status

sudo ufw deny 22/tcp 

sudo ufw default deny incoming

sudo ufw default allow outgoing

##Setting Time Zone

sudo timedatectl set_timezone Etc/UTC

##User creation and giving sudo access

sudo adduser grader

sudo nano /etc/sudoers.d/grader 

paste the following in grader file
grader ALL = (ALL) NOPASSWD:ALL

##Setting up SSH Access for grader

ssh-keygen -t rsa (on local machine)

su grader (on AWS SSH terminal)

sudo mkdir .ssh (in grader's home dir.)

sudo nano .ssh/authorized_keys
(paste public key created through keygen for grader in this file)

sudo nano .ssh/graderkey.pub
(paste public key created through keygen for grader in this file)

sudo nano .ssh/graderkey
(paste private key created through keygen for grader in this file)

sudo chown grader:grader .ssh (in grader’s home directory)

sudo chown grader:grader authorized_keys (in .ssh directory in grader’s home directory)

sudo chown grader:grader graderkey (in .ssh directory in grader’s home directory)

sudo chown grader:grader graderkey.pub (in .ssh directory in grader’s home directory)

sudo nano /etc/ssh/sshd_config (on AWS SSH terminal)

remove port 22 and add port 2200 here

##Disabling SSH Access for root

sudo nano /etc/ssh/sshd_config
change PermitRootLogin permissions to no

sudo service sshd restart (so that changes made take effect)

##Configuration

touch catalog.wsgi /var/www/html
(create a catalog.wsgi file)

sudo nano catalog.wsgi
(paste the following in catalog.wsgi)

import sys

import logging

logging.basicConfig(stream=sys.stderr)

sys.path.insert(0, '/var/www/html/')

from catalog.catalog import app as application

application.secret_key = 'super_secret_key'

sudo nano /etc/apache2/sites-enabled/000-default.conf
(update contents of file to the following)

<VirtualHost *:80>
	ServerName 54.209.205.198
	ServerAdmin admin@54.209.205.198
	DocumentRoot /var/www/html

	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined

	WSGIScriptAlias / /var/www/html/catalog.wsgi
	<Directory /var/www/html/catalog>
		Order deny,allow
		Allow from all
	</Directory>
</VirtualHost>

##Database Setup

sudo –u postgres psql postgres


\password postgres (type new password twice)

sudo su – postgres

psql

\password (enter password)

\q (quit)

createuser --interactive
(username = catalog)

createdb catalog

ALTER DATABASE catalog OWNER TO catalog

ALTER ROLE catalog WITH NOCREATEDB

sudo nano /etc/postgresql/9.5/main/postgresql.conf

listen_addresses = '*' (edit)

sudo nano /etc/postgresql/9.5/main/pg_hba.conf
(add the following to pg_hba.conf)

host     all   all   127.0.0.1/0   md5

hostssl  all   all   127.0.0.1/0   md5

##Code Layout

Database set up is in database_setup.py

Helper functions are in utilities.py

All handlers are in project.py

All html files are in the templates directory

Style file is in static directory

##Resources Consulted

[How to deploy a flask app on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
 
[How to install and use postgresql on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)

[How to install postgresql on ubuntu remote access](https://wixelhq.com/blog/how-to-install-postgresql-on-ubuntu-remote-access)

and 2 udacity 1:1 sessions
