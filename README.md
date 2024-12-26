# osticket-setup
Setting up osTicket, a free, open source IT helpdesk platform.

## Creating our osTicket host

We will be hosting our osTicket helpdesk on a virtual machine in Microsoft Azure, so we will need to do the following:
1. Create a resource group to hold all of our osTicket-related resources.
2. Create a Windows 10 virtual machine inside the resoucre group.
3. Log into the Windows 10 virtual machine with a remote desktop application.

If you don't know how to do part or all of this list, I made an [Azure Basics](https://github.com/grrob015/azure-basics) tutorial that covers these topics. Once inside your VM, open this repository up and [download the needed files](https://drive.usercontent.google.com/download?id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD&export=download&authuser=0) through the link. It contains:

- HeidiSQL, an SQL client used for accessing our SQL database.
- MySQL, an opensource database system using the language SQL.
- A PHP manager for Windows IIS (Internet Information Services).
- Visual C++ Installer and redistributable.
- osTicket source and configuration files.
- PHP source and configuration files.
- URL Rewrite Module for amd64 systems, allowing for complex and more user-friend URL manipulation.

## 
