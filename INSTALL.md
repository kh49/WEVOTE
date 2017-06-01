# WEVOTE Bootcamp
This tutorial is intended for beginner Linux and WEVOTE users, and will detail the steps required to install and run WEVOTE on the UIC Extreme cluster using a precompiled WEVOTE_PACKAGE. 

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

_Note: Windows users may need to enable Virtualization Technology in their BIOS before they can boot the virtual machine. VirtualBox will prompt you if you have not enabled it._

### Running the Virtual Machine
From VirtualBox, double click your new virtual machine to boot it. The first time the machine runs, the display may be very small. You can adjust it by selecting a larger size from the "Virtual Screen" settings in the "View" menu. Once the machine is booted, you may be prompted to upgrade Ubuntu. Click "Do Not Upgrade".

#### User Accounts
The default account on Bio-Linux is the manager account with the password "manager" (this may differ if you downloaded a different Linux OS). You can create your own account by opening System Settings (gear icon on app bar) and going to User Accounts. Unlock the User Accounts dialog page by clicking Unlock in the upper right hand corner and enter the password "manager" when prompted. 
![user account creation](https://raw.githubusercontent.com/pophipi/WEVOTE/master/images/useraccountcreation.png)

Then click the "+" sign in the lower left to add an account. If you chose to add your own account instead of using the manager account, be sure to set your account as an _Administrator_ instead of _Standard_ account type in the prompt that appears. Set a password for your account by clicking next to the password field after account creation. You can toggle Automatic Login so the next time the machine boots, you will login to your own account.

### Checking Your Shell Version
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
	
less FILENAME – view file

vim FILENAME – edit file
	Additional syntax: vim is entirely command line based. 
	To input text, you need to type "i" to enter insert mode.
	To exit input mode, press Esc.
	To save and quit, type :wq. Make sure you are not in insert mode.
	To quit without saving, type :q!.

cp FILENAME1 FILENAME2 – copy file1 to file2
	Additional syntax: cp -r directoryname destination will copy the whole directory, including all contents. This command also works with mv and rm. 
	
mv FILENAME DESTINATION – move file

rm FILENAME – delete file
	Note that files deleted this way are not easily recoverable.  

mkdir DIRECTORYNAME – creates a directory in the current working directory 
```

_Note: If you ever encounter an unfamiliar command, you can usually add -h or –help at the end of the command to see help text for it._ 

## SSH Login
To login to your UIC Extreme account you can use the following command:
```
ssh netid@login-1.extreme.uic.edu
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
ssh netid@login-1.extreme.uic.edu mkdir -p .ssh
```

And transfer your computer’s newly generated public RSA key to your Extreme account:
```
cat .ssh/id_rsa.pub | ssh netid@login-1.extreme.uic.edu ‘cat >> .ssh/authorized_keys’
```

_Note: cat is a concatenation command that appends text together._

You will now be able to login to Extreme with “ssh netid@login-1.extreme.uic.edu” without entering a password. To authorize access to your Extreme account, you will need copies of the RSA keys from all users you wish to authorize. Login to your Extreme account then navigate to the .ssh folder and open the authorized_keys file which contains all authorized RSA user keys:
```
cd .ssh
vim authorized_keys
```

Copy each additional key onto a _new line_ and save the file. Now all users you have added should also have SSH access to your Extreme account without a password prompt. Once this is setup, you can copy files from your local virtual machine to your Extreme account using (adding the -r option before file paths will copy whole directories):
```
scp [PATH TO LOCAL FILE] NETID@login-1.extreme.uic.edu:[PATH TO DESTINATION]
```

You can also copy files from your Extreme account to your local virtual machine. From your local machine shell (the -r option also applies here when you need to copy directories):
```
scp NETID@login-1.extreme.uic.edu:[PATH TO FILE] [PATH TO DESTINATION]
```

## WEVOTE Installation
Although WEVOTE normally requires that each tool be downloaded and compiled, and the databases for these tools be built, this process has already been completed in the WEVOTE_PACKAGE folder transferred to you after your Extreme account setup was completed. Thus, your WEVOTE installation mainly consists of configuration steps to ensure all file paths are correct.

### Environment Variables
In order to execute commands without having to type the path to the executable, Linux keeps track of an environment variable called PATH. In order to run WEVOTE scripts from any folder within your Extreme account you need to add WEVOTE’s main folder to your PATH. Within your Extreme account:
```
export PATH=$PATH:/Path_to_WEVOTE_PACKAGE/WEVOTE
```

### WEVOTE Configuration
Navigate to the WEVOTE directory in WEVOTE_PACKAGE and open wevote.cfg for editing:
```
cd [PATH TO WEVOTE]
vim wevote.cfg
```

Add the paths for WEVOTE’s tools (note that ~/ notation does work correctly when executed as a part of a script in the cluster):
```
blastnPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/blast"
blastDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/blastDB/nt"
krakenPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/kraken"
krakenDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/krakenDB"
clarkPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/clark"
clarkDB="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/clarkDB"
metaphlanPath="/export/home/[YOUR NETID]/PATH TO WEVOTE_PACKAGE/metaphlan"
tippPath=""
```

Copy wevote.cfg to location of your FASTA input files.
```
cp wevote.cfg [PATH_TO_FASTA_FILES]
```

_Note: FASTA files should always be in the following format:_
```
>read name/info
read
>read name/info
read
...
```

## Running WEVOTE
In order to take advantage of the Extreme cluster’s resources, WEVOTE should not be run directly from the shell. A shell script containing all relevant commands and info needs to be written and submitted to the Extreme cluster as a job for automatic approval and resource distribution. It is recommended that you familiarize yourself with the basics of [shell scripting](http://linuxcommand.org/lc3_writing_shell_scripts.php) and [specific commands](http://rc.uic.edu/resources/technical-documentation/) for the cluster. 

### Prepare the Shell Script

To start making a WEVOTE script, create a new file using vim in a directory where you would like to store your shell scripts. No file extension is required. 
```
vim WEVOTERUN
```

Similar to when we set our virtual machine’s terminal to use bash as its shell, the first line of every shell script designates the shell that will be used to carry out commands.
```
#!/bin/bash
```

Next, we need to define options for the Extreme cluster management system using #PBS commands. There are additional PBS commands listed in the technical documentation for Extreme, but the important ones for successfully running WEVOTE are listed below:
```
#PBS -l nodes=16 
#PBS -l partition=ALL
#PBS -l walltime=24:00:00
#PBS -M netid@uic.edu
#PBS -m be
#PBS -N WEVOTERUN
#PBS -V
#PBS -o ~/Scripts/WEVOTERUN.out
```

PBS -l commands describe the resources required for the job, with nodes being the number of computing nodes requested, partition meaning what part of Extreme to run on (leave this setting alone), and walltime indicating how much time the program is allotted to run before it is terminated in ds:hrs:mins:secs. 

PBS -M sets an email to receive notifications about the status of the job.

PBS -m be tells Extreme to notify the user when the program begins and ends. You can change the setting to “abe” if you would also like a notification when the program aborts. 

PBS -N names the job, making it easier to parse errors and outputs later.

PBS -V passes all environment variables from your Extreme account to the job. This is vital if your script relies on your environment variables but is optional if you wrote out the full path for everything in your script.

PBS -o sets a file to be used for the stdout of the script, i.e. any notifications or prompts from the shell as it executes commands.


### Setting Up WEVOTE in the Shell Script
Now that your shell script settings are ready, you can begin writing the actual script for running WEVOTE. These commands will essentially run in the same environment as commands you type into your Extreme bash shell. All syntax is the same.

Assuming you’ve added the path to WEVOTE in your PATH environment variable, you will only need one major command:
```
run_WEVOTE_PIPELINE.sh -i <input-file> -o <output-foldername> --db <path to WEVOTE_DB> <options>
```

Options:
```
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
```

Example (Note that threads should be adjusted based on the number of nodes you request. Each node usually can run 16 threads):
```
run_WEVOTE_PIPELINE.sh -i MiSeq_accuracy.fa -o ~/Documents/WEVOTEOUT/wevote_output --db ~/Documents/WEVOTE_PACKAGE/WEVOTE_DB --clark --metaphlan --blastn --kraken --threads 256 -a 2
```

After this command executes, you can add one more line to let the script indicate that it finished running successfully. This message will be saved to stdout.
```
echo ‘Done’
```

Save the file and exit vim.

### Running WEVOTE on the Cluster
To run your new script on the cluster:
```
qsub WEVOTERUN
```

Successful job submission will return a job ID number. You can track your job using two main commands. 

checkjob shows details of a particular job.
```
checkjob [job ID]
```

showq gives an overview of all jobs running and in the queue.
```
showq
```

### WEVOTE classification Output Format:
Each sequence classified by WEVOTE results in a single line of output. Output lines have tab-delimited fields; from left to right, they are:

1. The sequence ID, obtained from the FASTA header. 
2. The total number of tools used in WEVOTE (N). 
2. The number of tools that were able to classify the sequence (C).  
3. The number of tools that agreed on WEVOTE decision (A).  
4. Classification score (S).  
5. Taxonomy ID used to classify the sequence by tool #1. This is 0 if the sequence is unclassified by tool #1. 
6. Taxonomy ID used to classify the sequence by tool #2. This is 0 if the sequence is unclassified by tool #2. 
7. Taxonomy ID used to classify the sequence by tool #3. This is 0 if the sequence is unclassified by tool #3. 
8. Taxonomy ID used to classify the sequence by tool #4. This is 0 if the sequence is unclassified by tool #4. 
9. Taxonomy ID used to classify the sequence by tool #5. This is 0 if the sequence is unclassified by tool #5. 
10. The last field is the taxonomy ID assigned to the sequence by WEVOTE. This is 0 if the sequence is unclassified by WEVOTE. 


## How to generate the abundance profile from WEVOTE output:
WEVOTE supports calculating the abundance for the reads or contigs profiling. To execute the the Abundance script on WEVOTE output, use:
```
run_ABUNDANCE.sh -i <input-file> -p <output-prefix> --db <path-to-taxonomy-DB> <options>
```

### Abundance profiling implemented options: 
```
-h|--help                  	 help flag
-i|--input <input-file>    	 input query
-p|--prefix <output-prefix>  Output prefix
--db <taxonomy_db>         	 taxonomy database path
--threads <num-threads>    	 Number of threads
--seqcount <contig-reads-count-file>		 File that contains how many reads are used to assemble each contig
```

### Abundance example:
```
run_ABUNDANCE.sh -i test_wevote_output_WEVOTE_Details.txt -p test_wevote_abundance --db WEVOTE_DB
```

### Abundance example assuming the sequences are from contigs:
In this case, each sequence in the FASTA file that WEVOTE analyzed is a contig. To calculate the actual number of reads mapped to every taxon, we need to have a mapping file that has the information of how many reads are used to assemble each contig. This file should be a tab-delimited file with two fields. The first field has the contig-ID and the second field has the number of reads that used to assemble the corresponding contig. Here is an example of how to call the script for contigs profiling:
```
run_ABUNDANCE.sh -i test_wevote_output_WEVOTE_Details.txt -p test_wevote_abundance --db WEVOTE_DB --seqcount contig_reads.txt
```

### Abundance profiling output format:
The abundance ouput file is a comma-seprated file that has 10 fields; from left to right, they are:


1. Taxon: taxonomy ID. 
2. Count: number of reads classified to the taxon in the first field. 
3. Superkingdom: the name of the superkingdom corresponding to the taxonomy id of the first field. This field is left empty if no defined superkingdom for this taxon. 
4. Kingdom: the name of the kingdom corresponding to the taxonomy id of the first field. This field is left empty if no defined kingdom for this taxon. 
5. Phylum: the name of the phylum corresponding to the taxonomy id of the first field. This field is left empty if no defined phylum for this taxon. 
6. Class: the name of the class corresponding to the taxonomy id of the first field. This field is left empty if no defined class for this taxon. 
7. Order: the name of the order corresponding to the taxonomy id of the first field. This field is left empty if no defined order for this taxon. 
8. Family: the name of the family corresponding to the taxonomy id of the first field. This field is left empty if no defined family for this taxon. 
9. Genus: the name of the genus corresponding to the taxonomy id of the first field. This field is left empty if no defined genus for this taxon. 
10. Species: the name of the species corresponding to the taxonomy id of the first field. This field is left empty if no defined species for this taxon.

### Troubleshooting
After your job is complete, it will generate files that can help with troubleshooting. These files are labeled with the suffix .o[JOBID] or .e[JOBID] and stored in the folder designated by PBS -o.

Check these files for clues to script errors if the output of WEVOTE is not generated or not what was expected.



