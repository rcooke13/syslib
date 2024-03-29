# Google Cloud setup

Instructions taken from Dr. Burns' Github instructions. 

We need to create a new virtual machine instance and configure the Google firewall to allow HTTP traffic to our Koha install. 

## New Virtual Instance

The virtual instance we have been using does not meet the memory (RAM) needs required by the Koha integrated library system. We therefore need to create a new virtual instance that has more RAM. As a refresher, see the section titled gcloud VM Instance at [Using gcloud Virtual Machines](https://cseanburns.github.io/systems-librarianship/05-using-gcloud-virtual-machines.html). However, we will modify the Series to E2 and set the Machine Type to 2 vCPU, 4 GB memory. All else, including the operating system (Ubuntu 20.04), should remain the same. Note that this is a more expensive setup. Therefore, feel free to delete this instance at the end of the semester.

## Google Cloud firewall

Later, after we install Koha, we will need to access the staff interface on a special HTTP port. HTTP (i.e., web) traffic is delivered through what are called ports. The default port for HTTP is 80, and the default port for HTTPS is 443. Since we do not have encryption enabled, this means that the Apache2 web server listens on port 80 by default for all web traffic. Firewalls are used to control incoming and outcoming traffic via ports. When we selected Allow HTTP traffic when we created our virtual instance, we instructed the Google Console firewall to allow traffic through port 80. We need to add a firewall rule to allow web traffic through port 8080. We will use port 8080 to access the Koha staff interface.

o create a firewall rule to allow traffic to port 8080, go to the Google Cloud Console:

..* Click on the hamburger ☰ icon at the top left.
..* Click on VPN Network
..* Click on Firewall
..* At the top of the page, choose Create a firewall rule (do not choose Create a firewall policy)
..* Add name: koha
..* Add description: open port 8080
..* Next to Targets, click on All instances in the network
..* In the Source IPv4 ranges, add 0.0.0.0/0
..* Click on Specified protocols and ports
> Click on TCP
>Add 8080 in the Ports box
..* Click on Create

# Install Koha Repo

## Server setup

Now let's log onto our new server and prepare it for the Koha installation.

First we need to update our local repositories:


`sudo apt update`

And then upgrade our servers:


`sudo apt upgrade`

The next two commands help save disk space. The apt autoremove command "is used to remove packages that were automatically installed to satisfy dependencies for other packages and are now no longer needed as dependencies changed or the package(s) needing them were removed in the meantime" (see man apt). The apt clean command "clears out the local repository of retrieved package files" (see man apt-get). In the following example, I combine both commands on one line:


`sudo apt autoremove -y && sudo apt clean`

Next we need to install gnupg2, which is used to create digital signatures, encrypt data, and aid in secure communication.

`sudo apt install gnupg2`

At the time of this demo, the update above downloaded a new Linux kernel. Using the new kernel requires a reboot. The reboot command will disconnect you from the server. Just wait a minute or so and then re-connect.

`sudo reboot now`

## Add Koha Repository 

When you run the `sudo apt update` command, Ubuntu syncs the local repository database with several remote repositories. These remote repositories contain metadata about the packages they contain. The syncing process identifies if any new software updates are available. The remote repositories are also used to retrieve software.

We can add repositories to sync with and to use to download software, and this includes the Koha ILS. To add the special Koha repository to our system, we use the following command:

`sudo su`

Add the Koha repository to our server:

`echo 'deb http://debian.koha-community.org/koha stable main' | sudo tee /etc/apt/sources.li`

We then add the digital signature that verifies the above repo:


`wget -q -O- https://debian.koha-community.org/koha/gpg.asc | sudo apt-key add -`

## Install Koha

Next we need to update/sync the new repository with the Koha remote repository. This just means that we use `apt update` again.

`apt update`

Now we view the package information for Koha:

`apt show koha-common`

And install it:

`apt install koha-common`

The above command will download and install a lot of additional software, and therefore the process will take several minutes.

## Configure Koha

Edit some configuration files for Koha:

`nano /etc/koha/koha-sites.conf`

In the above koha-sites.conf file, change the line that contains the following information:

`INTRAPORT="80"`

To:

`INTRAPORT="8080"`

Next install and setup mysql-server:

`apt install mysql-server`

Next we set the root MySQL password:

`mysqladmin -u root password bibliolib1`

When we installed Koha, the Apache2 web server was installed with it as a prerequisite. We need to enable URL rewriting and CGI functionality.

`
a2enmod rewrite
a2enmod cgi 
`

Now we need to restart Apache2 in the normal way:

`systemctl restart apache2`

Next we create a database for Koha:

`koha-create --create-db bibliolib`

We need to tell Apache2 to listen on port 8080:

`nano /etc/apache2/ports.conf` 

And add:

`Listen 8080`

Make sure Apache configuration changes are valid:

`apachectl configtest`

If you get an error message, trace the error in the file and line listed.

Let's restart Apache2.

`systemctl restart apache2`

We'll disable the default Apache2 setup, enable traffic compression using deflate, enable the bibliolib site, and then reload Apache2's configurations and restart again:

`
a2dissite 000-default
a2enmod deflate
a2ensite bibliolib
systemctl reload apache2
systemctl restart apache2
`

## Koha Web Installer

All the backend work is complete, and like we did with WordPress and Omeka, we can complete the installation through a web installer.

First, get Koha username and password in the following file:

`nano /etc/koha/sites/bibliolib/koha-conf.xml`

Look for the `<config>` stanza (line number 252) and the line beginning with `<user>` (line number 257). The password is on the line after (line number 258).

Make sure your URL begins with *http** and not **https**, and visit the web installer at:

`http://IP-ADDRESS:8080`

The documentation for the web installer is helpful. One thing to do is to add sample libraries and sample patrons. More generally, be sure to follow instructions as you click through each step.

[Introduction to the Koha installation process(https://koha-community.org/manual//22.11/en/html/installation.html)

## Public OPAC

When the install and setup are complete, you will have access to the staff interface. To view the public facing OPAC, you need to make a setting change.

..* Click on More in the top drop down box
..* Click on Administration
..* Click on Global System Preferences
..* Click on OPAC in the left hand side bar
..* Scroll down to the OPACBaseURL line.
..* Enter the IP address of your server: http://IP-ADDRESS
..* Click on Save all OPAC Preferences
..* Once you save these preferences, you should be able to visit your public facing OPAC at the server IP address.

## Additional Tasks

Once you've installed and setup Koha, begin to learn the system. Some example tasks:

..* Create patron accounts
..* Create bibliographic records
..* Check out books to patrons
..* Delete patron circulation history
