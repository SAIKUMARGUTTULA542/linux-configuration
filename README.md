# linux-configuration

### Priavate Key:  File name :Private Key RSA
User name: grader
Password : grader

## Linux-Server-Configuration
 This project is a part of Udacity Nanodegree Full Stack.

 This project explains how to secure and set up a Linux distribution on a virtual machine, install and configure a web and database server to host a web application.

 The virtual private server is Amazon EC2.
 The web application is my Item Catalog project created earlier in this Nanodegree program.
 The database server is PostgreSQL.
   
## resultant Server Details:

  Server IP Address:3.92.85.167
  Hosted site Url: http://3.92.85.167.xip.io

## create AWS account and Instance Creation
  login to aws account
  after login
  click on aws Account
  click on Goto your AWS Educate stater Account

  next click on start lab
  click on consol
  choose EC2 in All Services
  choose US East(N.virginia) as server location
  click on launch instance
  choose the ubuntu 18.04 and click on start
  click on lunch
  create new key pair

  download key pair
  
  click on lunch instance
  check the lunch istance
  click on istance id:

  ec2-3-92-85-167.compute-1.amazonaws.com
  Public DNS (IPv4)
  ec2-3-92-85-167.compute-1.amazonaws.com
  Instance state
  running
  IPv4 Public IP
  3.92.85.167

  goto description add rule in inbound
  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for 
  SSH (port 2200),
  HTTP (port 80),
  and NTP (port 123).


## Steps to configure Linux server:

   Goto linux file

   open git bash here
   ssh -i linux_server_31_01_2019.pem ubuntu@3.92.85.167
   now update the server: 
     sudo apt-get update
     sudo apt-get upgrade


  #change the ssh port from 22 to 2200:
   edit /etc/ssh/sshd_config file sudo vi /etc/ssh/sshd_config

   change port 22 to 2200
   esc :wq

  restart the server with using 'sudo service ssh restart' command


  $ ssh -i linux_server_31_01_2019.pem -p 2200 ubuntu@3.92.85.167

  sudo ufw default deny incoming
  sudo ufw default allow outgoing
  sudo ufw allow 2200/tcp
  sudo ufw deny 22
  sudo ufw default allow www
  sudo ufw default allow 123/NTP
  To check status:
     sudo ufw status

 # create a new user with name Grader:

 sudo adduser grader

 permissions to grader:
 grader
 edit the sudoers
 sudo visudo

 newfile open

 verifiy the grader as sudo permission run: 
 su - grader 
 type password:grader

 # ssh keypair for grader:
  configure key pair authenticatin to grader
  create .ssh folder:
  mkdir /home/grader/.ssh

  grader@ip-172-31-41-55:~$su grader

  grader@ip-172-31-41-55:~$sudo cp /home/ubuntu/.ssh/authorized_keys /home/grader/.ssh/authorized_keys

  grader@ip-172-31-41-55:~$chown grader.grader /home/grader/.ssh

  grader@ip-172-31-41-55:~$usermod -aG sudo grader

  change it to root by:
  $sudo su

  root@ip-172-31-41-55:/home/grader# usermod -aG sudo grader
  root@ip-172-31-41-55:/home/grader# chmod 700 /home/grader/.ssh

  root@ip-172-31-41-55:/home/grader# chmod 644 /home/grader/.ssh/authorized_keys
  root@ip-172-31-41-55:/home/grader# vi /etc/ssh/sshd_config
  pop up a file
  In this file change PermitRootLogin to no and
                    pubkeyAuthentication yes

  root@ip-172-31-41-55:/home/grader# sudo service ssh restart

## Set the time zone for grader:
   To,set time zone for grader the command as follows
   sudo dpkg-reconfigure tzdata

## STEPS TO DEPLOYEE THE PROJECT:
 #step1:
  login into grader by using following
  ssh -i linux_server_31_01_2019.pem -p 2200 grader@ip_address

  sudo apt-get update
  sudo apt-get upgrade

 # step2:
  sudo apt-get install apache2
  open browser and check the ipv4:3.92.85.167 

 sudo apt-get install libapache2-mod-wsgi-py3


step3:
enable the  apache module 
sudo a2enmod wsgi

## installing postgres softwares
 sudo apt-get install libpq-dev python-dev
 sudo apt-get install postgresql postgresql-contrib

change grader to postgres:
 sudo su - postgres
 type psql and enter

postgres=# create user catalog with password 'catalog';
 username and password is catalog
postgres=# alter user catalog createdb;

create database catalog with owner catalog
 
connect to catalog from postgres

\c catalog

# revoke permissions
revoke all on schema public from public

# grant the permisssions to catalog
 grant all on schema public to catalog

 exit from catalog and postgres:
  \q
  exit

## install Git in grader:
  sudo apt-get install git

 Go to console.google.developer.com
 create new project
 goto credentials:
   configure the domains:
   
Authorized JavaScript originsAuthorized JavaScript origins:
  	http://3.92.85.167.xip.io
        http://ec2-3-92-85-167.compute-1.amazonaws.com
      
Authorized redirect URIs:
	http://3.92.85.167.xip.io/login
	http://3.92.85.167.xip.io/gconnect
	http://3.92.85.167.xip.io/callback



# goto grader:

 cd /var/www

 sudo git clone 'https://github.com/SAIKUMARGUTTULA542/catalog.git'

 grader@ip-172-31-41-55:/var/www$ sudo chown -R grader:grader catalog
 
# rename to main file to newfilename(__init__.py) by using follwing:
 
      mv mainproject.py  __init__.py

      sudo vi __init__.py
     
      change engine to engine=create_engine('postgresql://catalog:catalog@localhost/catalog')

      sudo vi database_setup.py

      change engine to engine=create_engine('postgresql://catalog:catalog@localhost/catalog')



install pip grader:

sudo apt-get install python3-pip
sudo apt-get install python-virtualenv

sudo virtualenv -p python3 venv

change ownership to grader with:sudo chown -R grader:grader venv

Activate the new environment: . venv/bin/activate

###installing following Modules:
  sudo apt-get install python-pip
  pip install httplib2
  pip install requests
  pip install --upgrade oauth2client
  pip install sqlalchemy
  pip install flask
  sudo apt-get install libpq-dev
  pip install psycopg2-binary


 service apache2 reload
 sudo vi /etc/apache2/sites-available/catalog.conf

 it produces the empty file:
 that can be filled with this code

<VirtualHost *:80>
    ServerName 54.161.86.157.xip.io
    ServerAlias ec2-54-161-86-157.compute-1.amazonaws.com
    ServerAdmin ubuntu@54.210.140.47
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/catalog/venv3/lib/python3.6/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

## Set up the Flask application:

  create new file named as catalog.wsgi with using 'sudo vi /var/www/catalog/catalog.wsgi' command

  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0, "/var/www/catalog/")
  from catalog import app as application
  application.secret_key = 'supersecretkey'

restart the apache2 with 'sudo service apache2 restart' command

# run the files:
 python database_setup.py
 python students.py

Deactivate the virtual environment: deactivate

Disable the default Apache site: sudo a2dissite 000-default.conf

sudo apt-get update
sudo apt-get dist-upgrade

Launch the Web Application:
  Open your browser to : (http://3.92.85.167.xip.io) Open your browser to : (http://ec2-3-92-85-167.compute-1.amazonaws.com)

 
