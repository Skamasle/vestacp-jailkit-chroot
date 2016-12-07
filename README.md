# Jailkit chrooting for [Vesta Control Panel](http://vestacp.com/) __v0.9.8-17__

This is a modification I've made to VestaCP code (http://github.com/serghey-rodin/vesta) which implements Jailkit chrooting. With this modification you're able to restrict users to their home directories on SSH and SFTP. VestaCP v0.9.8-17 already have a plugin for this named **SFTP Chroot**, so this works over it, that means you need to buy the plugin for getting this to work. The difference witheen the SFTP Chroot plugin and this modification is that you're able to give users SSH or SFTP access restricted to their home directory, with the official plugin you are only able to use SFTP restriction. It is useful if for example you wanna allow a user to use *composer*, or any other shell command you desire. 

# Repository navigation
* [Requirements](https://github.com/Estilate/vestacp-jailkit-chroot#requirements)
* [Roadmap](https://github.com/Estilate/vestacp-jailkit-chroot#roadmap)
* [Installation](https://github.com/Estilate/vestacp-jailkit-chroot#installation)
* [How to?](https://github.com/Estilate/vestacp-jailkit-chroot#how-to)
* [License](https://github.com/Estilate/vestacp-jailkit-chroot#license)
    
## Requirements
* Jailkit: http://olivier.sessink.nl/jailkit/
* A clean VestaCP installation: http://vestacp.com/ - https://github.com/serghey-rodin/vesta
* VestaCP SFTP Chroot plugin license: http://vestacp.com/features/#sftpchroot

## Roadmap
> ### Planned for next version
- [ ] SSH keys support.

> ### 6/12/2016 - Initial releasment
- [x] SFTP and SSH access usign **bash** shell.
- [x] SFTP only access usign **nologin** shell.
- [x] Custom PS1 prompt support.
- [x] VestaCP version **0.9.8-17**

## Installation

Take in mind that I'm using Centos 7, your installation process may differ a little bit depending on your O.S. **I only tested the code on Centos 7, however it should work on other systems... let me know ;)**


1. **Downloading and installing Jailkit**
    
    Once you've got your O.S installed, you must login to your SSH server console as root.
    
    Execute the update command for making sure you are up to date
    ```bash
    yum update -y
    ```
    Go to http://olivier.sessink.nl/jailkit/index.html#download and copy the link of the latest jalkit.tar.gz release.
    
    Install necessary packages
    ```bash
    yum install gcc make -y
    ```
    
    Download jailkit from the copied link and uncompress it
    ```bash
    cd /tmp
    wget http://olivier.sessink.nl/jailkit/jailkit-2.19.tar.gz
    tar xvfz jailkit-2.19.tar.gz
    cd jailkit-2.19
    ```
    
    Install jailkit
    ```bash
    ./configure
    make
    make install
    ```
    
    If everything goes well you should see something like this when executing the command **jk_init**
    ```bash
    ERROR: need at least a jail directory and a configfile-section

    Usage: /usr/sbin/jk_init [OPTIONS]
    Usage: /usr/sbin/jk_init [OPTIONS] -j jaildir sections...

    -h --help              : this help screen
    -c, --configfile=FILE  : specify configfile location
    -l, --list             : list all available sections in the configfile
    -j, --jail=             : specify the jail to use.
         For backwards compatibility, if no jail is specified, the first
         argument after the options will be used as jail
    -v, --verbose          : show what is being done
    -f, --force            : force overwriting of existing files
    -k, --hardlink         : use hardlinks if possible
    ```
    
    
2. **Downloading and installing VestaCP**
    
    *Note: I've tested the code on VestaCp v0.9.8-17 and it may not work on other release.*
    
    Download the installation script
    ```bash
    cd /tmp
    curl -O http://vestacp.com/pub/vst-install.sh
    ```
    
    Run the installation script. I recommend you using the ["install command generator"](http://vestacp.com/#install) as I did
    ```bash
    bash vst-install.sh --nginx yes --apache yes --phpfpm no --vsftpd yes --proftpd no --exim yes --dovecot yes --spamassassin yes --clamav yes --named yes --iptables yes --fail2ban yes --mysql yes --postgresql no --remi yes --quota yes
    ```
    
    
3. **Downloading code from GitHub and configuring jailkit** 
    * Download all the files located on the GitHub repository folder **[/bin](https://github.com/Estilate/vestacp-jailkit-chroot/tree/master/bin)**
    * Open the file **v-add-sys-sftp-jail** with your desired editor
    * Search for **/sbin/jk_init** 
    * Modify the following lines as you prefeer and save the file:
    > You must specify which packages the user should be able to use here.
    ```bash
        /sbin/jk_init -f -j $chroot basicshell editors extendedshell netutils ssh sftp scp git
        /sbin/jk_cp -f -j $chroot /bin/id
    ```    
    * Modify **/etc/jailkit/jk_init.ini** as you prefeer
    
    
4. **Inserting modified code**
    * Login to your SFTP server as root.
    * Navigate to the directory **/usr/local/vesta/bin**
    * Upload and replace all the files that you've downloaded on the *step 3*


5. **Activating SFTP Chroot plugin**
    
    For activating the SFTP Chroot license key you must go to your recently installed VestaCP web panel: 
    * Open your browser and go to https://yourhostname:8083 and login as the **admin** user.
    * Then go to "Server" -> "Configure" -> "Vesta Control Panel Plugins", activate the plugin, insert licence key and save the form.
    
    If you did everything correctly you should now see the **/chroot** directory.
    

## How to?

* ### How do I give SSH and SFTP access to a user?
    1. Make sure the user is logged out from SFTP, this way we avoid errors...
    2. Go to your browser and navigate to https://yourhostname:8083/ logged in as **admin**
    3. *Edit* your desired user
    4. On the **SSH Access** dropdown select **bash**


* ### How do I give SFTP only access to a user?
    1. Make sure the user is logged out from SFTP and SSH, this way we avoid errors...
    2. Go to your browser and navigate to https://yourhostname:8083/ logged in as **admin**
    3. *Edit* your desired user
    4. On the **SSH Access** dropdown select **nologin**
    
    
* ### What's PS1 prompt and how can I set a custom text to it?
    > PS1 (Prompt String 1) is one of the prompts available in Linux/Unix. When you try to login to any machine, you have to enter user name and password. Once you are done with this you are presented with some info like who logged in, on what machine he logged in, what is his present working directory and if the logged in user is a super user or a normal user. This is done by using PS1 prompt which is a inbuilt shell variable. The other prompts are PS2, PS3 and PS4.
    > - http://www.linuxnix.com/linuxunix-shell-ps1-prompt-explained-in-detail/
    
    Default prompt when we login to a machine:
    ```bash
    [root@host ~]
    ```
    If you wish, you can modify the PS1 prompt to a custom text like:
    ```bash
    root@host [12:42:55] $
    # or..
    [My great Hosting] root@host >
    ```
    If you wanna do this please take a look to the following steps:
    1. Login to your SSH server console as root.
    2. Open the file **/usr/local/vesta/bin/v-add-user-sftp-jail** with your desired editor (I'll use nano)
    ```bash
    nano /usr/local/vesta/bin/v-add-user-sftp-jail
    ```
    3. Search the following line
    ```bash
    #echo 'PS1="\u@\h [\t]> "' >> /home/$user/.bash_profile
    ```
    4. Uncomment it and modify it as you want. Here you have a [tutorial for doing it](http://www.thegeekstuff.com/2008/09/bash-shell-ps1-10-examples-to-make-your-linux-prompt-like-angelina-jolie/), just write inside the " ".
    5. At the end it should look something like this
    ```bash
    echo 'PS1="\u@\h [\t]> "' >> /home/$user/.bash_profile
    ```
   
## License
This script is licensed under the [GPL v3 license](https://github.com/Estilate/vestacp-jailkit-chroot/blob/master/LICENSE)

Copyright (c) 2016 Estilate, Manuel Valle - <info@estilate.com> 

