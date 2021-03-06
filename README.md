# Tutorial on how to code through Chrome browser

## Introduction

As web developers, we can’t help but ask since we are ultimately writing code to display information that would be seen through a browser, why not just write that code on the browser itself? The advantage here is that you could theoretically have your code in one tab, then view the progress on the webpage in a separate one. You would not have to change windows to use a separate text editor, only change tabs.

This tutorial below will set up Chrome as a development environment. It should work on all major platforms: mac OS, Windows, Linux, Chrome OS. This tutorial assumes working knowledge of the Linux/Unix command line. 

The end result will be a terminal that can be accessed in a browser tab, as shown below. 

![chrome coding screenshot](assets/chrome-tmux2.png)

## Prerequisites

The following software will be needed to successfully code in Chrome:

* Google Chrome
* Secure Shell Chrome Extension
* Terminal (built-in for mac OS and Linux users)
* Bash on Ubuntu on Windows (for Windows 10 users)
* Crouton (for Chrome OS users)
* SSH server
* Vim, a command line text editor 
* Tmux, a terminal multiplexer [recommended]

The general flow is the same across platforms:

1. Install Chrome
2. Install Secure Shell Chrome Extension
3. Setup SSH server
4. Login to SSH server through Secure Shell extension

However, the general details will differ depending on your platform of choice. Throughout the tutorial, platform-specific installation issues will be addressed.

## Chrome browser installation

Visit the following link to install Google Chrome:

https://www.google.com/chrome/browser/desktop/index.html

Install the Secure Shell Chrome Extension:

https://chrome.google.com/webstore/detail/secure-shell/pnhechapfaindjhompbnflcldabbghjo?hl=en

## Secure Shell extension setup

Once the Secure Shell extension is installed, you will want to change its default behavior from opening in a window to opening in a tab. This will make life much easier when developing web pages and needing to see on the fly changes. Also be sure to enable the bookmark bar on Chrome, so that you can easily bookmark the extension for easy access in the future. See images below for details.

![chrome-extension-menu](assets/chrome1.png) ![chrome-bookmarks](assets/chrome-bookmarks.png)

In the extensions menu, scroll down till you find Secure Shell and click "Details". From there, change the option from "Open as window" to "Open in a tab". 

![chrome-secure-shell](assets/chrome-ssh1.PNG) ![chrome-secure-shell2](assets/chrome-ssh2.PNG)

The next series of steps will diverge considerably, depending on your platform of choice and whether or not you already have an ssh server setup. Skip to the [ssh server login](#logging-in-to-ssh-server) section below if you already have an ssh server running with ssh keys. Keys are recommended over passwords for security purposes.

## Setting up the SSH server

### mac OS

The author unfortunately does not have access to a mac OS system to confirm setup instructions of the ssh server. However Apple, on their support page has instructions on how to setup an ssh server on your MacBook or any mac OS system you may own. View instructions [here](https://support.apple.com/kb/PH25252?viewlocale=en_US&locale=en_US).

### Linux

Instructions for setting up an SSH server will vary between distributions. However, the general flow should be more or less the same.

For Ubuntu- and Debian-based distributions, use the following command to ensure that the ssh server is installed:

`sudo apt-get update && sudo apt-get install openssh-server` 

Ubuntu and Debian-based distributions should automatically have the ssh server configured to run at boot time.

For Fedora, use the following two commands to install the ssh server and ensure that it runs at boot time:

`sudo dnf install openssh-server` 

`sudo systemctl enable sshd.service` 

Given the large number of Linux distributions, it is impossible to cover each one here. But the general setup remains the same: install the ssh server and make sure it is configured to automatically run at boot time.

### Windows 10

Windows 10 users can rejoice as Microsoft partnered with Canonical to create Bash on Ubuntu on Windows, running through a technology called the [Windows Subsystem for Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide). Most command line based tools should work with this tool, including the ssh server. There are a few details to address though for this to properly work.

1. Uninstall and reinstall the ssh server using the following commands:
    - `sudo apt remove openssh-server` 
    - `sudo apt install openssh-server`
2. Edit the `/etc/ssh/sshd_config` file by running the command `sudo vi /etc/ssh/sshd_config` and do the following
    - Change `Port` to 2222 (or any other port above 1000)
    - Change `UsePrivilegeSeparation` to no
    - Change `PasswordAuthentication` to yes. This will be initially necessary for logging in to the ssh server and setting up keys in the next section.
3. Restart the ssh server:
    - `sudo service ssh --full-restart`  
4. The ssh server must be turned on every time you run Bash on Ubuntu on Windows, as by default it is off. Use this command to turn it on:
    - `sudo service ssh start` [starts ssh server]
5. Alternatively, simple scripts can be written to start the ssh server automatically:
    1. Create a sshd.bat file and edit it with the following commands:
        - `vi sshd.bat`
        - Add the following code: `C:\Windows\System32\bash.exe -c "sudo /usr/sbin/sshd -D"`
        - Save the file and move it to a more accessible location, e.g. `mv ssh.bat /mnt/c/Users/YourUserName/Documents`. Make sure to match your username! Take note of this location for the next step as in Windows language this corresponds to `C:\Users\YourUserName\Documents`
    2. Create a sshd.vbs file and edit it with the following commands:
        - `vi sshd.vbs` 
        - Add the following code, making sure to put in your actual user name: 

        ```
        Set WinScriptHost = CreateObject("WScript.Shell")
        WinScriptHost.Run Chr(34) & "C:\Users\YourUserName\Documents\sshd.bat" & Chr(34), 0
        Set WinScriptHost = Nothing
        ```

        - Save the file and move it to a more accessible location, e.g. `mv sshd.vbs /mnt/c/Users/YourUserName/Documents`.
        - Open start menu, type `run`. Then type `shell:startup`. Copy the vbs file over to the Startup folder
    3. Finally, you will need to configure the ssh server to start without requiring password. Run the command `sudo visudo` and add this line to the end of the file:
        - `%sudo ALL=NOPASSWD: /usr/sbin/sshd`
6. If configured properly, the ssh server should now automatically start in the background when Windows starts.

### Chrome OS

Google's Chrome OS system does not normally permit access to a full-fledged terminal. A simple terminal can be opened up using ctrl + alt + t, but this combination won't give you the full capabilities of a Linux shell. It is therefore highly recommended that you install `crouton`, short for Chromium OS Universal Chroot Environment.

You can view documentation on how to install crouton [here](https://github.com/dnschneid/crouton). Please note that this requires putting your chromebook in developer mode! It is recommended that you only install a command line (i.e. server) version of Ubuntu, as running another desktop environment on top of Chrome OS may slow things down. The relevant command for a CLI-only installation of crouton is:

`sudo sh ~/Downloads/crouton -t core,cli-extra`

Setting up the actual ssh server requires a few additional steps:

1. Install ssh server:
    1. `sudo apt-get update && sudo apt-get install openssh-server`
2. Install iptables:
    1. `sudo apt-get install iptables`
3. Open the file `/etc/rc.local` to open firewall for ssh server:
    1. `sudo vi /etc/rc.local`
    2. Add this line to the file before the `exit 0`
        - `/sbin/iptables -I INPUT -p tcp --dport 22 -j ACCEPT`
4. To enable ssh into chroot, add these lines to `/etc/rc.local`

```
mkdir -p -m0755 /var/run/sshd
/usr/sbin/sshd
```

5. If setup properly, the ssh server will now run every time you start the chroot!

## Logging in to SSH server

It is recommended for users to setup ssh keys on their system to secure the ssh server from unauthorized logins. By default, the ssh server will allow password logins which are initially needed to setup the keys to begin with. To generate ssh keys, follow these steps:

1. Generate keys with 4096 bit RSA encryption:
    - `ssh-keygen -t rsa -b 4096`
2. The following prompts should appear. It is up to you whether or not you decide to password protect your ssh keys. This security helps if someone gets access to your computer and your keys:

        Generating public/private rsa key pair.
        Enter file in which to save the key (/home/username/.ssh/id_rsa):
        Enter passphrase (empty for no passphrase):
        Enter same passphrase again:
        Your identification has been saved in /home/username/.ssh/id_rsa.
        Your public key has been saved in /home/username/.ssh/id_rsa.pub.

    - Note that you have the option to give the keys a specific name and location to be saved. If using multiple keys (e.g. for managing different systems) it's highly recommended to give your keys unique names.
3. Now that you generated ssh keys, you will need to authenticate them on your ssh server. This command will prompt you for your user password:

    - `ssh-copy-id -i ~/.ssh/mykey username@localhost`

    Of course be sure to fill in the actual name of your ssh key and username! Also note that if you changed the port (specifically Windows 10 users) you will need to specify as follows:

    - `ssh-copy-id -i ~/.ssh/mykey -p 2222 username@localhost`

4. Now that you have authenticated your ssh keys, you might want to disable `PasswordAuthentication` for your ssh server. Run the command `sudo vi /etc/ssh/sshd_config` and change `PasswordAuthentication` to `no`. 

5. Depending on your system, you might have to copy both the public key and private key to a separate folder, for easy access for the Secure Shell extension. 
    1. For example on Linux and mac OS:
        - `cd ~/.ssh`
        - `cp id_rsa id_rsa.pub ~/Documents`
    2. Similarly on Windows 10 you could run:
        - `cd ~/.ssh`
        - `cp id_rsa id_rsa.pub /mnt/c/Users/YourUserName/Documents`
6. After creating the keys and moving them, start up the Secure Shell extension and type in your username and IP address. If the ssh server is on the same machine it should just be `username@localhost`. Make sure to specify the port if you changed it or are using Windows 10!

![chrome secure shell setup](assets/chrome-ssh3.PNG)

7. Import your ssh keys as follows as shown in the screenshot. Make sure to import both the private and public keys by using ctrl + click, or shift + click (mac OS)

![ssh keys import](assets/importing-ssh-keys.PNG)

8. Test the connection. If all goes well, you will be greeted by the terminal in your browser tab!

![ssh connection](assets/ssh-success.png)


