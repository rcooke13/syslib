# Installation 

Taken from Dr. Burns' Github Instructions.

Read the following instructions, but don't install yet. 

[How to install Wordpress](https://developer.wordpress.org/advanced-administration/before-install/howto-install/)

# Customized Installation Process

After you have read through the WordPress.org documentation, follow the steps below to complete the manual install:

## Step 1: Requirements

All major software has dependencies. For example, our bare bones OPAC depends on MySQL and PHP to provide the database (MySQL) and the glue (PHP) between our HTML and the database. The same is true for WordPress. However, since WordPress is much more complicated software than our bare bones OPAC, its dependencies are stricter. This means that when we plan to download software outside of the apt ecosystem, we need to make sure that our systems meet the requirements for our installation. The [WordPress.org Requirements](https://wordpress.org/about/requirements/) page states that the WordPress installation requires at least PHP version 7.4 and MySQL version 5.7. We can check that our systems meet these requirements with the following commands:

`
php --version
mysql --version
`

The output from php --version shows that our systems have PHP 7.4.3, which is greater than PHP 7.4. The output from mysql --version show that our systems have MySQL 8.0, which is greater than MySQL 5.7. This means we can proceed.

Next, we need to add some additional PHP modules to our system to let WordPress operate at full functionality. We can install these using the apt command:

`sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl`

Then restart Apache2 and MySQL:

Restart Apache2 and MySQL:

`sudo systemctl restart apache2
sudo systemctl restart mysql`

## Step 2: Download and extract

The next step is to download and extract the WordPress software, which is downloaded as a tar.gz file. This is very much like a compressed zip file. Although we only download one file, when we extract it with the tar command, the extraction will result in a new directory that contains multiple files and subdirectories. The general instructions include:

1. Change to the /var/www/html directory.
2. Download the latest version of WordPress using the wget program.
3. Extract the package using the tar program.
4. Specifically, we do the following on the command line:

`cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
`

As noted in the WordPress documentation, this will create a directory called wordpress in the same directory. Therefore the full path of your installation will located at /var/www/html/wordpress

## Step 3: Create the database and a user

The WordPress documentation describes how to use phpMyAdmin to create the database and a user for WordPress. phpMyAdmin is a graphical front end to the MySQL relational database that you would access through the browser. But I like to minimize the software that we install on servers to reduce the server's security exposure. Therefore, we are going to create the WordPress database and a database user using the same process we used to create a database and user for our bare bones OPAC. The general instructions are:

1. Switch to the root Linux user
2. Login as the MySQL root user

Specifically, we do the following on the command line:


`sudo su
mysql -u root`

The `mysql -u root` command places us in the MySQL command prompt. The next general instructions are to:

1. Create a new user for the WordPress database
2. Be sure to replace the Xs with a strong password
3. Create a new database for WordPress
4. Grant all privileges to the new user for the new database
5. Examine the output
6. Exit the MySQL prompt

Specifically, this means the following (be sure to replaces the Xs with a unique and strong password of your own):

`create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q
`

## Step 4: Set up wp-config.php

When we created our bare bones OPAC, we created a file called login.php that contained the name of the database (e.g., opacdb), the name of the database user (e.g., opacuser), and the user's password. WordPress follows a similar process, but instead of login.php, it uses a file called wp-config.php.

Follow these general steps:

1. Change to the wordpress directory, if you haven't already.
2. Copy and rename the wp-config-sample.php file to wp-config.php.
3. Edit the file and add your WordPress database name, user name, and password in the fields for DB_NAME, DB_USER, and DB_PASSWORD.
4. This means that we specifically do the following:

`cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
`

In `nano`, add your database name, user, and password in the appropriate fields, just like we did with our login.php file for our bare bones OPAC.

Additionally, we want to disable FTP uploads to the site. To do that, navigate to the end of the file and add the following line:

`define('FS_METHOD','direct');`

## Step 5: Optional

The WordPress files were installed at /var/www/html/wordpress. This means that your site would be located at:

`http://11.111.111.11/wordpress`

If you want to, you can rename your wordpress directory to something else. The WordPress documentation uses blog as an example. But it could be something else, like the name of a fictional library that you might be using WordPress to build a site. If you decide to change it, be sure to keep the name lowercase and one word (no spaces and only alphabetic characters). For example, if I want to change mine to blog, then:

`cd /var/www/html
sudo mv wordpress blog
`

## Step 6: Change file ownership 

WordPress will need to write to files in the base directory. Assuming your still in your base directory, whether that is /var/www/html/wordpress or /var/www/html/blog or like, run the following command:

`sudo chown -R www-data:www-data *`

## Step 7: Run the install script

The next part of the process takes place in the browser. The location (URL) that you visit in the browser depends on your specific IP address and also includes the directory in /var/www/html that we extracted WordPress to or that you renamed if you followed Step 5. Thus, if my IP address is 11.111.111.11 and I renamed by directory to blog, then I need to visit the following URL:

`http://11.111.111.11/blog/wp-admin/install.php`

IF I kept the directory named wordpress, then this is the URL that I use:

`http://11.111.111.11/wordpress/wp-admin/install.php`

## Finishing installation

From this point forward, the steps to complete the installation are exactly the steps you follow using WordPress's documentation.

Most importantly, you should see a Welcome screen where you enter your site's information. The site Username and Password should not be the same as the username and password you used to create your WordPress database in MySQL. Rather, the username and password you enter here are for WordPress users; i.e., those who will add content and manage the website.

Make sure that the URL is set to http and that it's not trying to access https. Setting up an https site also generally requires a domain name, but we are not not the URL starts off with http.
