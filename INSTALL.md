# WEVOTE Bootcamp
This tutorial is intended for beginner Linux and WEVOTE users, and will detail the steps required to install and run WEVOTE on the UIC Extreme cluster using a precompiled WEVOTE_PACKAGE. 

## Installing Linux Virtual Machine:
Since WEVOTE currently only supports Linux, a copy of Linux must be installed on your computer to properly interface with and install WEVOTE. If your computer already has a Linux virtual machine installed or is running Linux, skip this section.

### Installing VM VirtualBox
VirtualBox allows a copy of Linux to run on your computer without having to fully install the operating system. Download and install the [latest version]( https://www.virtualbox.org/wiki/Downloads) for your computer.


Once installed, VirtualBox requires a copy of Linux to run. You may choose any up-to-date version of Linux for VirtualBox. Links for download and installation of Bio-Linux, a version specialized for bioinformatics analysis is below:

> http://environmentalomics.org/bio-linux-download/
> http://environmentalomics.org/bio-linux-installation/

### Virtual Machine Configuration
After installation, boot up the virtual machine, login and setup your user profile per your preferences.
_ _Note: Windows users may need to enable Virtualization Technology in their BIOS before they can boot the virtual machine. VirtualBox will prompt you if you have not enabled it._ _

Once the machine is booted successfully and you are logged into the account you wish to use on the virtual machine, be sure to open a terminal and check that your default shell is bash:
‘’’
echo $SHELL
‘’’

If your shell is bash, the previous command will return “/bin/bash”.

Set the default shell as bash if it isn’t already:
‘’’
chsh
ENTER PASSWORD
/bin/bash
‘’’

You may need to logout for the change to take effect. Your shell prompt (text appearing before your typed commands) should have a “$” at the end of it. 


## Basic Shell Commands
All interaction with the computing cluster will be through the Linux shell terminal. It is recommended that you read through some basic tutorials for navigating the Linux shell and writing shell scripts: http://linuxcommand.org/lc3_learning_the_shell.php. You don’t need to be an expert, but should know some of the commands and basic syntax. Below is a list of important commands for navigating the shell and some basic syntax.
‘’’
cd – changes directories
	cd with no commands returns to the home directory
	cd /PATH/ changes directory to designated path
	Additional syntax: ~ is the home directory, . is the current directory, .. is the previous directory
ls – lists files in the current directory
less FILENAME – view file
vim FILENAME – edit file
cp FILENAME1 FILENAME2 – copy file1 to file2
	additional syntax: cp -r directoryname destination
mv FILENAME DESTINATION – move file
rm FILENAME – delete file
mkdir DIRECTORYNAME – creates a directory in the current working directory
‘’’

_ _Note: If you ever encounter an unfamiliar command, you can usually add -h or –help at the end of the command to see help text for it._ _

## SSH Login
To login to your UIC Extreme account you can use the following command:
‘’’
ssh netid@login-1.extreme.uic.edu
‘’’

After successfully entering your password, your shell prompt should change to “-bash-4.1$”. This prompt indicates that you are interfacing directly with your account on the Extreme cluster and not with your own Linux installation.

This login method is straightforward, but will prompt you to enter your password every time and prevent others from transferring important files like the WEVOTE_PACKAGE folder to you. To allow faster login and allow authorized users to transfer files to you, SSH needs to be configured with RSA keys. This means generating a RSA key for each machine that will be authorized access to your Extreme account. 

If your shell prompt still shows “-bash-4.1$”, use the command “logout” to return to your own Linux installation. Then, from your home directory:

'''
ssh-keygen -t rsa
‘‘‘

Just press enter for all prompts following this command until the RSA key is generated. Then make a directory on your Extreme account for SSH login keys:
‘’’
ssh netid@login-1.extreme.uic.edu mkdir -p .ssh
‘’’

And transfer your computer’s newly generated public RSA key to your Extreme account:
‘’’
cat .ssh/id_rsa.pub | ssh netid@login-1.extreme.uic.edu ‘cat >> .ssh/authorized_keys’
‘’’

You will now be able to login to Extreme with “ssh netid@login-1.extreme.uic.edu” without entering a password. To authorize access to your Extreme account, you will need copies of the RSA keys from all users you wish to authorize. Login to your Extreme account then navigate to the .ssh folder and open the authorized_keys file which contains all authorized RSA user keys:
‘’’
cd .ssh
vim authorized_keys
‘’’

Copy each additional key onto a new line and save the file. Now all users you have added should also have ssh access to your Extreme account without a password prompt. Once this is setup, you can copy files from your local virtual machine to your Extreme account using (adding the -r option before file paths will copy whole directories):
‘’’
scp [PATH TO LOCAL FILE] NETID@login-1.extreme.uic.edu:[PATH TO DESTINATION]
‘’’

You can also copy files from your Extreme account to your local virtual machine. From your local machine shell (the -r option also applies here when you need to copy directories):
‘’’
scp NETID@login-1.extreme.uic.edu:[PATH TO FILE] [PATH TO DESTINATION]
‘’’

## WEVOTE Installation
Although WEVOTE normally requires that each tool be downloaded and compiled, and the databases for these tools be built, this process has already been completed in the WEVOTE_PACKAGE folder transferred to you after your Extreme account setup was completed. Thus, your WEVOTE installation mainly consists of configuration steps to ensure all file paths are correct.

### Environment Variables
In order to execute commands without having to type the path to the executable, Linux keeps track of an environment variable called PATH. In order to run WEVOTE scripts from any folder within your Extreme account you need to add WEVOTE’s main folder to your PATH. Within your Extreme account:
‘’’
export PATH=$PATH:/Path_to_WEVOTE_PACKAGE/WEVOTE
‘’’

### WEVOTE Configuration
Navigate to the WEVOTE directory in WEVOTE_PACKAGE and open wevote.cfg for editing:
‘’’
cd [PATH TO WEVOTE]
vim wevote.cfg
‘’’

Add the paths for WEVOTE’s tools (note that ~/ notation does work correctly when executed as a part of a script in the cluster):
‘’’
blastnPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/blast"
blastDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/blastDB/nt"
krakenPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/kraken"
krakenDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/krakenDB"
clarkPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/clark"
clarkDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/clarkDB"
metaphlanPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/metaphlan"
tippPath=""
‘’’

Copy wevote.cfg to location of your FASTA input files.
‘’’
cp wevote.cfg [PATH_TO_FASTA_FILES]
‘’’

## Running WEVOTE
In order to take advantage of the Extreme cluster’s resources, WEVOTE should not be run directly from the shell. A shell script containing all relevant commands and info needs to be written and submitted to the Extreme cluster as a job for automatic approval and resource distribution. It is recommended that you familiarize yourself with the basics of [shell scripting](http://linuxcommand.org/lc3_writing_shell_scripts.php) and [specific commands](http://rc.uic.edu/resources/technical-documentation/) for the cluster. 

### Prepare the Shell Script

To start making a WEVOTE script, create a new file using vim in a directory where you would like to store your shell scripts. No file extension is required. 
‘’’
vim WEVOTERUN
‘’’

Similar to when we set our virtual machine’s terminal to use bash as its shell, the first line of every shell script designates the shell that will be used to carry out commands.
‘’’
#!/bin/bash
‘‘‘

Next, we need to define options for the Extreme cluster management system using #PBS commands. There are additional PBS commands listed in the technical documentation for Extreme, but the important ones for successfully running WEVOTE are listed below:

‘’’
#PBS -l nodes=16 
#PBS -l partition=ALL
#PBS -l walltime=24:00:00
#PBS -M netid@uic.edu
#PBS -m be
#PBS -N WEVOTERUN
#PBS -V
#PBS -o ~/Scripts/WEVOTERUN.out
‘’’
>
PBS -l commands describe the resources required for the job, with nodes being the number of computing nodes requested, partition meaning what part of Extreme to run on (leave this setting alone), and walltime indicating how much time the program is allotted to run before it is terminated in ds:hrs:mins:secs. 

PBS -M sets an email to receive notifications about the status of the job.

PBS -m be tells Extreme to notify the user when the program begins and ends. You can change the setting to “abe” if you would also like a notification when the program aborts. 

PBS -N names the job, making it easier to parse errors and outputs later.

PBS -V passes all environment variables from your Extreme account to the job. This is vital if your script relies on your environment variables but is optional if you wrote out the full path for everything in your script.

PBS -o sets a file to be used for the stdout of the script, i.e. any notifications or prompts from the shell as it executes commands.
<

### Setting Up WEVOTE in the Shell Script
Now that your shell script settings are ready, you can begin writing the actual script for running WEVOTE. These commands will essentially run in the same environment as commands you type into your Extreme bash shell. All syntax is the same.

Assuming you’ve added the path to WEVOTE in your PATH environment variable, you will only need one major command:
‘’’
run_WEVOTE_PIPELINE.sh -i <input-file> -o <output-foldername> --db <path to WEVOTE_DB> <options>
‘’’

Options:
‘’’
-h|--help                     help flag
-i|--input <input-file>       input query
-o|--output <output-prefix>   Output prefix
--db <taxonomy_db>            taxonomy database path
--threads <num-threads>       number of threads 
-s <score>                    Score threshold
-a <num-of-agreed>            Minimum number of tools agreed tools on WEVOTE decision	
-k <penalty>                  Score penalty for disagreement
--kraken                      Run Kraken
--blastn                      Run BLASTN
--tipp                        Run TIPP
--clark                       Run CLARK
--metaphlan                   Run MetaPhlAn
-c|--classfy                  Start the pipeline from the classification step. i.e., skip running individual tools
‘’’

Example (Note that threads should be adjusted based on the number of nodes you request. Each node usually can run 16 threads):
‘’’
run_WEVOTE_PIPELINE.sh -i MiSeq_accuracy.fa -o ~/Documents/WEVOTEOUT/wevote_output --db ~/Documents/WEVOTE_PACKAGE/WEVOTE_DB --clark --metaphlan --blastn --kraken --threads 256 -a 2
‘’’

After this command executes, you can add one more line to let the script indicate that it finished running successfully. This message will be saved to stdout.
‘’’
echo ‘Done’
‘’’

Save the file and exit vim.

### Running WEVOTE on the Cluster
To run your new script on the cluster:
‘’’
qsub WEVOTERUN
‘’’

Successful job submission will return a job ID number. You can track your job using two main commands. 

checkjob shows details of a particular job.
‘’’
checkjob [job ID]
‘’’

showq gives an overview of all jobs running and in the queue.
‘’’
showq
‘’’
<br/>
### Troubleshooting
After your job is complete, it will generate files that can help with troubleshooting. These files are labeled with the suffix .o[JOBID] or .e[JOBID] and stored in the folder designated by PBS -o.

Check these files for clues to script errors if the output of WEVOTE is not generated or not what was expected.



