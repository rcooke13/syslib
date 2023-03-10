# Install PHP

Use **apt** to install PHP and relevant modules.

> sudo apt install php libapache2-mod-php

Now, create a connection between PHP and Apache2:

> sudo systemctl restart apache2

Now, check its status to ensure there are no errors:

> systemctl status apache2

## Check Install

To check it's been installed and working with Apache2, create a small php file in the web document root. 

To do that, **cd** to the **/var/www/html/** directory and create a file called **info.php**:

> cd /var/www/html/
>sudo nano info.php
 
Now visit the file using the external IP address of your server:

> http://34.174.114.102/info.php

You should see a page about PHP, Apache2 and the server. 

## Basic Configurations 

If our plan is to provide for PHP, we want Apache2 to default to a file titled **index.php** instead of **index.html** file.

To do that, edit the **dir.conf** file in the **/etc/apache2/mods-enabled/** directory. 

Before we make any changes, create a backup file just in case there is a disaster.

>cd /etc/apache2/mods-enabled/
>sudo cp dir.conf dir.conf.bak
>sudo nano dir.conf

Open the file **dir.conf**. We need to change this line to this:

> DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm

Since we made a configuration change, use **apachectl** to check the configuration:

> apachectl configtest

If you get **Syntax Ok**, you can reload the Apache2 configuration and restart the service:

>sudo systemctl reload apache2
>sudo systemctl restart apache2

## Create an index.php file

Now, create a basic php page. 

**cd** back to the Apache2 document root directory and use **nano** to create and open an **index.php** file:

> cd /var/www/html/
>sudo nano index.php

Add HTML and then php to it. 

<html>
<head>
<title>Broswer Detector</title>
</head>
<body>
<p>You are using the following browser to view this site:</p>

<?php
echo $_SERVER['HTTP_USER_AGENT'] . "\n\n";

$browser = get_browser(null, true);
print_r($browser);
?>
</body>
</html>

Save the file and exit **nano**.

Visit your external IP address again:

http://34.174.114.102/


