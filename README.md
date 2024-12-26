# osticket-setup
Setting up osTicket, a free, open source IT helpdesk platform.

## Creating our osTicket host

We will be hosting our osTicket helpdesk on a virtual machine in Microsoft Azure, so we will need to do the following:
1. Create a resource group to hold all of our osTicket-related resources.
2. Create a Windows 10 virtual machine inside the resoucre group.
3. Log into the Windows 10 virtual machine with a remote desktop application.

If you don't know how to do part or all of this list, I made an [Azure Basics](https://github.com/grrob015/azure-basics) tutorial that covers these topics. Once inside your VM, open this repository up and [download the needed files](https://drive.usercontent.google.com/download?id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD&export=download&authuser=0) through the link. It contains:
<!-- I'm aware that downloading stuff like this 'blind' is pretty bad, but github only allowed 25MB uploads, and some of these are bigger than that. -->

- HeidiSQL, an SQL client used for accessing our SQL database.
- MySQL, an opensource database system using the language SQL.
- A PHP manager for Windows IIS (Internet Information Services).
- Visual C++ Installer and redistributable.
- osTicket source and configuration files.
- PHP source and configuration files.
- URL Rewrite Module for amd64 systems, allowing for complex and more user-friend URL manipulation.

Extract the files on your virtual machine, and we'll be ready to begin!

![1  extracted files in a vm](https://github.com/user-attachments/assets/cdb55077-4522-450e-be96-15aa8e58af57)

## Enabling IIS (Internet Information Services) for Windows

IIS is a Windows feature that allows a computer to run a webserver to host a "website" on a local network. It won't be publicly available on the greater internet (for instance, I couldn't access my helpdesk from my real computer), but IIS is very helpful for businesses and schools that can have announcements, meeting notes, or an IT helpdesk within the network. In order to enable IIS, go to the Windows **Control Panel** and do these things:
<!-- My high school had its announcements on a locally hosted site, just like this. This was just slightly unprofessional enough to exclude from the official tutorial. -->

1. Click "Uninstall a program" under "Programs".
2. Click "Turn Windows features on or off".
3. Enable Internet Information Services.
4. Enable CGI (Shown through the expanding menus below)
5. Click "OK" and wait for the webserver to install.

![2  control panel navigation](https://github.com/user-attachments/assets/7c4f0081-f5dc-4e4e-a447-5355a3686044)

![3  enable certain features](https://github.com/user-attachments/assets/15e59566-753e-4c58-93d1-a1f9245d8c03)

In order to test that our webserver is running, we can type in `127.0.0.1` into our web browser and see what happens. That IP address belongs to a specific range of IP addresses called "loopback" addresses that the computer uses to reference itself. If we put a loopback address into our browser, Microsoft's default page for IIS should come up:

![4  loopback address test](https://github.com/user-attachments/assets/97306add-d9dd-40cf-8b2c-8676003a39a1)

## Installing a PHP Manager for IIS

A PHP manager is a prerequisite for osTicket, so run the installer named `PHPManagerForIIS_V1.5.0.msi`. We're also going to install some tools for our PHP manager, specifically the URL Rewrite module, so ruin the installer called `rewrite_amd64_en-US.msi`. Neither require special directions, simply run the installers, agree to the licenses, and click through the dialogues.

![5  supplementary picture for a short section](https://github.com/user-attachments/assets/704db9a7-df47-4d12-b57f-b92582664474)







