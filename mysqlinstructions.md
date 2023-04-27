# MySQL Installation & Set-up

Install the MySQL Community Server:

`sudo apt install mysql-server`

This should start and enable the database server. Check that it's running and enabled using the `systemctl` command:

`systemctl status mysql`

You can log into the database to test it. You'll need to become the *root Linux user*. To do so, use the following command:

`sudo su`

Log into MySQL as the root user:

`mysql -u root`

Request a list of the databases:

`show databases;`

The following databases should be returned:

`+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.002 sec)
`

# Create and set up a regular user account

The *MySQL root user* should be for special cases. So you need a *regular user account* or more as needed.

To create a regular user account, use the `create` command. In the below example, create a user called opacuser with a difficult password. 

`create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';`

If it returns with a *Query OK* message, the user was created. 

# Create a Practice Database

As the root database user, we'll create a new database for a regular, new user. 

The regular user will be granted all privileges on the new database, including all its tables. Other than granting all privileges, we could limit the user to specific privileges, including: CREATE, DROP, DELETE, INSERT, SELECT, UPDATE, and GRANT OPTION. Such privileges may be called operations or functions, and they allow MySQL users to use and modify the databases, where appropriate. For example, we may want to limit the opacuser user to only be able to use SELECT commands. It totally depends on the purpose of the database and our security risks.

From the MySQL query prompt, run the following commands to create a new database *opacdb* and to grant all privileges to *opacdb* to the MySQL user *opacuser*:

`create database opacdb;
grant all privileges on opacdb.* to 'opacuser'@'localhost';
show databases;
`

Exit out of the MySQL database as the *root MySQL user*, and then exit out of the *root Linux user account*, and you should be back to your normal Linux user account:

`\q`

And exit out of the Linux root user account:

`exit`

# Logging in as regular user and creating tables

We created a new MySQL user named *opacuser* and a new database for *opacuser* that is called *opacdb*. When we run the `show databases` command as the opacuser user, we should see the opacdb database. Note below that I use the -p option. This instructs MySQL to request the password for the opacuser user, which is required to log in.

`mysql -u opacuser -p`

Then from the MySQL prompt, list the available databases and switch to the new *opacdb* database:

`show databases;
use opacdb;
`

In the following code, create and define a new table for our opacdb database. The table will be called books, and it will contain data describing some books. We will keep this table very simple and use only three fields:

`
create table books (
id int unsigned not null auto_increment,
author varchar(150) not null,
title varchar(150) not null,
copyright date not null,
primary key (id)
);
`

You can confirm that the table was created by running the following two commands, which lists the available tables and then describes the books table:

`show tables;
describe books;
`

Now create records for that table. 

# Adding records into the table 

We can populate our opacdb database with some data. We'll use the insert command to add our records into our *distribution* table:

`insert into books (author, title, copyright) values
('Jennifer Egan', 'The Candy House', '2022-04-05'),
('Imbolo Mbue', 'How Beautiful We Were', '2021-03-09'),
('Lydia Millet', 'A Children\'s Bible', '2020-05-12'),
('Julia Phillips', 'Disappearing Earth', '2019-05-14');
`

Now we can view all the records that we just created with the MySQL `select` command:

`select * from books;`

Now let's test our table. 

# Testing Commands

We will complete the following tasks to refresh our MySQL knowledge:

..* retrieve some records or parts of records,
..* delete a record,
..* alter the table structure so that it will hold more data, and
..* add a record

**each MySQL command ends with a semi-colon. Some of the following MySQL commands are single-line, but others are multi-line. Regardless if a MySQL command is one-line or multi-line, it doesn't end until it ends with a semi-colon:**

`
select author from books;
select copyright from books;
select author, title from books;
select author from books where author like '%millet%';
select title from books where author like '%mbue%';
select author, title from books where title not like '%e';
select * from books;
alter table books add publisher varchar(75) after title;
describe books;
update books set publisher='Simon \& Schuster' where id='1';
update books set publisher='Penguin Random House' where id='2';
update books set publisher='W. W. Norton \& Company' where id='3';
update books set publisher='Knopf' where id='4';
select * from books;
delete from books where author='Julia Phillips';
insert into books
(author, title, publisher, copyright) values
('Emma Donoghue', 'Room', 'Little, Brown \& Company', '2010-08-06'),
('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000-01-27');
select * from books;
select author, publisher from books where copyright < '2011-01-01';
select author from books order by copyright;
\q
`

# Install PHP and MySQL Support

The next goal is to complete the connection between PHP and MySQL so that we can use both for our websites.

First install PHP support for MySQL. We're installing some modules alongside the basic support. These may or may not be needed, but I'm installing them to demonstrate some basics.

`sudo apt install php-mysql php-mysqli`

And then restart Apache2 and MySQL:

`sudo systemctl restart apache2
sudo systemctl restart mysql`

## Create PHP Scripts

In order for PHP to connect to MySQL, it needs to authenticate itself. To do that, we will create a **login.php** file in **/var/www/html**. We also need to change the group ownership of the file and its permissions so that the file can be read by the Apache2 web server but not by the world, since this file will store password information.

`cd /var/www/html/
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
ls -l login.php
sudo nano login.php
`

In the file, add the following credentials. If you used a different database name than **opacdb** and a different username than **opacuser**, then you need to substitute your names below. You need to use your own password where I have the Xs:

`
<?php // login.php
$db_hostname = "localhost";
$db_database = "opacdb";
$db_username = "opacuser";
$db_password = "XXXXXXXXX";
?>
`

Next we create a new PHP file for our website. This file will display HTML but will primarily be PHP interacting with our books database.

Create a file titled opac.php.

`sudo nano opac.php`

Then copy over the following text:

`
<html>
<head>
<title>MySQL Server Example</title>
</head>
<body>

<h1>A Basic OPAC</h1>

<p>We can retrieve all the data from our database and book table
using a couple of different queries.</p>

<?php

// Load MySQL credentials
require_once 'login.php';

// Establish connection
$conn = mysqli_connect($db_hostname, $db_username, $db_password) or
  die("Unable to connect");

// Open database
mysqli_select_db($conn, $db_database) or
  die("Could not open database '$db_database'");

echo "<h2>Query 1: Retrieving Publisher and Author Data</h2>";

// Query 1
$query1 = "select * from books";
$result1 = mysqli_query($conn, $query1);

while($row = $result1->fetch_assoc()) {
    echo "<p>Publisher " . $row["publisher"] .
        " published a book by " . $row["author"] .
        ".</p>";
}

mysqli_free_result($result1);

echo "<h2>Query 2: Retrieving Author, Title, Date Published Data</h2>";

$result2 = mysqli_query($conn, $query1);
while($row = $result2->fetch_assoc()) {
    echo "<p>A book by " . $row["author"] .
        " titled <em>" . $row["title"] .
        "</em> was released on " . $row["copyright"] .
        ".</p>";
}

// Free result2 set
mysqli_free_result($result2);

/* Close connection */
mysqli_close($conn);

?>

</body>
</html>
`

Save and exit out of `nano`.

## Test Syntax

After you save the file and exit the text editor, we need to test the PHP syntax. If there are any errors in our PHP, these commands will show the line numbers that are causing errors or leading up to errors. Nothing will output if all is well with the first command. If all is well with the second command, HTML should be outputted:

`sudo php -f login.php
sudo php -f index.php
`


