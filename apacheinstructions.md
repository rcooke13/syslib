# Install Apache server

Before you install Apache, you need to update the system first:

>sudo apt update
>sudo apt -y upgrade

Once the machine is updated, install Apache2 using **apt**.

Use **apt search**:

> sudo apt search apache2 | head

Look for apache2 on Ubuntu since we are using Ubuntu. This package will be called **httpd**.

Install the package with the **apt** install command:

> sudo apt install apache2

## Basic checks

Ensure the server is up and running. Use **systemctl** to look up information about apache2:

>systemctl list-unit-files apache2.service
>systemctl status apache2

It should say enabled *and* running. 


### Creating a web page 

Install the web browser w3m on the command line now to look at the web page. 

Visit the sitevia the command line by writing the following:

>w3m 127.0.0.1
>w3m localhost



#### Create a web page

The default pages are at **/var/www/html**. Navigate to the directory and create a page in the text editor, nano.

Add some basic code to the page to get started:

><html>
><head>
><title>My first web page using Apache2</title>
></head>
><body>

><h1>Welcome</h1>

><p>Welcome to my web site.
I created this site using the Apache2 HTTP server.</p>

></body>
></html>

Now go to this url to see the page:

>http://34.174.114.102/index.html.original

