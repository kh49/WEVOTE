# Finn-Perkins Lab Linux Virtual Machine Installation Tutorial 
This tutorial is intended for beginner Linux and WEVOTE users, and will detail the steps required to install a Linux virtual machine and interface with the Extreme cluster at UIC. 

## Installing Linux Virtual Machine
Since WEVOTE currently only supports Linux, a copy of Linux must be installed on your computer to properly interface with and install WEVOTE. If your computer already has a Linux virtual machine installed or is running Linux, skip this section.

### Installing VM VirtualBox
VirtualBox allows a copy of Linux to run on your computer without having to fully install the operating system. Download and install the [latest version]( https://www.virtualbox.org/wiki/Downloads) for your computer.

Once installed, VirtualBox requires a copy of Linux to run. You may choose any up-to-date version of Linux for VirtualBox. Links for download and installation of Bio-Linux, a version specialized for bioinformatics analysis is below:

> http://environmentalomics.org/bio-linux-download/

> http://environmentalomics.org/bio-linux-installation/

### Virtual Machine Configuration
After installation, open VirtualBox and  boot up the virtual machine, and select "Import Appliance" from the File menu. Select the Bio-Linux OVA or other OVA/OVF file you downloaded as the appliance to import. Click next.

On the following page, select "Reinitialize the MAC address of all network cards" then click "Import". Once the virtual machine has been imported, you should see a new entry in VirtualBox's list of virtual machines on the left side of the window. Click once to select the new machine then select "Settings" from the menu above the virtual machine list. Go to the "Display" tab and make sure Video Memory is set to at least 128MB. Click "Ok" to exit the settings prompt. You are now ready to boot the virtual machine.

_Notes: 
1. While Bio-Linux defaults to 2 GB of virtual memory, you may need more if you plan on working with large files locally and not just on the Extreme cluster. You can change these settings through your virtual machine system settings.
2. Windows users may need to enable Virtualization Technology in their BIOS before they can boot the virtual machine. VirtualBox will prompt you if you have not enabled it._

### Running the Virtual Machine
From VirtualBox, double click your new virtual machine to boot it. The first time the machine runs, the display may be very small. You can adjust it by selecting a larger size from the "Virtual Screen" settings in the "View" menu. Once the machine is booted, you may be prompted to upgrade Ubuntu. Click "Do Not Upgrade".

#### User Accounts
The default account on Bio-Linux is the manager account with the password "manager" (this may differ if you downloaded a different Linux OS). You can create your own account by opening System Settings (gear icon on app bar) and going to User Accounts. Unlock the User Accounts dialog page by clicking Unlock in the upper right hand corner and enter the password "manager" when prompted. 
![user account creation](https://raw.githubusercontent.com/pophipi/WEVOTE/master/images/useraccountcreation.png)

Then click the "+" sign in the lower left to add an account. If you chose to add your own account instead of using the manager account, be sure to set your account as an _Administrator_ instead of _Standard_ account type in the prompt that appears. Set a password for your account by clicking next to the password field after account creation. You can toggle Automatic Login so the next time the machine boots, you will login to your own account.

### Checking Your Shell Version (Optional)
Linux terminals run through a protocol called a shell. We will use the bash shell for all WEVOTE and Extreme work. To check if you are using the bash shell, open up a terminal:
![terminal location](https://raw.githubusercontent.com/pophipi/WEVOTE/master/images/terminallocation.png)

Then, enter the following:
```
echo $SHELL
```

If your shell is bash, the previous command will return “/bin/bash”.

Set the default shell as bash if it isn’t already:
```
chsh
ENTER PASSWORD
/bin/bash
```

You need to logout for the change to take effect. Your shell prompt (text appearing before your typed commands) should have a “$” at the end of it. 
![bash config](https://raw.githubusercontent.com/pophipi/WEVOTE/master/images/bashconfiguration.png)

_Note: If you want to chance the colors of your terminal, you go to Profile Preferences from the Edit menu which appears at the menu bar on the top of the screen when terminal is selected as the active window._

### Shared Clipboard
By default, your virtual machine does not share any information with your actual computer. If you are viewing this tutorial in your actual computer's browser, you won't be able to copy code over. To fix that, you can enable clipboard sharing by going to the Devices menu in VirtualBox while running your virtual machine and setting the "Shared Clipboard" option to "bidirectional". This will allow you to copy files to and from your virtual machine to your actual computer. 

## Basic Shell Commands
All interaction with the computing cluster will be through the Linux shell terminal. It is recommended that you read through some basic [tutorials](http://linuxcommand.org/lc3_learning_the_shell.php) for navigating the Linux shell and writing shell scripts. You don’t need to be an expert, but should know some of the commands and basic syntax. Below is a list of important commands for navigating the shell and some basic syntax.
```
cd – changes directories
	cd with no commands returns to the home directory
	cd /PATH/ changes directory to designated path
	Additional syntax: ~ is the home directory, . is the current directory, .. is the previous (parent) directory
	
pwd - displays path of current directory
	
ls – lists files in the current directory
	Additional syntax: ls -l will give more detailed file information
	
less <FILENAME> – view file

vim <FILENAME> – edit file
	Additional syntax: vim is entirely command line based. 
	To input text, you need to type "i" to enter insert mode.
	To exit input mode, press Esc.
	To save and quit, type :wq. Make sure you are not in insert mode.
	To quit without saving, type :q!.

cp <FILENAME1> <FILENAME2> – copy file1 to file2
	Additional syntax: cp -r directoryname destination will copy the whole directory, including all contents. This command also works with mv and rm. 
	
mv <FILENAME> <DESTINATION> – move file

rm <FILENAME> – delete file
	Note that files deleted this way are not easily recoverable.  

mkdir <DIRECTORYNAME> – creates a directory in the current working directory 
```

_Note: If you encounter an unfamiliar command, you can usually add -h or --help at the end of the command to see help text for it._ 

## SSH Login
To login to your UIC Extreme account you can use the following command:
```
ssh <YOURNETID>@login-1.extreme.uic.edu
```

If you receive a warning about the authenticity of the host, type yes and hit enter to ignore the warning and continue connecting. You will be prompted for your password.

After successfully entering your password, your shell prompt should change to “-bash-4.1$”. This prompt indicates that you are interfacing directly with your account on the Extreme cluster and not with your own Linux installation.
![extremelogin prompt](https://raw.githubusercontent.com/pophipi/WEVOTE/master/images/extremeloginbash.png)

### RSA Keys
The previous login method is straightforward, but will prompt you to enter your password every time and prevent others from transferring important files like the WEVOTE_PACKAGE folder to you. To allow faster login and allow authorized users to transfer files to you, SSH needs to be configured with RSA keys. This means generating a RSA key for each machine that will be authorized access to your Extreme account. 

If your shell prompt still shows “-bash-4.1$”, use the command “logout” to return to your own Linux installation. Then, from your home directory:
```
ssh-keygen -t rsa
```

Just press enter for all prompts following this command until the RSA key is generated. Then make a directory on your Extreme account for SSH login keys:
```
ssh <YOURNETID>@login-1.extreme.uic.edu mkdir -p .ssh
```

And transfer your computer’s newly generated public RSA key to your Extreme account:
```
cat .ssh/id_rsa.pub | ssh <YOURNETID>@login-1.extreme.uic.edu ‘cat >> .ssh/authorized_keys’
```

_Note: cat is a concatenation command that appends text together._

You will now be able to login to Extreme with “ssh netid@login-1.extreme.uic.edu” without entering a password. 

### Obtaining WEVOTE_PACKAGE using SSH
In order to obtain the WEVOTE_PACKAGE directory needed to run WEVOTE, you will need to add Kai or Ahmed's RSA key to your authorized users. To authorize access to your Extreme account, you will need copies of the RSA keys from all users you wish to authorize. Login to your Extreme account then navigate to the .ssh folder and open the authorized_keys file which contains all authorized RSA user keys:
```
cd .ssh
vim authorized_keys
```

Copy each additional key onto a _new line_ and save the file. Now all users you have added should also have SSH access to your Extreme account without a password prompt. Once this is setup, you can copy files from your local virtual machine to your Extreme account using (adding the -r option before file paths will copy whole directories):
```
scp <PATH TO LOCAL FILE> <YOURNETID>@login-1.extreme.uic.edu:<PATH TO DESTINATION>
```

You can also copy files from your Extreme account to your local virtual machine. From your local machine shell (the -r option also applies here when you need to copy directories):
```
scp <YOURNETID>@login-1.extreme.uic.edu:<PATH TO FILE> <PATH TO DESTINATION>
```
Now you are ready to move on to the next part of the tutorial and [install WEVOTE](https://github.com/pophipi/WEVOTE/blob/master/TUTORIAL.md). 
