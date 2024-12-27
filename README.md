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

**Note:** if you don't want to download the files through google drive, the official [osTicket installation documentation](https://docs.osticket.com/en/latest/Getting%20Started/Installation.html) has a lot of helpful links.

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

Once both installers have run successfully, we need to **create a PHP folder on our C:\ drive.** It should be named exactly `PHP`. You can access your C:\ drive by either typing `C:\` into the address bar or click "This PC" on the sidebar and then select "Windows C:". When finished, your drive should look like this:

![6  the php folder](https://github.com/user-attachments/assets/156a7c1a-8bac-4cec-a076-73b1c15de988)

Next, we need to extract our PHP source and binary files into the folder we just created. From the osTicket installation files folder, right click the zipped folder named `php-7.3.8-nts-Win32-VC15-x86.zip`, select "Extract All..." then select "Browse", and choose `C:\PHP`, the folder we just created. This allows osTicket and other applications that rely on the PHP scripting language to find the binaries it needs by putting it in a place that it already 'looks' to decode files it detects as PHP. Our PHP folder should now look like this:

![7  extracted php folder](https://github.com/user-attachments/assets/81237020-e8b5-4b98-a752-98a90749d833)

## Installing C++

Run the installer named `VC_redist.x86.exe`. Just agree to the license and click "Install."

![8  cpp redist](https://github.com/user-attachments/assets/68074b31-d404-431d-b9bd-26716cd8f3f4)

## Installing SQL and our SQL client

SQL is a programming language designed to be used in databases, and it's going to hold all of the information about our helpdesk. In order to set it up, run the MYSQL installer, named `mysql-5.5.62-win32.ms`. Accept the license and click through the wizard, selecting "Typical" for your installation type.

![9  select typical](https://github.com/user-attachments/assets/4d402429-681b-4407-af94-4fa96d1abd6a)

Once the initial installation is complete, run the configuration wizard.

![10  run config wizard](https://github.com/user-attachments/assets/55572d03-5fed-4f05-b8aa-561de7bce6db)

Select "Standard Configuration", that will be more than enough for osTicket.

![11  select standard](https://github.com/user-attachments/assets/fd692368-a632-440f-84f6-65157914ecc4)

The default settings in the windows service menu are what we need, so click "Next" and then set the root password for your SQL server. I just did `root` for simplicity's sake, but this is for a demonstration and the server and virtual machine will be deleted afterwards. (⚠️ In real business circumstances, never have `root` as your root password. Choose a strong password and be very careful about who knows it or has access to it.)

![12  root password](https://github.com/user-attachments/assets/585de3a8-7219-4f01-b1f3-b85514e921b5)

After this, click "Execute" to set up the server, and MYSQL is installed and configured!

## Configuring our webserver

Now that we have PHP, SQL, and C++ installed, we have the languages we need to host osTicket on our own webserver. But if you go to IP address `127.0.0.1`, nothing looks different. That's because while we have a webserver and the languages, the webserver doesn't know that it needs to access them or that they even exist yet! In order to change that, we must open our IIS as an admin and enable certain functions in our IIS webserver. Simply type `iis` into your windows search menu and right click Internet Information Services Manager, selecting "Run as administrator".

![13  accessing the iis menu](https://github.com/user-attachments/assets/b613367f-8182-4e1b-a509-86ba90c6d125)

In order to register a new PHP manager, we must select "PHP Manager" from the IIS Manager menu that pops up, then do the following:

1. Click "Register new PHP version"
2. Click the three dots to browse to a file
3. Select the executable from `C:\PHP`

![14  3 step process to register php](https://github.com/user-attachments/assets/ae82a895-4459-4db6-a6d9-42523c1654e4)

Once you register it, you should see the PHP settings and configurations change from "Not available" to "7.3.8", with the local files in C:\PHP referenced for configurations and settings. In order to apply the changes we have just made, we must **restart our webserver**. You can do this by selecting the webserver in the left hand sidebar and then clicking "Restart" under "Manage Server" in the right hand sidebar.

![15  restart webserver](https://github.com/user-attachments/assets/99fd4e6e-2317-44e2-8029-8569096f018b)

## Installing osTicket

Now that our webserver can support osTicket, we can finally install it. Start by unzipping the file named `osTicket-v1.15.8.zip` to a location of your choice. Once it's done extracting:

1. Move the folder named `upload` into the folder `C:\inetpub\wwwroot` on your virtual machine.
2. rename the `upload` folder to be **EXACTLY** `osTicket`. (⚠️ I really mean exactly.)

![16  the big move](https://github.com/user-attachments/assets/17baa7c8-ca9b-4a02-8800-3ded2fd95944)

Restart your IIS webserver again to register the new files.

![15  restart webserver](https://github.com/user-attachments/assets/7eb7777c-1a8e-414c-b6c2-f493ae34c31c)

## More IIS webserver configuration

osTicket is now officially installed! In order to view our page, we can either type `localhost/osTicket` into our browser or use the IIS Manager to access the page. On the sidebar, open "Sites", then "Default Web Site", then click "osTicket" and then "Browse *80 (http)" on the right hand sidebar (**pictured below**):

![17  osticket exists now](https://github.com/user-attachments/assets/c37ce001-0423-4f21-a0f5-89013e1ba6bf)

You'll notice on the site that there are a list of features that osTicket supports but may not be enabled or functioning. osTicket doesn't need all of these, but some functionality may be removed, and it's good practice to enable features with our PHP manager, so we'll fix these!

![18  things we must fix](https://github.com/user-attachments/assets/4cdb8eba-68a8-45e6-af42-06ffe97e31d7)

Go to the PHP manager for osTicket with your IIS Manager. (⚠️ Be ABSOLUTELY SURE that you're modifying your PHP manager for osTicket, NOT the entire webserver!)

![19  make sure its os ticket](https://github.com/user-attachments/assets/610f111e-ac43-4fdf-af00-39e0580bbd4c)

Click "Enable or disable an extension" at the bottom of the PHP Manager menu.

![20  php extensions](https://github.com/user-attachments/assets/77e9a2e7-3f54-4a9d-bed1-0d5bacab7717)

Once in the list of PHP extensions you can enable or disable, enable these three extensions:

- php_imap.dll
- php_intl.dll
- php_opcache.dll

Simply right click them and select "Enable" from the list of options. (The webserver does NOT need to be refreshed to register the changes.)

![21  enabling an extension example](https://github.com/user-attachments/assets/915816cd-e15b-4919-8646-d8dcaf4e443d)

Navigate back to `localhost/osTicket`  in your browser and note the changes!

![22  updated features](https://github.com/user-attachments/assets/62009578-8fc9-4b8f-816d-f81354c11c76)

## osTicket config work

Now that our webserver is fully configured for osTicket, it's time to work on the osTicket configuration itself! Start by browsing to `C:\inetpub\wwwroot\osTicket\include` and rename the file named `ost-sampleconfig.php` to `ost-config.php`. (⚠️ The new file name should be EXACTLY that!) This is just a very easy way to give osTicket the default config with a minimal amount of effort!

![23  renaming config](https://github.com/user-attachments/assets/4bbaee41-507c-4102-9270-3524f1c39d0c)

We also need to enable osTicket to make changes to the config file we've just given it, so we need to change the file permissions that `ost-config.php` has. To do this:

1. Right click `ost-config.php` and select "Properties"
2. Go to the "Security" tab and click "Advanced"
3. Click "Disable inheritance"
4. Select "Remove all inherited permissions from this object."

The steps are shown visually below:

![24  steps to remove inheritance](https://github.com/user-attachments/assets/c89f525b-01ae-4082-a181-04a5dbd96302)

To give access to osTicket to modify and read the file, click "Add" in the advanced security settings, click "select a principal", type "everyone", and then check the "Full control" box for the specific permissions that everyone will have. Your permissions shoudl look like this:

![25  permission granted](https://github.com/user-attachments/assets/3e2364d2-2d60-4685-8137-348366aa7409)

⚠️ **BIG WARNING** ⚠️ This is not a cybersecurity tutorial! This is a demonstration of the setup process of osTicket! This is the fastest and simplest way to get what we need done. In a real business scenario, __follow the rule of least permissions__ and **only allow the user that osTicket represents** access to the file!

## osTicket SQL database setup

We're in the home stretch now! Navigate back to `localhost/osTicket` and click "Continue" at the bottom of the screen. Fill in the information you need to for the helpdesk and read the next line after you get to the section labelled "Database Settings".

We have MYSQL installed on our computer, but unless we're a programming whizz we can't do much with it right now. Run the installer named `HeidiSQL_12.3.0.6589_Setup` to install a visual SQL client that will allow us to work more easily with our databases. No settings need changed for our purposes, so just accept the license and click through the installer.

![26  heidi install picture](https://github.com/user-attachments/assets/44e9598f-0caa-43cc-863b-5af3eeecd4fb)

We can now use HeidiSQL to create a database that osTicket will use to store and manage information like tickets and users! Launch HeidiSQL (either from the desktop icon, start menu, or the installer option) and click "New" in the bottom left hand corner. (⚠️ Don't click the arrow on the right hand side of the button.) Log into your existing SQL server with the password you created when installing MYSQL, click "Open", and you're in!

![27  heidisql](https://github.com/user-attachments/assets/15016d74-a32d-40f9-a7c2-3c13e73ee1d4)

![28  im in](https://github.com/user-attachments/assets/8645a1b6-5e03-49c6-b246-e99551034cfc)

Once you're logged into your local SQL server, create a new database named `osTicket` by right clicking the server, selecting "Create", then "Database" (shown below):

![29  create database](https://github.com/user-attachments/assets/6d2ecc79-1f1d-48dc-9aa3-4ecc79d525ab)

Then, back in your osTicket page setup, fill in the information to link the database to osTicket and give it the credentials it needs to log into your SQL server.

![30  the final install](https://github.com/user-attachments/assets/3d5c5c0f-ce56-4b5a-b80e-bfeee9785056)

If you've followed along properly, you should see this waiting for you once osTicket finishes installing! **The very last steps** are to go to `C:\inetpub\wwwroot\osTicket` and delete the `setup` folder for security reasons, then run the `chmod 644 ost-config.php` command in an administrator PowerShell to remove write access from our config file for security reasons.
<!-- Neither of these are like, necessary for the purposes of this demonstration, but they remove an annoying error that would live at the top of our webpage and I don't want my screenshots. -->

![31  end result](https://github.com/user-attachments/assets/a37b1aef-7e2f-4450-88d8-b3a43035778e)

Congratulations! You've successfully installed osTicket manually! In the [linked repository](https://github.com/grrob015/osticket-settings), I'll be showing some ways that osTicket can be configured post-install.
