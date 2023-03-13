# Launching Linux Instance on AWS
 ## Provisioning an EC2 instance
 ![poll mokup](./2.png)
 
 ----------------------
 ## SSH into the instance
  > cd ~/Downloads <br>
   sudo chmod 0400 <armyandbtsforever>.pem <br>
  ssh -i <armyandbtsforever>. pem<br>
  ubuntu@<Public-IP-address>
  
  ![poll mokup](./connect.png)
  
  -------------------------------
  ## Installing Apache Server
 > #updatae a list of package in package manager<br>
  sudo apt update
  
  > Run apache2 package installation<br>
  sudo apt install apache2
  
  
  ![poll mokup](./installapache2.png)

---------------------
## Check the status of apache2 service in the OS
 > sudo systemctl status apache2
 
 ![poll mokup](./systemctlstatusapache2.png)
 
 ---------------------------
 ## Input the instance public ip address in the browser to verify Apache2 Installation
 ![poll mokup](./welcomepageubuntu.png)
 
 ----------------------
## Install MYSQL
  When prompted, confirm installation by typing Y, and then ENTER.
  > sudo apt install mysql-server
  
  -------------------------------
  Log in to mysql after installation 
  this will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running thus command.
  > sudo mysql
  
  
  ![poll mokup](./sudomysql.png)
  
  --------------------------
  Set a password for the root user using mysql_native_password as default authentication method 
  > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '< password >';



  ### Exit mysql
  > exit<br>

  it's recommend that you run a security script that comes pre-installed with MYSQL. This script will remove some insecure default settings and lock down access to your database system.
  > sudo mysql_secure_installation
  
  ![poll mokup](./ppassword.png)
  

 This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.
Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

----------------------------------
##Confirm Mysql was successfully installed by logging into the console
> sudo mysql -p

![poll mokup](./mysql-p.png)

### Exit mysql
> exit 


--------------------------------

## Installing PHP
You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases.You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

> sudo apt install php libapache-mod-php php-mysql 



----------------------------------
## Creating a Virtual Host Using Apache
We will set up a domain called singlamp.
Create the directory for projectlamp using ‘mkdir’ command and assign ownership as follows:
> sudo mkdir /var/www/test
  sudo chown -R $USER:$USER /var/www/test

![poll mokup](./creatingvirtualhost.png)
![poll mokup](./virtualhost2.png)

Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

>   sudo nano /etc/apache2/sites-available/test.conf

![poll mokup](./test.conf.png)

this creates a blank file. Pressing "i" on keyboard to enter insert mode. Then paste the following configuration into the file.

> <VirtualHost *:80> 

    #ServerName test 

    ServerAlias www.test  

    ServerAdmin webmaster@localhost 

    DocumentRoot /var/www/test 

    ErrorLog ${APACHE_LOG_DIR}/error.log 

    CustomLog ${APACHE_LOG_DIR}/access.log combined 

</VirtualHost> 

![poll mokup](./virtualhost4.png)

To save and close the file, simple hit esc on your keyboard and type :wq : for command w for write q for quit

Use the ls command to show the new file in the sites-available directory
>  sudo ls /etc/apache2/sites-available

Results:
You will see something like this:
> 000-default.conf  default-ssl.conf  test.conf

use a2ensite command to enable the new virtual host:
>  sudo a2ensite test

Disable the default website that comes installed with Apache and check if your configuration file doesnt have syntax errors and finally reload Apache do these chnages takes effect.

  >sudo a2dissite 000-default
  sudo apache2ctl configtest
  sudo systemctl reload apache2

  your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:
  >sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

![poll mokup](./url.png)

  Now go to your browser and try to open your website URL using IP address:
  >http://<Public-IP-Address>:80

------------------------------

  ## Enable Php on the website
  This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. 

  In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

>  sudo nano /etc/apache2/mods-enabled/dir.conf

![poll mokup](./sudonano.png)

Copy and paste the below, save.

> <IfModule mod_dir.c>

#Change this:<br>
#DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm<br>
#To this:<br>
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm

</IfModule>

After saving and closing the file, you will need to reload Apache so the changes take effect:

> sudo systemctl reload apache2

Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

Create a new file named index.php inside your custom web root folder:

>   vim /var/www/test/index.php

This will open a blank file. Add the following text, which is valid PHP code, inside the file:


>/<?php<br>
  phpinfo();

## When you are finished, save and close the file, refresh the page# jin
