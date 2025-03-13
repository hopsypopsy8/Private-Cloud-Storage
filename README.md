# Private-Cloud-Storage

### Intention
Other than very minor cloud usage I have no experience with any cloud service so i though making and configuring my own private cloud would give me a better understanding of the system. I made a youtube video on part of the process here 🔗 **Reference:** [Youtube Video](https://www.youtube.com/watch?v=cMgL5Ng5fzU).  

### Hardware + Software
I'm using an old toshiba laptop from ~2010 to run the ubuntu server on which the cloud storage application will run. I then have two other laptops which i can use as end nodes to acctually access the cloud application. Assume relevant internet connection harwdware (aka router from ISP) but everyone has that so :). Nextcloud is an opensource cloud application that has commercial uses so its patched frequently. 

## Software Install
### Ubuntu Server
I wiped an old 256G harddrive, then installed the ubuntu server .iso file through a usb stick. We used BalenaEtcher to flash the .iso onto the usb stick. After I booted the machine you follow the typical installation process. The only moment i had trouble was during network selection, by default wifi packages are not installed, this means you need a ethernet connection temporarily so the wifi can eventually work. I did not see any threads online in regards to this however old videos discuss the packages which need to be installed. I just used deduction that this will start working once the server boots the first time and it did. I tested by removing the ethernet and pinging 8.8.8.8 then doing ping google.com to see if DNS would resolve. Both ping attempts worked which meant my wifi connection was working. 

After server setup i realised that a lot of installation code for NextCould is copy paste, so i installed SSH packages to connect from my main laptop. This allowed for easy execution of commands, plus my old keyboard is not great so using it for long periods is not fun. 

### NextCould Application
Nextcloud documentation is a little outdated, this is immediately shown when the packages required to be installed include an old version of php. 

<img alt="image" src="https://github.com/user-attachments/assets/3fefce16-5425-4983-adad-df257681c9b3" />

You can use essentially the same commands but remove all specifications of 7.4 and it will work. After installing packages you go onto configuring mariadb so that it can store the data as required by NextCloud. Now we download the nextcloud file, they use a really annoying way of package verification however you can just use.

```
wget https://download.nextcloud.com/server/releases/latest.zip
```

This downloads the zip file directly and no verification is required, unzipping you get the nextcloud directory, you can remove the old zip file as you no longer need it.

Next we edit the http account permissions so it can use the nextcloud directory
```
chown -R www-data:www-data /var/www/html/nextcloud/
```

The next steps are taken from **Reference:** [MailServerGuru Tutorial](https://mailserverguru.com/install-nextcloud-on-ubuntu-24-04-lts/) as they have a much faster approach to install then the actual documentation (and its more recent). First you traverse to the nextcloud directory then run the following command.

```
sudo -u www-data php occ  maintenance:install --database \
"mysql" --database-name "db_name"  --database-user "user_name" --database-pass \
'user_pass' --admin-user "CHANGE admin" --admin-pass " CHANGE password"
```

If this is successful you will see a **“Nextcloud was successfully installed”.** Please remember to adjust admin user and pass as this is what you will use to login to the applicaton, you can change later its just more annoying. 

We then configure an allow list, originally it just has localhost so you need to add the ip of the server onto this list. Alternatively you can not change it and when you try to access the application it will throw the untrested domain error and you can change it then.

```
vim /var/www/html/nextcloud/config/config.php

  'trusted_domains' =>
  array (
    0 => 'localhost',
    1 => 'server ip here',   // put the ip of the server that is hosting nextcloud
  ),
```

Finally we need to configure Apache to load Nextcloud from the nextcloud directory. To do this just copy paste the relevant parts from this code, fyi this was taken from the NextCloud website, no need for any changes here.

```
vim /etc/apache2/sites-enabled/000-default.conf

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/nextcloud
        
        <Directory /var/www/html/nextcloud>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
	      </Directory>
        
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Now we can restart apache and the application should work 
```
systemctl restart apache2
```

To access the application you should go to http://server_ip/nextcloud and type in the admin details. I've included an image of me inside the application and my uploaded photo, they will have default existing photos but i just deleted them. 
<img alt="image" src="https://github.com/user-attachments/assets/400e9d92-16fb-4206-bdb7-75575a6be578" />


# Part 2 if youtube video does well



