## Using gcloud for Virtual Machines

Instructions taken from Dr. Burns' Github instructions. 

## Google Cloud / gcloud

### Google Account

We need to have a personal Google account to get started with gcloud. I imagine most of you already have a Google account, but if not, go ahead and create one at [https://www.google.com(https://www.google.com/).

### Google Cloud (gcloud) Project

The gcloud software helps you us on a Google Cloud project on your own system. Once you've created that project, you can enable billing for that project, and then install the gcloud software on your local machine.

Follow Step 1 at the top of the [Install the gcloud CLI](https://cloud.google.com/sdk/docs/install-sdk) page to create a new project. Review the page on [creating and managing projects](https://cloud.google.com/resource-manager/docs/creating-managing-projects#gcloud).

When you create your project, you can name it anything, but try to name it something to do with this course. E.g., I am using the name syslib-2023. Avoid using spaces when naming your project.

Then click on the Create button, and leave the organization field set to No Organization.

### Install the latest gcloud CLI version

After you have set up billing, the next step is to install gcloud on your local machines. The [Install the gcloud CLI](https://cloud.google.com/sdk/docs/install-sdk) page provides instructions for different operating systems.

There are installation instructions for macOS, Windows, Chromebooks, and various Linux distributions. Follow these instructions closely for the operating system that you're using. Note that for macOS, you have to choose among three different CPU/chip architectures. If you have an older macOS machine (before November 2020 or so), it's likely that you'll select macOS 64-bit (x86_64). If you have a newer macOS machine, then it's likely you'll have to select macOS 64-bit (arm64, Apple M1 silicon). It's unlikely that any of you are using a 32-bit macOS operating system. If you're not sure which macOS system you have, then let me know and I can help you determine the appropriate platform. Alternatively, follow these instructions to find your processor information:

..* click on the Apple menu
..* choose About This Mac
..* locate the Processor or Chip information

After you have downloaded the gcloud CLI for your particular OS and CPU architecture, you will need to open a command prompt/terminal on your machines to complete the instructions that describe how to install the gcloud CLI. macOS uses the Terminal app, which can located using Spotlight. Windows user can use Command.exe, which can be located by search also.

Windows users will download a regular .exe file, but macOS users will download a .tar.gz file. Since macOS is Unix, you can use the mv command to move that file to your $HOME directory. Then you extract it there using the tar command, and once extracted you can change to the directory that it creates with the cd command. For example, if you are downloading the X86_64 version of the gcloud CLI, then you would run the following commands:

For macOS users, this assumes the .tar.gz file was downloaded to your default Downloads folder:

`cd ~/Downloads/
mv google-cloud-cli-392.0.0-darwin-x86_64.tar.gz ~/
cd ~/
tar -xzf google-cloud-cli-392.0.0-darwin-x86_64.tar.gz
cd google-cloud-sdk
`

Modify the above commands, as appropriate, if you're using the M1 or the M2 version of the gcloud CLI.

### Initializing the gcloud CLI 

Follow the instructions from the Google Cloud documentation for your operating system. 

Once you have downloaded and installed the gcloud CLI program, you need to initialize it on your local machine. Scroll down on the [install page](https://cloud.google.com/sdk/docs/install-sdk) to the section titled Initializing the gcloud CLI. In your terminal/command prompt, run the initialization command, per the instructions at the above page:

`gcloud init`

And continue to follow the above instructions.

### gcloud VM Instance

Log into [Google Cloud Console](https://console.cloud.google.com/).

Create the first virtual machine instance. 

We will create our VM using the gcloud console. To do so, follow these steps from the Project page:

..* Click on the hamburger icon (three vertical bars) in the top right corner.
..* Click on Compute Engine and then VM instances
..* Make sure your project is listed.
..* Next, click on Create Instance.

Provide a name for your instance.

..* Under the Series drop down box, make sure E2 is selected.
..* Under the Machine type drop down box, select e2-micro (2 vCPU, 1 GB memory)
..* This is the lowest cost virtual machine and perfect for our needs.
..* Under Boot disk, click on the Change button.
..* In the window, select Ubuntu from the Operating system drop down box.
..* Select Ubuntu 20.04 LTS x86/64
..* Leave Boot disk type be set to Balanced persistent disk
..* Disk size should be set to 10 GB.
..* Click on the Select button.
..* Check the Allow HTTP Traffic button

Finally, click on the Create button to create your VM instance.

### Connect to our VM

After the new VM machine has been created, we need to connect to it via the command line. macOS users will connect to it via their Terminal.app. Windows users can connect to it via their command prompt.

We use a ssh command to connect to our VMs. The syntax follows this pattern:

`gcloud compute ssh --zone "zone-info" "name-info" --project "project-id"`

The values in the double quotes in the above command can be located in your Google Cloud console and in your VM instances section. 

### Update our Ubuntu VM

The VM will include a recently updated version of Ubuntu 20.04, but it may not be completely updated. Thus the first thing we need to do is update our machines. On Ubuntu, we'll use the following two commands, which you should run also:

`
sudo apt update
sudo apt -y upgrade
`

Then type exit to logout and quit the connection to the remote server.

`exit`

### Snapshots

Lastly, we have installed a pristine version of Ubuntu, but it's likely that we will mess something up as we work on our systems. Or it could be that our systems may become compromised at some point. Therefore, we want to create a snapshot of our newly installed Ubuntu server. This will allow us to restore our server if something goes wrong later.

To do it:

1. In the left hand navigation panel, click on Snapshots.

2. At the top of the page, click on Create Snapshot.

3. Provide a name for your snapshot: e.g., ubuntu-1.

4. Provide a description of your snapshot: e.g.,

5. This is a new install of Ubuntu 20.04.

6. Choose your Source disk.

7. Choose a Location to store your snapshot.

> To avoid extra charges, choose Regional.
> From the drop down box, select the same location (zone-info) your VM has

8. Click on Create

**Note**: There's billing involved in this so ensure you have the money to pay and monitor billing.
