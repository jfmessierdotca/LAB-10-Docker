## CST 8254 Lab 1

Version 1.1

**What you will do:**

1. Install the Raspberry Pi OS
2. Configure your Raspberry Pi as a LAMP stack.
3. Install Wordpress on the Raspberry Pi.
4. Install Webmin on the Raspberry Pi.
5. Practice some Linux commands.
6. Checking your lab

### Task 1:

1. Install Raspberry Pi OS using Raspberry Pi Imager  [https://www.raspberrypi.org/software/]
   
2. Download, install and start the Imager.
   Select Device Raspberry 4 (it's the same as the 400)
   Select Raspberry Pi OS (64-BIT) and recommended software for the OS from the first dropdown list.   
   Connect your microSD card within an adaptor on your laptop USB port (USB-3)   
   Click Storage and select the desired device, Click NEXT   
   Click Edit Settings   
      Set hostname (your username), Username (pi) and the Password (Welcome.123)   
      Configure the Wireless LAN with your home network SSID and Password, Wireless Lan Country to CA   
      Set Locale Settings to America/Toronto for timezone and "us" the keyboard layout   
   Click SAVE   
   Back to the Imager, click YES to apply the OS customization settings   
   Click YES to the warning window   
   (You may get prompted to authorize the override of your microSD card, or to wnter your password)   
   NOTE: You may want to repeat this multiple time for each microSD card if you have more than one for your PI400
   
4. If you have a display with HDMI input and access to your router, plug all the cables, and proceed to step 5 with the installation.
   
5. If you want to do a keyboardless, screenless, wireless install:
   - Prepare your microSD card
   - Use the settings button to setup the pi name (it must be your algonquin user name). wireless settings and password.

   - If you have a Windows machine, **install OpenSSH**, start Settings then go to Apps > Apps and Features > Manage Optional Features. Scan this list to see if **OpenSSH client** is already **installed**. If not, then at the top of the page select "Add a feature", then: To **install** the **OpenSSH client**, locate "**OpenSSH Client**", then click "**Install**".

   - Get the IP address of your Pi by issuing the command `ping username.local` from a Terminal window/Command prompt window.
   
6. Then issue the command `ssh pi@<ipAddressOfYourPi>` where<ipAddressOfYourPi> is the Ip obtained from the previous step.
7. Start a terminal session then type `sudo raspi-config` . Navigate the various menu options to 

   - make sure that SSH, VNC, SPI, I2C are enabled. 
   - make sure you boot automatically in the GUI using the user pi.
   - set the time zone if you did not in the microSD card preparation.

### Task 2: Configure the Raspberry Pi as a LAMP stack

Install the following packages on your Pi using `apt-get`. Install them in the following order

1. Start by issuing the following commands from a terminal/command prompt window.
   ```
   sudo apt-get update
   sudo apt-get dist-upgrade
   ```

3. Apache2: `sudo apt-get install apache2 -y`

4. Mariadb: `sudo apt-get install mariadb-server`

   Once installed, issue the following commands from a terminal/command prompt window. Make sure you replace 'password' with a password of your choice.

   ```
   sudo mysql
   
   CREATE USER 'pi'@'localhost' IDENTIFIED BY 'password';
   
   GRANT ALL PRIVILEGES ON *.* TO 'pi'@'localhost';
   
   FLUSH privileges;
   
   EXIT;
   sudo service mysql restart
   ```

5. PHP: `sudo apt-get install php`

6. Phpmyadmin: `sudo apt-get install phpmyadmin`
   Install it on Apache, you may need to create a password for phpmyadmin database access.

### Task 3: Install Wordpress on the Raspberry Pi.

Install a copy of Wordpress on your Pi.

The following site will assist you:

https://projects.raspberrypi.org/en/projects/lamp-web-server-with-wordpress/5

When setting up the wordpress database, log on using the user you created instead of using the`root` user as specified.

### Task 4: Install Webmin on the Raspberry Pi

Install a copy of `webmin` on your raspberry pi.

1. In a terminal/command prompt window 

   - `sudo nano /etc/apt/sources.list`
   - add the following line to the file:
     `deb https://download.webmin.com/download/repository sarge contrib`
   - `ctrl-o` to save the file
   - `ctrl-x` to exit

2. Issue the following commands ONE BY ONE:
   ```
   wget https://download.webmin.com/jcameron-key.asc
   sudo apt-key add jcameron-key.asc
   sudo apt-get install apt-transport-https
   sudo apt-get update
   sudo apt-get install webmin
   ```

### Task 5: Practice some Linux commands

Download the following Unix tutorial file named unixtutor.tar.gz from this repo. 

Once downloaded, use filezilla to copy the file to the raspberry pi. Then, on the pi, issue the following commands:

```
tar -xf unixtut.tar.gz
sudo cp -R unixtut /var/www/html
```

Complete tutorials 1 to 4

### Task 6: Checking your lab

Make a screenshot of Wordpress running, phpmyadmin running and Webmin running and upload to GitHub.

### Marking Scheme

| **Rubric** This lab has a maximum mark of 10, awarded according to the following rubric. |          |
| ------------------------------------------------------------ | :------: |
| **Criteria**                                                 | **Mark** |
| Superior capability. Lab submitted meets or exceeds expected standards |    10    |
| Satisfactory capability, acceptable product/result           |    7     |
| Marginal capability, substandard product/result              |    5     |
| No capability, unacceptable product/result. Work not submitted |    0     |

Note that 40% of your final grade comes from the grades you obtained from your labs and assignments.
