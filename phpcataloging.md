# Creating the HTML Page and a PHP Cataloging Page

We will create a basic HTML page that contains a form for entering our bibliographic data.

First, we will need to create a directory to hold the cataloging-related files. 

>cd /var/www/html
>sudo mkdir cataloging

We created a directory called **cataloging** in **/var/www/html**.

Now, to create the form, we will use nano and name it **index.html*, and it will be saved in the cataloging directory.

>cd cataloging
>sudo nano index.html

Now that nano is opened, add the following content to the **index.html** file:

<!DOCTYPE html>
<html>
<head>
    <title>Enter Records</title>
</head>
<body>
    <h1>OPAC Library Administration</h1>

    <p>This is the library administration page for entering records into the OPAC.</p>
    <p>Please do not use this page unless you are an authorized cataloger.</p>

    <form action="insert.php" method="post">
        <label for="author">Author:</label>
        <input type="text" name="author" id="author" required><br><br>

        <label for="title">Book Title:</label>
        <input type="text" name="title" id="title" required><br><br>

        <label for="publisher">Publisher:</label>
        <input type="text" name="publisher" id="publisher" required><br><br>

        <label for="copyright">Copyright:</label>
        <input type="date" id="copyright" name="copyright">

        <input type="submit" value="Submit">
    </form>
</body>
</html>

Save it and name it **index.html**.

## PHP Insert Script

The **index.html** page is a user interface, a form, for entering bibliographic data. 

The PHP script is needed now to communicate and add data from the form into our MySQL database and books table.

Just as the HTML form has to match the data structure of the books table, the PHP script also needs to match the form from the HTML page and the data structure in the books table.

In the **cataloging** directory, open nano and insert the following PHP script:

<?php

// Load MySQL credentials
require_once '../login.php';

// Establish connection
$conn = mysqli_connect($db_hostname, $db_username, $db_password) or
  die("Unable to connect");

// Open database
mysqli_select_db($conn, $db_database) or
  die("Could not open database '$db_database'");

// Prepare and bind SQL statement
$stmt = $conn->prepare("INSERT INTO books (author, title, publisher, copyright) VALUES (?, ?, ?, ?)");
$stmt->bind_param("ssss", $author, $title, $publisher, $copyright);

// Set parameters and execute statement
$author = $_POST["author"];
$title = $_POST["title"];
$publisher = $_POST["publisher"];
$copyright = $_POST["copyright"];

if ($stmt->execute() === TRUE) {
    echo "New record created successfully";
} else {
    echo "Error: " . $stmt->error;
}

// Close statement and connection
$stmt->close();
$conn->close();

echo "<p>Return to the cataloging page: <a href='http://34.174.114.102/cataloging/'>http://34.174.114.102/cataloging/</a></p>";
?>

Save it and name it **insert.php**.

## Security

To keep outsiders from accessing the MySQL database and entering records into it, we will use an authorization technique provided by the Apache2 server called **htpasswd**.

First, we need to create an authentication file in our **/etc/apache2** directory, which is where the Apache2 web server stores its configuration files.

The file will contain a hashed password and a username we give it. In the following command, set the username to libcat:

>sudo htpasswd -c /etc/apache2/.htpasswd libcat

To tell the Apache2 web server that we will use the **htpasswd** to control access to our cataloging module, we use nano to open the **apache2.conf** file:

>sudo nano /etc/apache2/apache2.conf

In the **apache2.conf** file, look for the following code block. Look at the third line in the stanza, which is line 173:

<Directory /var/www/>
  Options Indexes FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>

Carefully change the word **None** to the word **All**:

<Directory /var/www/>
  Options Indexes FollowSymLinks
  AllowOverride All
  Require all granted
</Directory>

Next, change to the cataloging directory and use **nano** to create a file called **.htaccess**:

>cd /var/www/html/cataloging
>sudo nano .htaccess

Add the following content to **.htaccess**:

AuthType Basic
AuthName "Authorization Required"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user

Check that the configuration file is okay:

>apachectl configtest

If you get a **Syntax OK** message, then restart Apache2 and check its status:

>sudo systemctl restart apache2
>systemctl status apache2

Now, you should be required to enter the username and password that you created with **htpasswd**.
