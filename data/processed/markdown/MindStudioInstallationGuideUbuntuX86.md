## Ascend 310

## Mind Studio Installation Guide (Ubuntu, x86)

## Trademarks and Permissions

**NOTICE:**

The purchased products, services and features are stipulated by the contract made between Huawei and the customer. All or part of the products, services and features described in this document may not be within the purchase scope or the usage scope. Unless otherwise specified in the contract, all statements, information, and recommendations in this document are provided "AS IS" without warranties, guarantees or representations of any kind, either express or implied.

The information in this document is subject to change without notice. Every effort has been made in the preparation of this document to ensure accuracy of the contents, but all statements, information, and recommendations in this document do not constitute a warranty of any kind, express or implied.

## Contents

1 Introduction.............................................................................................................................. 1

The Installation?........................................................................................................................................................................... 43

| 2 Privacy Statement...................................................................................................................                                                                                                                                                     | 3   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| 2.1 About Third-Party Content...................................................................................................................................................3                                                                                                          |     |
| 2.2 About Third-Party Tools........................................................................................................................................................                                                                                                        | 3   |
| 2.3 About Personal Data..............................................................................................................................................................                                                                                                      | 4   |
| 3 Obtaining Software Packages..............................................................................................                                                                                                                                                                | 6   |
| 4 Environment Preparation......................................................................................................                                                                                                                                                            | 7   |
| 5 Mind Studio Installation......................................................................................................12                                                                                                                                                         |     |
| 5.1 Preparing for Installation...................................................................................................................................................                                                                                                          | 12  |
| 5.2 Installation..............................................................................................................................................................................                                                                                             | 15  |
| 5.3 Installation Verification.......................................................................................................................................................20                                                                                                     |     |
| 5.4 Common Operations...........................................................................................................................................................                                                                                                           | 21  |
| 5.4.1 Starting Mind Studio........................................................................................................................................................                                                                                                         | 21  |
| 5.4.2 Stopping Mind Studio......................................................................................................................................................                                                                                                           | 21  |
| 5.4.3 Uninstalling Mind Studio................................................................................................................................................                                                                                                             | 22  |
| 5.4.4 Querying the Mind Studio Version..............................................................................................................................                                                                                                                       | 23  |
| 5.4.5 Configuring OpenPGP Public Keys..............................................................................................................................                                                                                                                        | 24  |
| 5.4.6 Changing the User Password.........................................................................................................................................27                                                                                                                |     |
| 5.4.7 Changing IP Addresses....................................................................................................................................................                                                                                                            | 30  |
| 6 Upgrade...................................................................................................................................                                                                                                                                               | 31  |
| 6.1 Preparing for Upgrade........................................................................................................................................................                                                                                                          | 32  |
| 6.2 Performing Upgrade............................................................................................................................................................                                                                                                         | 33  |
| 6.3 Exception Handling..............................................................................................................................................................                                                                                                       | 38  |
| 6.3.1 What Do I Do If the Message "get board_id failed" Is Displayed During the Upgrade?.........................                                                                                                                                                                          | 39  |
| 6.3.2 What Do I Do If the Developer Board Failed to Be Upgraded Due to Timeout?........................................40                                                                                                                                                                  |     |
| 7 FAQs..........................................................................................................................................41                                                                                                                                         |     |
| 7.1 What Do I Do If the "apt-get update" Execution Fails During Mind Studio Installation?..........................                                                                                                                                                                        | 41  |
| 7.2 What Do I Do If python-skimage or python3-skimage Is Not Installed During Dependency Installation?.................................................................................................................................................................................... | 42  |
| 7.3 What Do I Do If "Software cycler (for python) decorator (for python) xxx error" Is Displayed During                                                                                                                                                                                    |     |
| 7.4 What Do I Do If a Message Is Displayed Indicating pip2 or pip Unavailability During Mind Studio DDK Installation?.........................................................................................................................................................................          | or 44   |
| 7.5 What Do I Do If Setuptools Is Uninstalled During Mind Studio Installation?.................................................                                                                                                                                                                         | 45      |
| 7.6 What Do I Do If an Error Is Reported During Mind Studio Installation?..........................................................                                                                                                                                                                     | 46      |
| 7.7 What Do I Do If Mind Studio Installation Fails?........................................................................................................                                                                                                                                             | 46      |
| 7.8 What Do I Do If I Cannot Access Mind Studio Using Chrome After Mind Studio Installation?................                                                                                                                                                                                            | 46      |
| 7.9 What Do I Do If Profiling Installation or Startup Fails?..........................................................................................                                                                                                                                                  | 47      |
| 7.10 What Do I Do If the MongoDB Service Fails to Be Stopped During Uninstallation?.................................                                                                                                                                                                                    | 49      |
| 7.11 What Do I Do If a WARNING or FAIL Result Is Returned in the Integrity Verification of a Software Package?......................................................................................................................................................................................... | 50      |
| 7.12 What Do I Do If the Online Help Documents Fail to Be Viewed?.....................................................................52                                                                                                                                                                |         |
| 7.13 How Do I Configure the sshd_config File?................................................................................................................                                                                                                                                           | 53      |
| 8 Appendix..................................................................................................................................55                                                                                                                                                          |         |
| 8.1 Overview of Software Packages......................................................................................................................................                                                                                                                                 | 55      |
| 8.2 Manual Installation..............................................................................................................................................................                                                                                                                   | 56      |
| 8.3 Open Source Third-Party Libraries..................................................................................................................................                                                                                                                                 | 58      |
| 8.4 Change History......................................................................................................................................................................                                                                                                                | 59      |

# 1 Introduction

This document describes how to install Mind Studio, troubleshoot faults during the installation, and upgrade Mind Studio online.

- Mind Studio is a development toolchain platform developed based on the Ascend AI processor, which provides web services such as chip-based operator development, debugging, optimization, and third-party operator development. Mind Studio also supports network migration, optimization, and analysis and a set of visualized AI engine-based drag-and-drop programming services at the service engine layer, greatly lowering the AI engine development threshold.

Mind Studio can be installed on a common PC or workstation and runs on the Ubuntu Linux operating system (OS). Currently, Mind Studio can be accessed only through a browser. If you only need to perform project management, code writing, compilation, model conversion, and running debugging in the simulation environment, you can perform these operations on the computer where Mind Studio is installed. To run a developed project on a real Ascend AI processor, you need to connect Mind Studio to the host and use the host and the tool background service module on the device together to implement functions such as running, log analysis, and profiling of the developed project.

- The device development kit (DDK) provides developers with an algorithm development kit based on the Ascend AI processor, supporting fast and efficient development of AI algorithms. Developers can install the DDK on Mind Studio to quicken algorithm development. The DDK contains the header files and library files, compilation toolchains, debugging and optimization tools, and other required tools for the development of Ascend AI processor algorithms.

Figure 1-1 shows the architecture of Mind Studio and the DDK.

Figure 1-1 Architecture of Mind Studio and the DDK

For details about the concepts such as process orchestration, profiling, and offline model in Figure 1-1, see the Mind Studio description in the Ascend 310 Mind Studio Quick Start.

# 2 Privacy Statement

## 2.1 About Third-Party Content

- This document may contain third-party content, such as third-party information, products, services, software, components, and data. Huawei does not control or assume any responsibility for third-party content, including but not limited to the accuracy, compatibility, reliability, availability, legitimacy, appropriateness, performance, non-infringement, and update status, unless otherwise specified in this document. Any third-party content mentioned or cited in this document does not represent Huawei's recognition or guarantee of the third-party content.
- If a user requires a third-party license, the user must obtain a third-party license through legal means, unless otherwise specified in this document.

## 2.2 About Third-Party Tools

After MindSpore Studio is installed, the gdb and gcc tools are retained on the server.

- Reason for retention: The programming tools are externally provided for users to develop applications. The gdb and gcc tools need to be reserved so that users can compile and debug applications during development.
- Application scenario: Users use the programming tools to develop applications, use gcc to compile source code, and use gdb to debug applications that are compiled. The tools can be used only in the development environment instead of the production environment.
- Risk: Attackers may use the existing debugging and compilation tools to compile new programs, causing secondary attacks on the system. It is recommended that these tools be used only in the development environment.

## 2.3 About Personal Data

## Description of Personal Data

| Usage Scenario                          | The user can enter the password to log in to MindSpore Studio. During the login, the IP address of the host is collected. The datasets imported by the user are used when the model is used for inference. The datasets may contain personal privacy data.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Collected Items                         | IP address, user name, password, and datasets                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Collection Source and Method            | User names and passwords are collected during user registration, IP addresses are collected during login and logout, and datasets are uploaded by users.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Purpose and Security Protection Measure | 1. User names and passwords are used as credentials for user login authentication. IP addresses are used to identify the client for user operations during audit, and datasets are used by users to perform inference on data. In addition, during the usage of MindSpore Studio, the server where MindSpore Studio is installed records the user's latest account login and operation logs. The operation logs include the IP address and user name for security audit. 2. Data is transmitted under HTTPS-based encryption. 3. User names and passwords are encrypted using PBKDF2WithHmacSHA256 and stored on the local MindSpore Studio server. IP addresses are recorded in plain-text logs. Both log files and datasets are under permission control (600). |
| Data Retention Period and Policy        | ● The system forcibly changes the user name and password every three months by default. ● The log data collected by MindSpore Studio is recorded on the host where MindSpore Studio is deployed. Therefore, the log can be manually deleted and cleared by the user. The maximum size of an operation log file is 20 MB. If the maximum size is exceeded, a new operation log file is generated. The maximum number of operation log files is 30. If the maximum number is exceeded, the generated operation log files overwrite the earliest operation log files. ● Due to repeated inference, datasets are retained on the MindSpore Studio server during inference.                                                                                            |
| Clearance Method                                                                                               | ● Users can delete personal data (such as the user names, passwords, IP addresses, and imported datasets) by uninstalling MindSpore Studio or on the GUI. ● Users can delete personal data by using profiling on the GUI. ● Due to repeated inference, datasets are stored in the ~/ HIAI_DATANDMODELSET/workspace_mind_studio directory of the server on the host and need to be cleared by users.   |
| Data Export Method (Interface s or Command s) and Implemen tation Scheme (Anonymi zation or Pseudony mization) | N/A                                                                                                                                                                                                                                                                                                                                                                                                   |
| Data Export Instructio ns                                                                                      | N/A                                                                                                                                                                                                                                                                                                                                                                                                   |

# 3 Obtaining Software Packages

The software packages are described as follows.

Table 3-1 Description of software packages for a non-developer board

| Installation Package                                                                      | Description                                                           | Integrity Verification File                                                                    |
|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| mini_mind_studio_Ubuntu.rar                                                               | Mind Studio installation package                                      | mini_mind_studio_Ubuntu.r ar.asc                                                               |
| MSpore_DDK-{version}- x86_64.ubuntu16.04- aarch64.ubuntu16.04- aarch64.ubuntu16.04.tar.gz | DDK installation package for DK scenarios (such as the Atlas 200 DK): | MSpore_DDK-{version}- x86_64.ubuntu16.04- aarch64.ubuntu16.04- aarch64.ubuntu16.04.tar.gz. asc |
| MSpore_DDK-{version}- x86_64.ubuntu16.04- x86_64.ubuntu16.04- aarch64.miniOS.tar.gz       | DDK installation package for non-DK scenarios (such as Atlas 300):    | MSpore_DDK-{version}- x86_64.ubuntu16.04- x86_64.ubuntu16.04- aarch64.miniOS.tar.gz.asc        |

- {version} is the DDK version number.
- The versions of the Mind Studio installation package and DDK installation package must be consistent.

# 4 Environment Preparation

To ensure that Mind Studio is used properly, environment requirements described in this section must be met.

## Environment Requirements

Mind Studio installation must meet the following hardware and operating system (OS) requirements.

Table 4-1 Ubuntu system version information

| Catego ry   | Version Restricti on                                     | Obtaining Method   | Precautions                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------|----------------------------------------------------------|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardw are   | Suggesti on for the single- user scenario: ≥ 4 GB memory | -                  | ● If the memory size of the Linux host is 4 GB, the size of the model file should be less than or equal to 350 MB for model conversion on Mind Studio. Otherwise, the OS may be unstable. ● If the configurations of the Linux host are upgraded, for example, the memory is expanded to 8 GB, the supported operation specifications should be increased proportionally. If the memory is expanded from 4 GB to 8 GB, the recommended size threshold of the model file does not exceed 700 MB. |
| OS             | 16.04.3                        | Download the required version from http://old- releases.ubuntu.com/ releases/16.04.3/. You can download the desktop version ubuntu-XXX- desktop-amd64.iso or server version ubuntu-XXX-server- amd64.iso. NOTE Only the English version is supported. | ● Ensure that the OS supports the UTF-8 character set. Otherwise, you cannot view the support documents by using online help of Mind Studio after the installation. For details about the troubleshooting solution, see 7.12 What Do I Do If the Online Help Documents Fail to Be Viewed?. ● During model conversion, if a custom TE plug-in is loaded, the GUI may be stuck. In this case, run the uname -r or cat /proc/version command to check whether the Linux kernel version is earlier than 4.18. If so, install the Linux kernel upgrade patch by visiting: https://bugzilla.kernel.org/ attachment.cgi?id=277305 For details about how to upgrade the Linux kernel, visit https:// bbs.huaweicloud.com/forum/ thread-22441-1-1.html. |
| Python         | Python 2: 2.7 Python 3: 3.5+   | For details, see Installing Dependencies.                                                                                                                                                                                                             | Install Python 2.7 or later for Python2 and Python 3.5 or later for Python3. Both Python versions need to be installed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| JDK            | 1.8.0_17 1+                    | For details, see Installing Dependencies.                                                                                                                                                                                                             | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Google Chrom e | 64-bit, 67.0.339 6.87 or later | -                                                                                                                                                                                                                                                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

## Preparing the Mind Studio Installation User

Before the installation, you need to create a user account for Mind Studio installation. Perform all the operations in this section as the root user.

User description:

- Mind Studio can be installed and used only in a single-user scenario.
- If you use an existing non-root user to install the DDK, run the following command as the root user to assign the existing user permission 750 on the $HOME directory:

chmod 750 /home/username

- If you want to install the DDK as a new non-root user, perform the following steps as the root user to create one:
- a. Run the following command to create a Mind Studio installation user and set the $HOME directory for the user: useradd -d /home/username -m username
- b. Run the following command to set the password: passwd username
- c. Run the following command to access the /home directory: chmod 750 /home/username

Username indicates the name of the Mind Studio installation user. The umask value of the user cannot be greater than 0027.

- You can view the umask value by running the umask command.
- You can change the umask value by running the umask New value command.

## Configuring Permissions for the Mind Studio Installation User

Before Mind Studio installation, you need to download dependent software with the sudo apt-get permission. Perform the following operations as the root user:

1. Open the /etc/sudoers file. chmod u+w /etc/sudoers vi /etc/sudoers
2. Add the following content under # User privilege specification of the file: username ALL=(ALL:ALL) NOPASSWD:SETENV:/usr/bin/apt-get

Replace username with the name of the common user who executes the installation script.

Ensure that the last line of the /etc/sudoers file is #includedir /etc/sudoers.d. Otherwise, add it manually.

3. Run the :wq! command to save the file.
4. Run the following command to revoke the write permission on the /etc/ sudoers file:

chmod u-w /etc/sudoers

## Checking the Source Validity

The Mind Studio installation requires the download of related dependencies. Ensure that the server where Mind Studio is installed can connect to the network.

Run the following command as the root user to check whether the source is valid:

apt-get update

If an error is reported during the command execution, check whether the network connection is normal or replace the source in the /etc/apt/sources.list file with a valid one.

## Installing Dependencies

Run the su - username command to switch to the Mind Studio installation user and perform the following operations to install the GCC and JDK tools on which Mind Studio depends:

Step 1 Run the following command to install the Mind Studio dependencies: (Run the following commands separately. If a single command line is wrapped, copy the command to Word or Notepad, replace the line wrap point with a space to merge the lines into a single line, and copy it back to the server for execution.)

sudo apt-get install gcc g++ cmake curl libboost-all-dev libatlas-base-dev unzip haveged liblmdb-dev

sudo apt-get install python-skimage python3-skimage python-pip python3pip

sudo apt-get install libhdf5-serial-dev libsnappy-dev libleveldb-dev swig python-enum python-future

sudo apt-get install make graphviz autoconf libxml2-dev libxml2 libzip-dev libssl-dev sqlite3 python

If the system displays a message indicating that python-skimage or python3-skimage is not installed, see 7.2 What Do I Do If python-skimage or python3-skimage Is Not Installed During Dependency Installation? for the troubleshooting.

**Step 2 Install the JDK.**

1. Run the sudo apt-get install -y openjdk-8-jdk command to install the JDK.
1. If the JDK is installed on the local host, run the java -version command to check the version number. If the version number is earlier than 1.8.0_171, uninstall the JDK, download the sources again by referring to Checking the Source Validity, and reinstall the JDK.
2. If the JDK is installed by using the source described in Checking the Source Validity and the commands described in Step 2.1, run the java -version command to check the version number. If the version number is earlier than 1.8.0_171, run the sudo apt-get update command to update the source.
3. If the source version is still earlier than 1.8.0_171 after either of the preceding method is used, download the jce_policy-8.zip file from the Oracle official website and replace the local_policy.jar and US_export_policy.jar files in the package with their counterparts in %JAVA_HOME%\jre\lib\security (%JAVA_HOME% is an environment variable. You can check the address pointed to by %JAVA_HOME% in the .bashrc file).
2. Configure the JAVA_HOME environment variable as follows, on which the installation and running of Mind Studio depends:

If you do not set environment variables according to this step, the following error information is displayed during the Mind Studio installation, and the Mind Studio installation fails.

Please set JAVA_HOME !, Exit 1

- a. Run the vi ~/.bashrc command in any directory to open the .bashrc file.
- b. Add the following content to the end of the file: export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

export PATH=$JAVA_HOME/bin:$PATH

JAVA_HOME indicates the JDK installation directory. If the JDK has been set, change it as required. If the JDK is installed according to the preceding steps, you do not need to change the installation directory.

- c. Run the :wq! command to save the file and exit.
- d. Run the source ~/.bashrc command for the environment variable to take effect.
- e. Run the echo $JAVA_HOME command to check the environment variable settings. The following information is displayed: /usr/lib/jvm/java-8-openjdk-amd64
- f. Run the which jconsole command to check the JDK installation. If the following information is displayed, the installation is successful. Otherwise, the JDK installation fails. /usr/lib/jvm/java-8-openjdk-amd64/bin/jconsole

**----End**

# 5 Mind Studio Installation

The DDK is automatically installed during the Mind Studio installation. This topic describes how to install Mind Studio.

**NOTICE:**

- Currently, only one set of Mind Studio can be installed on a host. You can check whether Mind Studio has been installed by referring to 5.4.4 Querying the Mind Studio Version. If so, uninstall it by referring to 5.4.3 Uninstalling Mind Studio as the Mind Studio installation user and re-install Mind Studio.
- After uninstalling Mind Studio, clear the /tmp and /dev/shm directories before re-installation, to avoid insufficient permissions.

## 5.1 Preparing for Installation

## 5.2 Installation

Mind Studio can be installed either automatically or manually. Automatic installation is implemented by running the installation script in one-click mode. Manual installation requires manual input of configuration parameters and installation commands. You are advised to use automatic installation. After the installation is successful, you need to check whether the current version is correct.

## 5.3 Installation Verification

## 5.4 Common Operations

If the server is restarted, you need to restart Mind Studio as well. You can also run a command to stop the Mind Studio services. Before upgrading the tool to a new version, uninstall the earlier version.

Before installing the tool, upload and decompress the package.

## Setting Permissions on the Installation Package Directory

**Step 1 Create a directory.**

In the $HOME directory of Linux, create a directory (for example, director) for storing the installation package as the Mind Studio installation user by running the following command:

mkdir director

Step 2 Set the permissions on the installation package directory.

The director directory must be readable, writable, and executable to the Mind Studio installation user. If the user does not have the permissions on this directory, run the su root command to switch to the root user and run the following commands:

chown username:usergroup director chmod 750 director

The parameters are described as follows:

- Replace username with the name of the Mind Studio installation user.
- Replace usergroup with the group to the Mind Studio installation user belongs.
- usergroup indicates the first group to which the Mind Studio installation user belongs. The query command is groups, as shown in Figure 5-1.
- If the tool is installed in a different directory, ensure that the directory has permission 750.
- Set permission 750 on the director directory.

Figure 5-1 Querying the first group of the user

----End

## Uploading Installation Packages

Upload the following files to the director directory as the Mind Studio installation user:

- Mind Studio installation package: mini_mind_studio_Ubuntu.rar
- Verification file of the Mind Studio installation package: mini_mind_studio_Ubuntu.rar.asc
- DDK installation package: MSpore_DDK****tar.gz
- Verification file of the DDK installation package: MSpore_DDK****tar.gz.asc
- For details about the DDK package names, see 3 Obtaining Software Packages. * indicates the version name of the DDK installation package.
- The Mind Studio and DDK installation packages must be placed in the same directory.

## Verifying the Software Package Integrity

Before installing a software package, you are advised to check whether the software package is incomplete or damaged due to network or storage device faults.

In the director directory where the installation packages are located, perform the following steps:

- Step 1 Configure the OpenPGP public keys. For details, see 5.4.5 Configuring OpenPGP Public Keys.
- Step 2 Run the following commands as the Mind Studio installation user to check whether the Mind Studio and DDK software packages are valid and integrated, as shown in Figure 5-2.
- Mind Studio: gpg --verify "mini_mind_studio_*.rar.asc"
- DDK: gpg --verify "MSpore_DDK****tar.gz.asc"
- In the command output, D5CFA5D9 indicates the public key ID of Mind Studio and 27A74824 indicates the public key ID of the DDK.
- If the message Good signature is displayed without WARNING or FAIL, the signature is valid and the integrity verification is passed.
- If WARNING or FAIL is displayed, the verification fails. Rectify the fault by referring to the handling suggestions described in 7.11 What Do I Do If a WARNING or FAIL Result Is Returned in the Integrity Verification of a Software Package?.
- Replace mini_mind_studio_*.rar.asc and MSpore_DDK****tar.gz.asc with the actual verification files of the installation packages.
- The integrity verification can be performed only when the software package and the .asc file are stored in the same path.

Figure 5-2 Verifying the software package integrity

**----End**

## Decompressing Installation Packages

Run the following command as the Mind Studio installation user to decompress the mini_mind_studio_Ubuntu.rar installation package:

unzip mini_mind_studio_Ubuntu.rar

For details about the content of the decompressed package, see Table 8-1.

During the installation of Mind Studio, the installation script automatically loads the related contents in the DDK installation package. Therefore, you do not need to decompress the DDK installation package.

Mind Studio can be installed either automatically or manually. Automatic installation is implemented by running the installation script in one-click mode. Manual installation requires manual input of configuration parameters and installation commands. You are advised to use automatic installation. After the installation is successful, you need to check whether the current version is correct.

**Prerequisites**

**Procedure**

This section describes the procedure of automatic installation. For the procedure of manual installation, see 8.2 Manual Installation.

Step 1 Switch to the root user and assign permissions to the Mind Studio installation user.

su root cd /home/username/director ./add_sudo.sh username

Without permission assignment, the following information is displayed when you run the installation script, and the installation is stopped.

Please check if add_sudo.sh exists and execute it with root privileges. Usage: ./add_sudo.sh [user],example:sudo add_sudo.sh [install_user]

Step 2 Before the installation, check parameters in the env.conf file as the Mind Studio installation user, as shown in Table 5-1.

If you do not modify the configuration file, the default configurations are used during installation. If you need to modify installation parameters, modify the parameters according to the Configuration Description column in Table 5-1, and then perform the installation.

If the content is deleted or cleared by mistake when you configure the env.conf file, redecompress the installation package by referring to Decompressing Installation Packages.

The operations required in 4 Environment Preparation and 5.1 Preparing for Installation have been completed.

Table 5-1 Configuration file parameters

| Parameter        | Function Description                                                                                 | Configuration Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ip=<your ip>     | IP address used for Mind Studio startup                                                              | ● If the eth0 NIC is available in the environment, its IP address is automatically obtained during the installation. If multiple NICs exist, check all valid IP addresses and choose one from them. ● If no eth0 NIC is available in the environment and the eth0 IP address is not obtained, run the ifconfig command to check all valid IP addresses and choose one from them. ● If you set ip=any, the NIC IP address is automatically obtained during the Mind Studio startup. The value is left empty by default. |
| port=<your port> | Number of the Mind Studio listening port                                                             | The value is an integer within 1024 to 65535. Ensure that the port is not occupied. You can run the netstat -an&#124; grep Port number command to check whether a port is occupied. The default value is 8888.                                                                                                                                                                                                                                                                                                         |
| toolpath         | Path of required tools during the execution, for example, the database path                          | The default value is ~/tools.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| backup           | Path for storing backup project, my- model, and my- datasets files during Mind Studio uninstallation | The default value is ~/wsbackup.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| profiler_port | Profiling listening port                                               | The value is an integer within 1024 to 65535. The default value is 8099. ● Ensure that the port is not occupied. You can run the netstat -an &#124; grep Port number command to check whether a port is occupied. Some ports cannot be customized using the Chrome browser (for example, ports 6666, 6000, and 6667). You are advised to set this parameter to a value greater than 7000. ● Ensure that these ports are not occupied: port 4412 for exec, 4411 for terminal, 4403 for wsagent_debug, 4401 for wsagent, 3002 for hiai- agent, 3001 for cce_profiler, 3003 for hiai_log, and profiler_port. |
| max_log_size  | Maximum number of log files in the ~/ tools/log/ hiai_agent_log folder | Considering the limit of the disk capacity, you are advised not to set this parameter to a too large value. The default value is 10.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| apache_user   | User name of the background Apache service of profiling                | The default value is msvpUser and cannot be changed. For security purposes, the installation script automatically modifies the configuration file during the installation to disable the SSH access of the user. If the configuration file is abnormal, the modification fails. You are advised to manually modify the configuration file by referring to FAQ > How Do I Configure the sshd_config File?.                                                                                                                                                                                                 |
| install_user  | Current installation user                                              | Mind Studio installation user, who must have the permission to create tool path and backup path. The value is left empty by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| package_pat h | Path of the permission assignment script add_sudo.sh                   | The value is left empty by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| use_eth0    | Whether to use eth0 for startup by default when ip is set to any | If use_eth0 is set to true, eth0 is used for startup by default when ip=any. If use_eth0 is set to false, you can input an IP address of one out of multiple NICs for startup. The default value is true.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| load_data   | Whether to load data from the backup path                        | The value true indicates that data is loaded from the backup path. The value false indicates that data is not loaded.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| run_type    | Mind Studio running environment                                  | The default value is true. ● If this parameter is set to remote, when you click Run on the process orchestration page or click Link on the log page, the host IP address setting dialog box is displayed. In this case, you need to manually input the IP address. This configuration applies to the scenario where the Mind Studio installation and running processes are orchestrated in different environments. ● If this parameter is set to local, when you click Run on the process orchestration page or click Link on the log page, the host IP address setting dialog box is displayed. The default value is 127.0.0.1. This configuration applies to the scenario where the Mind Studio installation and running processes are orchestrated in the same environment. The default value is remote. NOTE Perform the following steps to change the value of this parameter: 1. Stop Mind Studio. For details, see Common Operations > Stopping Mind Studio. 2. Open the file where run_type is located: vi ${toolpath}/script/env.conf 3. Change the value of run_type to local or remote. 4. Restart Mind Studio. For details, see Common Operations > Starting Mind Studio. |

- Step 3 Switch to the Mind Studio installation user and run the ./install.sh or bash install.sh command to execute the installation script.

During the installation of Mind Studio, the installation script automatically loads the related contents in the DDK installation package to complete the DDK installation. The default installation path of the DDK is $HOME/tools/che/ddk.

- Step 4 Configure the IP address used for Mind Studio startup in either of the following scenarios (The following information is displayed only when the user environment has no eth0 NIC or multiple NICs):

[INFO] Your ip address is xxx.xxx.xxx.xxx

1. [INFO] Press ENTER to continue or input a NEW ip address: (If an eth0 NIC exists, its IP address is obtained during the access to the Mind Studio IP address. Press Enter to continue or run the ifconfig command to check all valid IP addresses and choose one from them.)
2. [INFO] Please input your ip address: (If no eth0 NIC exists, its IP address is not obtained during the access to the Mind Studio IP address. Run the ifconfig command to check all valid IP addresses and choose one from them.)

If the eth0 NIC is not available and its IP address is not obtained, the following error message is displayed after you press Enter directly:

[ERROR] Invalid ip address: , please input again:

Input a valid IP address queried by running the ifconfig command and press Enter.

- Step 5 Specify whether to back up the tools installation directory. (This message is displayed only when the user installation directory is not empty.)

[INFO] /home/username/tools is not empty. Files in the directory will be cleared during installation. Are you sure to back up them?[Y/N]: (Input Y or y and press Enter to exit the installation. Back up the installation directory, and then run the /.install.sh script to perform the installation again. Input N or n and press Enter to delete the files in /home/username/tools and continue to install Mind Studio.)

After backing up the installation directory, you need to assign permissions again according to Step 1 before installation.

- Step 6 Follow the installation process to complete the installation.

If Install successfully is displayed, the installation is successful.

Regardless of the installation result, the del_sudo.sh script is automatically executed to revoke the permissions of the Mind Studio installation user. If you want to run the installation script again in the case of an installation failure, perform permission assignment again and re-install Mind Studio.

- If the installation fails, view the log file in ~/tools/log/mind_log and rectify the fault according to the error information.
- If a message is displayed indicating that profiling installation fails, view the log file in ~/ tools/log/profilerlog and rectify the fault according to the error information. You can also obtain the solution to a specific problem by referring to 7.9 What Do I Do If Profiling Installation or Startup Fails?.

----End

## Installation Process

The following actions are executed during the installation of Mind Studio:

1. Installing the MongoDB database
2. Installing the DDK
3. Installing the performance analyzer, HiAI CCE Profiler
4. Starting the Mind Studio service
5. Checking whether backup data (in the backup directory) needs to be imported
6. Installing the Apache service and PHP, and starting the Apache service

Now, Mind Studio is ready for development.

## Verifying the Installation

- Use the Chrome browser to access the following website to check whether the Mind Studio GUI can be accessed. If yes, Mind Studio has been installed successfully. Otherwise, the installation fails. https://IP:Port

If you cannot access Mind Studio using the preceding URL, rectify the fault be referring to 7.8 What Do I Do If I Cannot Access Mind Studio Using Chrome After Mind Studio Installation?.

- Use the Chrome browser to access the following website to check whether the Profiling GUI can be accessed. If yes, the Profiling tool has been installed successfully. Otherwise, the installation fails. https://IP:Profiler_port
- Check whether the installed version is correct. For details, see 5.4.4 Querying the Mind Studio Version.
- After Mind Studio is installed, you are advised to change the default password of the MongoDB user for security considerations. For details, see 5.4.6 Changing the User Password.

- Replace IP with the IP address of the server where Mind Studio is installed. Port of Mind Studio is default to 8888. Profiler_port of Profiling is default to 8099. If the Mind Studio installation IP address and port number, as well as the Profiling port number are mapped, enter the mapped IP address and port numbers. You can modify them in the ~/ tools/scripts/env.conf file.
- ~/tools is the default toolpath setting, which can be customized before Mind Studio installation. You can view the value of toolpath in the scripts/env.conf file.
- The default user name for logging in to Mind Studio is MindStudioAdmin, which cannot be changed. The initial password is Huawei123@. Change the password by referring to User Management in Ascend 310 Mind Studio Basic Operations.
- The user name for logging in to Profiling is msvpadmin, and the initial password is Admin12#$. You can create a common user by referring to Viewing Performance Analysis Results in Ascend 310 Mind Studio Auxiliary Tools.

If the server is restarted, you need to restart Mind Studio as well. You can also run a command to stop the Mind Studio services. Before upgrading the tool to a new version, uninstall the earlier version.

### 5.4.1 Starting Mind Studio

Perform the following operations as the Mind Studio installation user:

To start Mind Studio, run the following command in the ~/tools/bin directory on Linux:

bash start.sh

If no exception occurs after the script is executed, the MongoDB service, HiAI_CCE-Profiler service, and Mind Studio services have been started successfully. Use the Chrome browser to access the following website to check whether the Mind Studio GUI can be accessed. If yes, Mind Studio has been started successfully. Otherwise, the startup fails.

https://IP:Port

Replace IP with the IP address of the server where Mind Studio is installed. Port of Mind Studio is default to 8888. If the Mind Studio installation IP address and port number have been mapped, enter the mapped IP address and port number. You can modify them in the ~/tools/scripts/env.conf file.

### 5.4.2 Stopping Mind Studio

Perform the following operations as the Mind Studio installation user:

To stop Mind Studio, run the following command in the ~/tools/bin directory on Linux:

bash stop.sh

The following services are stopped as well:

- MongoDB service
- HiAI_CCE-Profiler performance analyzer service
- Mind Studio service

After Mind Studio is stopped, the Mind Studio GUI cannot be accessed through Chrome.

### 5.4.3 Uninstalling Mind Studio

During the Mind Studio uninstallation, the DDK is automatically uninstalled. Log in to the Mind Studio server as the installation user and run the ./uninstall.sh command in the ~/tools/bin directory of Linux to uninstall Mind Studio. The operation steps are as follows:

Step 1 Switch to the root user and assign permissions to the Mind Studio installation user in the /usr/bin directory.

su root cd /usr/bin ./add_sudo.sh username

Without permission assignment, the following information is displayed when you run the uninstallation script, and the uninstallation is stopped.

Please check if add_sudo.sh, del_sudo.sh exists and execute the add_sudo.sh script with root privileges

- Step 2 Switch to the Mind Studio installation user and run the ./uninstall.sh uninstallation script in the ~/tools/bin directory.

If the uninstallation fails, perform permission assignment again by referring to Step 1.

Step 3 Specify whether to back up the user data.

[WARNING] Do you need to backup for user data (include projects mydatasets my-model caffe-model mongodb profiling) ? [Y/N]

(To input Y or y and press Enter to back up the user data, go to 3.1. To input N or n and press Enter to skip the backup, go to 3.2.)

1. Specify whether to back up the user data. (This message is displayed only when the /home/username/wsbackup directory is not empty. When the directory is empty, go to Step 4.)

[WARNING] Directory /home/username/wsbackup is not empty, please make your choice! [Y:(Continue to overwrite backup)/N:(Change a directory)]:

Input Y or y and press Enter to overwrite the backup, or input N or n and press Enter to change the backup path.

2. If the user data is not backed up, a message is displayed indicating whether to delete the user data.

[WARNING] Are you sure to remove user data (include projects mydatasets my-model caffe-model mongodb profiling) ? [Y/N]:

Input Y or y and press Enter to delete the user data, or input N or n and press Enter to retain the user data.

- Step 4 Stop the HiAI_CCE-Profiler service.
- Step 5 Uninstall MongoDB.
- Step 6 Stop the web service.
- Step 7 Back up user data to the backup directory, including projects, custom datasets, and custom models.
- Step 8 Check the uninstallation result. If the message "Uninstallation finished" is displayed, the uninstallation is successful.
- ----End

### 5.4.4 Querying the Mind Studio Version

You can query the version by running a command on the server or in the client.

- Query the version by running a command on the server. After Mind Studio is installed, go to the ~/tools/conf directory and run the cat version command to check the Mind Studio version, as shown in Figure 5-3.
- Query the version in the client.

Figure 5-3 Querying the Mind Studio version on the server

Log in to Mind Studio and choose Help &gt; About. The Mind Studio version information is displayed, as shown in Figure 5-4.

Figure 5-4 Querying the Mind Studio version in the client

The time information shown in Figure 5-3 and Figure 5-4 is for reference only.

### 5.4.5 Configuring OpenPGP Public Keys

**Prerequisites**

- The public keys are configured by the installation user of Mind Studio.
- The GnuPG tool is installed on Linux.

Verification method:

- If the GnuPG tool has been installed, run the gpg --version command in the shell. Information in Figure 5-5 is displayed:

Figure 5-5 Command output

- If the GnuPG tool is not installed, install the tool by following the instructions provided in its official website https://www.gnupg.org/.

## Configuring Public Keys

Step 1 Obtain the public key files.

To differentiate the public key used by Mind Studio and that used by the DDK, you are advised to rename the downloaded public key files or import the files to different directories. In this example, the public key files are renamed as follows: KEYS_mind_studio.txt for Mind Studio and KEYS_DDK.txt for the DDK.

- To obtain the Mind Studio public key, perform the following steps:
- Upload the KEYS.zip file in the resource folder extracted from the .zip package to the Linux OS where Mind Studio is installed,

for example, home/username/openpgp/keys directory. Run the following command to decompress the package, and then rename the public key file KEYS_mind_studio.txt:

unzip KEYS.zip

- To obtain the DDK public key, perform the following steps:
1. Go to the OpenPGP download page and click the download link, as shown in Figure 5-6. The file download page is displayed.

Figure 5-6 OpenPGP download page

The KEYS file is the public key file, as shown in Figure 5-7.

Figure 5-7 Selecting the KEYS file

To switch to the language, click in the upper right corner.

2. Rename the downloaded KEYS.txt file KEYS_DDK.txt and upload it to the Linux OS where Mind Studio is installed,

for example, /home/username/openpgp/keys.

**Step 2 Import the public key files.**

Run the following command to go to the directory where a public key file is stored and import the public key: (The following uses the Mind Studio public key as an example. Import the DDK public key the same way.)

gpg --import "/home/username/openpgp/keys/KEYS_mind_studio.txt"

Figure 5-8 Importing a public key file

/home/username/openpgp/keys indicates the absolute path of the public key file KEYS. username must be replaced with the name of the Mind Studio installation user.

**Step 3 Run the following command to view the import result:**

gpg --fingerprint

Figure 5-9 Checking the import result

**Step 4 Verify the public keys.**

- The validity of an OpenPGP public key must be verified based on the public key ID, fingerprint, UID, and the publisher of the public key. The published information of the OpenPGP public keys is as follows:
- Mind Studio public key information:
- i. Public key ID: D5CFA5D9
- ii. Public key fingerprint: 3938 F6DA 31B5 8D47 5D6A FA5C C8CB 3C14 D5CF A5D9

- iii. User ID (UID): Mind_studio &lt;support-mind_studio@huawei.com&gt;
- DDK public key information:
- i. Public key ID: 27A74824
- ii. Public key fingerprint: B100 0AC3 8C41 525A 19BD C087 99AD 81DF 27A7 4824
- iii. UID: OpenPGP signature key for Huawei software (created on 30th Dec,2013) &lt;support@huawei.com&gt;

The public key fingerprint in the public key information is only an example. For details, see the obtained public key file KEYS.txt.

Verify the public key information and perform the following steps to set the trust level for the public keys (The following uses the Mind Studio public key as an example. Set the trust level of the DDK public key the same way.):

- Run the following command to set the trust levels of the public keys:

```
- For Mind Studio: gpg --edit-key "Mind_studio" trust - For the DDK: gpg --edit-key "OpenPGP signature key for Huawei software" trust
```

Information similar to the following is displayed. Enter 5 after Your decision?, which indicates I trust ultimately. Enter y after Do you really want to set this key to ultimate trust? (y/N).

Figure 5-10 Setting the trust level for a public key

Step 5 Run the quit command to exit.

----End

### 5.4.6 Changing the User Password

## Changing the Password of the non-root User

The methods for changing passwords of non-root users are the same. This topic uses the Mind Studio installation user ascend as an example.

- Step 1 Log in to the OS as the Mind Studio installation user.
- Step 2 Run the passwd command to change the password of the current user. For example, change the password of the ascend user. passwd
- Step 3 Input the old password and the new password twice as prompted, and press Enter.
- Step 4 Perform the following operations to check whether the new password takes effect.
1. Exit the OS.
2. Use the new password to log in to the OS. If the login is successful, the new password has taken effect.

----End

## Changing the Password of the root User

- Step 1 Log in to the OS as the Mind Studio installation user.
- Step 2 Run the following command to switch to the root user:

su - root

Enter the password of the root user as prompted.

- Step 3 Run the following command to change the password of the root user: passwd

Enter the new password of the root user twice as prompted. Then, press Enter. The password is changed successfully.

----End

## Changing the Password of the MongoDB User

Step 1 Make sure that a Mind Studio project is started.

Run the java -version command in the Linux CLI to check whether the JDK has been installed and JDK environment variable has been configured.

Figure 5-11 Checking JDK installation and JDK environment variable configurations

If the JDK is not installed or the JDK environment variable is not configured, an error is reported when the MongoDB password is changed, and the modification fails.

Step 2 Log in to the Mind Studio server as the Mind Studio installation user, go to the tools/scripts directory, and run the mongoPwdUpdate.sh script to change the MongoDB password. by running the following command:

sh mongoPwdUpdate.sh

Enter the password according to the system output.

```
Please enter your admin user old password: Please enter your admin user new password: Please enter your admin user new password again: Please enter your minddbuser user new password: Please enter your minddbuser user new password again:
```

- The new password must meet the following requirements: The password must contain at least eight characters, including letters, digits, and special characters. The special characters cannot be @, %, or spaces. Otherwise, the Mind Studio GUI may not be displayed properly.
- If the password is successfully changed, the following message is displayed: update mongodb pwd success!!!!
- If the password is changed for the first time, the default password of the admin user is abJ!19bj. The admin user is the administrator of the MongoDB. After logging in to the database, the admin user can manage common user accounts. The minddbuser user is a common user account of the MongoDB for connection authentication when you add, delete, modify, or query the database.
- ~/tools is the default toolpath setting, which can be customized before Mind Studio installation. You can view the value of toolpath in the scripts/env.conf file. You can run the find / -name 'env.conf' command to view the location of the env.conf file in the script directory.

Step 3 Refresh the Mind Studio project and check whether the functions are normal.

Figure 5-12 Refreshing a Mind Studio project

----End

## Updating the Key for Encrypting Character Strings

- Context

After Mind Studio is installed, a key for encrypting character strings is provided by default. The key is managed in a two-layer structure, including the root key and working key. In scenarios such as changing the password of the MongoDB user and changing the password of the Mind Studio certificate key store, the system uses the working key to encrypt the character strings. You are advised to update the key periodically (for example, every 90 days).

You can log in to the Mind Studio server as the Mind Studio installation user, view the working key work_key.json in ~/mongo/config/keygen, and view the root key in ~/mongo/config/keygen/rootkey.

- Procedure
- Step 1 Log in to the Mind Studio server as the Mind Studio installation user and go to the ~/tools/bin directory.

Step 2 Run the following command to update the key:

./updateMongoKey.sh

After the command is executed, the system stops Mind Studio (including profiling), updates the key, and restarts Mind Studio (including profiling). After the key is updated, a new root key and a new working key are generated in the ~/ mongo/config/keygen directory, and the files in this directory are backed up before the key update.

- The backup file crt.conf_bak of the crt.conf file is generated in ~/tools/ scripts.
- The backup file profiler.cfg_bak of the profiler.cfg file is generated in ~/ tools/conf.
- The backup file redis.cfg_bak of the redis.cfg file is generated in ~/tools/ conf.
- The backup directory config_bak of the config directory is generated in ~/ mongo.
- The backup file workkey.img_bak of the workkey.img file is generated in ~/ tools/vendor.

If an exception occurs during key update, all operations are rolled back, that is, the status before the key update is restored. If the rollback fails, you need to manually restore the *_bak backup files or folders and restart Mind Studio. Run the following command in the ~/tools/bin directory to restart Mind Studio:

bash stop.sh bash start.sh

----End

### 5.4.7 Changing IP Addresses

If you want to change the IP address of the Ubuntu server, the IP address used for Mind Studio installation needs to be changed as well.

- If the IP address in the env.conf file is the IP address of the Ubuntu server, change it to the new IP address of the Ubuntu server.
- If the IP address in the env.conf file is set to any:
- If use_eth0 in the env.conf file is set to true, change the eth0 IP address and restart Mind Studio for the new IP address to take effect.
- If use_eth0 in the env.conf file is set to false, restart Mind Studio and select one from the IP addresses of multiple NICs for the new IP address to take effect.

The path of the env.conf file is as follows: ~/tools/scripts/env.conf

6 Upgrade

Online upgrade of Mind Studio and Atlas 200 DK developer board is supported. You do not need to uninstall Mind Studio and reinstall it. You can directly upgrade the tool and the Atlas 200 DK developer board.

Before the upgrade, query the Mind Studio version by referring to 5.4.4 Querying the Mind Studio Version.

- If the current version is 1.3.XX, upgrade it by referring to Table 6-1.
- If the current version is 1.1.XX and you want to upgrade it to 1.3.XX, refer to Table 6-2.

# 6 Upgrade

Table 6-1 1.3.XX upgrade

| Source Version                        | Target Version                                                                                                                                                                                   |
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.3.T10.B770 to 1.3.T21.B880          | Upgrade to 1.3.T10.B770 to 1.3.T21.B880                                                                                                                                                          |
| 1.3.T21.B880 to 1.3.T25.B883          | Online upgrade not supported                                                                                                                                                                     |
| 1.3.T25.B883, 1.3.T26.B885, and later | Upgrade to 1.3.T28.B886 and later. However, you need to re-obtain the Mind Studio public keys. For details, see Configuring OpenPGP Public Keys in the installation guide of the target version. |

Table 6-2 Upgrade from 1.1.XX to 1.3.XX

| Source Version                    | Target Version                                                             |
|-----------------------------------|----------------------------------------------------------------------------|
| Versions earlier than 1.1.T8.B750 | Online upgrade not supported. Perform uninstallation then re-installation. |
| 1.1.T8.B750 to 1.1.T19.B880       | Upgrade to 1.3.T10.B770 to 1.3.T21.B880                                    |
| 1.1.T19.B880 to 1.1.T22.B883          | Upgrade to 1.3.T22.B881, 1.3.T23.B882, or 1.3.T25.B883 not supported                                                                                                                            |
| 1.1.T22.B883, 1.1.T23.B885, and later | Upgrade to 1.3.T28.B886 and later. However, you need to re-obtain the Mind Studio public key. For details, see Configuring OpenPGP Public Keys in the installation guide of the target version. |

XX indicates the version number.

```
```

## 6.1 Preparing for Upgrade

- Ensure that the havaged dependency has been installed on the Mind Studio server. If they are not installed, run the following command on the server: sudo apt-get install haveged
- Before the upgrade, run the gpg --list-keys command to check the public key files in the system. If the following two public keys are returned, the upgrade can be performed. If only one of them is returned, obtain the missing one by referring to 5.4.5 Configuring OpenPGP Public Keys before the upgrade.
- For the upgrade from 1.3.T25.B883 or 1.3.T26.B885 to 1.3.T28.B886 or later, reconfigure the Mind Studio public keys by referring to Configuring OpenPGP Public Keys in the installation guide of the target version. After the reconfiguration, run the gpg --list-keys command to check the public key files of the system. If two public keys shown in Figure 6-1 are returned, the upgrade can be performed.
- Before the upgrade, prepare the following installation packages.

Figure 6-1 Checking the public keys in the system

Table 6-3 Overview of the software packages

| Installation Package                                                          | Integrity Verification File                                                       | Application Scenario                                                                                                                                                                                                                                                                                                                          |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mini_mind_studio _Ubuntu.rar                                                  | mini_mind_studio_Ub untu.rar.asc                                                  | Mind Studio installation package for Ubuntu (x86)                                                                                                                                                                                                                                                                                             |
| MSpore_DDK- {version}-<uihost arch.os>-<host arch.os>-<device arch.os>.tar.gz | MSpore_DDK- {version}-<uihost arch.os>-<host arch.os>-<device arch.os>.tar.gz.asc | DDK installation package for Ubuntu (x86) ● DDK installation package in the Atlas 200 DK scenario: MSpore_DDK-{version}- x86_64.ubuntu16.04- aarch64.ubuntu16.04- aarch64.ubuntu16.04.tar.gz ● DDK installation package in the non-Atlas 200 DK scenario: MSpore_DDK-{version}- x86_64.ubuntu16.04- x86_64.ubuntu16.04- aarch64.miniOS.tar.gz |
| mini_developerkit -x.x.x.x.rar                                                | mini_developerkit- x.x.x.x.rar.asc                                                | Installation package of the Atlas 200 DK developer board                                                                                                                                                                                                                                                                                      |

- x indicates the version number of the software package.
- To upgrade Mind Studio and the Atlas 200 DK together, ensure that the Mind Studio, DDK, and Atlas 200 DK versions are consistent.
- To upgrade Mind Studio only, you do not need to download the Atlas 200 DK installation package mini_developerkit-x.x.x.x.rar.
- To verify software package integrity, you need to install the GnuPG tool and configure the OpenPGP public keys on the Linux server where Mind Studio is installed. For details, see 5.4.5 Configuring OpenPGP Public Keys .

## 6.2 Performing Upgrade

**Prerequisites**

Before the upgrade, log in to the server as the Mind Studio installation user, switch to the root user, and run the ./add_sudo.sh username script in the /usr/bin directory to add permissions to the user. The command is as follows:

su root ./add_sudo.sh username

**Procedure**

Step 1 Choose Help &gt; Upgrade. The Upgrade dialog box is displayed, as shown in Figure 6-2.

Figure 6-2 Upgrade dialog box

You can upgrade Mind Studio and Atlas 200 DK developer board separately or at the same time. If you click , the corresponding module is hidden or displayed. Table 6-4 describes the parameters on the Upgrade page.

Table 6-4 Parameters on the upgrade page

| Parameter        | Description                                                           |
|------------------|-----------------------------------------------------------------------|
| Mind Studio Path | Path of the Mind Studio installation package                          |
| Studio Asc Path  | Path of the verification file of the Mind Studio installation package |
| DDK Path         | Path of the DDK installation package                                  |
| DDK Asc Path      | Path of the verification file of the DDK installation package                                 |
| Mini Package Path | Path of the installation package of the Atlas 200 DK developer board                          |
| Mini Asc Path     | Path of the verification file of the installation package of the Atlas 200 DK developer board |
| Board IP          | IP address of the Atlas 200 DK developer board                                                |

If the Atlas 200 DK is upgraded separately, Mind Studio does not stop.

Step 2 Click next to Mind Studio Path, Studio Asc Path, DDK Path, DDK Asc Path, Mini Package Path, and Mini Asc Path, select the upgrade packages and .asc files with the same name as the upgrade packages, enter the IP address of the developer board in the Board IP text box, and click Update to upload the files, as shown in Figure 6-3.

Figure 6-3 Uploading the upgrade packages and verification files

The package names displayed in Figure 6-3 are for reference only.

Step 3 After successful upload and verification, the dialog box shown in Figure 6-4 is displayed. Click OK and the upgrade window is displayed. During the upgrade of Mind Studio and the developer board, the back-end services are disconnected and the front end is masked. Do not refresh or close the window during this phase. During the upgrade of the developer board only, the back-end services of Mind Studio are not disconnected and the upgrade progress is displayed, as shown in Figure 6-5.

Figure 6-4 Message displayed after the package is uploaded and verified

Figure 6-5 Developer board upgrade progress

Step 4 After the upgrade is successful, Mind Studio is restarted. The Mind Studio login page is displayed, as shown in Figure 6-6.

Figure 6-6 Upgrade success dialog box

After the upgrade is successful or the upgrade is canceled, close the dialog box shown in Figure 6-3. The redundant folders (upgrade and upgradeForLog) generated due to the upgrade by the Mind Studio server will be automatically deleted.

## Verifying the Upgrade

To check the Mind Studio version, see 5.4.4 Querying the Mind Studio Version.

## 6.3 Exception Handling

If an exception occurs during the upgrade, an error message is displayed in the upgrade dialog box. You can view the exception information in the ~/ upgradeLogForMindStudio directory of the server. The following log files are provided for the upgrade process.

Table 6-5 Upgrade log files

| Log File               | Description                                                                                                                                                                         |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| upgradeMindStudio.l og | Records information about uninstalling Mind Studio of an earlier version, saving user data, installing Mind Studio of a later version, restoring user data, and installing the DDK. |
| upgradeMini.log        | Records information about the process of transferring packages to the Atlas 200 DK developer board and restarting the Atlas 200 DK developer board.                                 |
| startMindStudio.log    | Records information about starting Mind Studio, generated whenMind Studio or the Atlas 200 DK developer board is upgraded separately.                                               |
| stopMindStudio.log     | Records information about stopping Mind Studio, generated when Mind Studio or the Atlas 200 DK developer board is upgraded separately.                                              |
| startNewIDE.log        | Records the information about the startup of Mind Studio of a later version after successful upgrade. If the upgrade fails, the log file is not generated.                          |
| restartStudio.log      | Records the information about the startup of Mind Studio of an earlier version after an upgrade failure. If the upgrade is successful, the log file is not generated.               |

- If the message "Unpacking package failed" is displayed during the upgrade, check whether the upgradeassembly folder exists in the /tmp directory of the server. If yes, delete the folder and try again.
- If the message "Please manually delete upgrade folder" is displayed during the upgrade, delete the ~/upgrade folder from the server and try again.
- If only stopMindStudio.log exists during the upgrade, check the log content, rectify the fault, restart Mind Studio, and perform the upgrade again.
- If only upgradeMindStudio.log exists but startMindStudio.log does not, run the bash mind_studio.sh rollback command in the ~/upgrade/scripts directory of the server to roll back to an earlier version, restart the system, and perform the upgrade again.

- If only startMindStudio.log exists, restart Mind Studio in the installation directory to complete the upgrade.
- During the upgrade of the Atlas 200 DK developer board, a script is executed. The return value of the script is displayed on the page when an error occurs.
- The return value 0 indicates that the script is executed successfully.
- The return value 1 indicates that the SD card space on the Atlas 200 DK developer board is insufficient.
- The return value 2 indicates that the script fails to be decompressed.
- Other return values are not defined currently.

Ensure that the IP address can be restored after the Atlas 200 DK developer board is restarted. Otherwise, manually configure the IP address. The upgrade log information of the Atlas 200 DK developer board is stored in upgradeMini.log in the ~/ upgradeLogForMindStudio directory.

### 6.3.1 What Do I Do If the Message "get board_id failed" Is Displayed During the Upgrade?

**Symptom**

When the online upgrade function of Mind Studio is used to upgrade the Atlas 200 DK, the system displays the failure message check board_id failed to upgrade Mini.... The upgrade log file upgradeMini.log prompts get board_id failed, as shown in Figure 6-7.

Figure 6-7 Upgrade failure log information

**Possible Cause**

During the online upgrade, the background checks board_id of the Atlas 200 DK. If the board ID of the Atlas 200 DK is changed or added, the ID verification will fail, resulting in an upgrade failure.

**Solution**

Write board_id of the Atlas 200 DK to the configuration file in ~/tools/scripts/ upgradeMiniBoardId.conf. During the upgrade, the system compares board_id obtained from the background with that in the configuration file. If they are the same, the verification is successful and the upgrade is allowed.

For example:

As shown in Figure 6-7, set board_id to 1004, write this ID to the configuration file in ~/tools/scripts/upgradeMiniBoardId.conf, and save the file. Then, perform the upgrade again.

### 6.3.2 What Do I Do If the Developer Board Failed to Be Upgraded Due to Timeout?

**Symptom**

**Solution**

During the upgrade of the developer board, the upgrade progress page is suspended, causing the timeout. The log file upgradeMini.log in the ~/ upgradeLogForMindStudio directory on the server prompts "ping: icmp open socket: Operation not permitted".

Log in to the server as the Mind Studio installation user, switch to the root user, and run the following commands:

su root chmod +s /bin/ping

- Symptom 1

# 7 FAQs

## 7.1 What Do I Do If the "apt-get update" Execution Fails During Mind Studio Installation?

**Symptom**

**Solution**

In environment preparation before Mind Studio deployment, after the source dependency is configured and the apt-get update command is executed, the following error message is displayed:

Aborted (core dumped)

```
Reading package lists... Done E: Problem executing scripts APT::Update::Post-Invoke-Success' if /usr/bin/test -w /var/cache/app-info - a -e /usr/bin/appstreamcli; then appstreamcli refresh > /dev/null; fi' E: Sub-process returned an error code
```

- Symptom 2

In environment preparation before Mind Studio deployment, after the source dependency is configured and the apt-get update command is executed, the following error message is displayed:

E: Could not get lock /var/lib/dpkg/lock - open (11: Resource temporarily unavailable)

E: Unable to lock the administration directory (/var/lib/dpkg/), is another process using it?

- Solution to symptom 1:

Run the following command to install the libappstream3 library:

sudo apt-get purge libappstream3 sudo apt-get update

- Solution to symptom 2:

Run the following commands to remove the lock:

sudo rm /var/cache/apt/archives/lock sudo rm /var/lib/dpkg/lock

## 7.2 What Do I Do If python-skimage or python3skimage Is Not Installed During Dependency Installation?

**Symptom**

**Solution**

- The installation of python-skimage requires dependencies including python, python-cycler, python-decorator, python-matplotlib, python-numpy, pythonpil, python-pyparsing, python-dateutil, python-tz, and python-six, which will be checked later. If any of the preceding software is not installed, an error message is displayed during the installation.
- The installation of python3-skimage requires dependencies including python3, python3-cycler, python3-decorator, python3-matplotlib, python3-numpy, python3-pil, python3-pyparsing, python3-dateutil, python3-tz, and python3six, which will be checked later. If any of the preceding software is not installed, an error message is displayed during the installation.
- If a message is displayed indicating that a certain dependency of pythonskimage fails to be installed, run the following command to install the dependency separately: sudo apt-get install xxx

xxx indicates the software required for python-skimage installation. Replace it with the name of the required dependency.

- If most of the dependencies are missing, run the following commands: sudo apt-get remove python-skimage sudo apt-get install python-skimage
- If a message is displayed indicating that a certain dependency of python3skimage fails to be installed, run the following command to install the dependency separately: sudo apt-get install xxx

xxx indicates the software required for python3-skimage installation. Replace it with the name of the required dependency.

- If most of the dependencies are missing, run the following commands: sudo apt-get remove python3-skimage sudo apt-get install python3-skimage

## 7.3 What Do I Do If "Software cycler (for python) decorator (for python) xxx error" Is Displayed During The Installation?

**Symptom**

**Solution**

The following error information is displayed during the installation:

Software cycler(for python) decorator(for python) xxx error (xxx indicates the dependent software package.)

Before running the following commands, ensure that the server where Mind Studio is installed is disconnected from the external network.

1. Run the dpkg -l | grep python-xxx command to check whether the software is installed. If the following information is displayed, the software is installed. Otherwise, run the sudo apt-get install python-xxx command to install it.
2. If the software is installed, run the python -c "import xxx" command to check whether the software is usable. If the software is usable, run the installation script again to install Mind Studio. If the problem persists, go to 3.

To check Pillow, replace xxx with PIL. To check dateutil, replace xxx with datetime. To check other software, replace xxx with the software name.

3. If the software has been installed but the python -c "import xxx" check fails, run the sudo apt-get remove python-xxx command to uninstall the

software, and then run the sudo apt-get install python-xxx command to reinstall it.

```
4. If the problem persists after 3, run the following commands: sudo apt-get remove python-xxx sudo apt-get remove python-skimage sudo apt-get remove python sudo apt-get autoremove sudo apt-get install python-skimage
```

If the problem occurs with Python 3, change python to python3 in Solution.

## 7.4 What Do I Do If a Message Is Displayed Indicating pip2 or pip Unavailability During Mind Studio or DDK Installation?

**Symptom**

During the installation of Mind Studio or a DDK, the system displays a message indicating that the pip2 or pip is unavailable and exits the installation, as shown in Figure 7-1 and Figure 7-2.

Figure 7-1 Message indicating pip2 unavailability

Figure 7-2 Message indicating pip unavailability

**Possible Cause**

pip2 is not updated during pip re-installation.

## Solution 1

Step 1 Run the su root command to switch to the root user and run the pip list command. If no error message is displayed, the pip is available. If an error message is displayed after the pip2 list command is executed, the pip2 is unavailable.

- Step 2 Run the rm /usr/bin/pip2 command as the root user to delete the pip2.
- Step 3 Run the ln -s pip pip2 command to create a soft link from the pip2 to pip.
- Step 4 Run the pip2 list command again. If no error message is displayed, it indicates that the fault has been rectified.

If the pip and pip2 are still unavailable, see Solution 2.

----End

If the pip installation is abnormal during the dependency installation, run the following commands in sequence:

```
sudo apt-get remove python-pip python3-pip wget https://bootstrap.pypa.io/get-pip.py python get-pip.py --user python3 get-pip.py --user
```

## 7.5 What Do I Do If Setuptools Is Uninstalled During Mind Studio Installation?

**Symptom**

During Mind Studio installation, a message is displayed indicating that Setuptools is uninstalled successfully.

**Possible Cause**

During Mind Studio installation, the system checks whether the versions of easy_install and Setuptools are consistent. If not, Setuptools is uninstalled during the installation.

**Solution**

## Solution 2

If Setuptools is uninstalled, perform the following steps to reinstall it:

1. Run the su root command to switch to the root user and run the following commands:

pip install setuptools

pip3 install setuptools

pip install --upgrade setuptools

pip3 install --upgrade setuptools

2. Run the easy_install version and easy_install3 version commands to check whether the software is installed. If the information shown in Figure 7-3 is displayed, the software is installed.

Figure 7-3 Result check

3. Switch to the Mind Studio installation user.

## 7.6 What Do I Do If an Error Is Reported During Mind Studio Installation?

The following errors do not affect the installation process and can be ignored.

Figure 7-4 Installation error - 1

Figure 7-5 Installation error - 2

## 7.7 What Do I Do If Mind Studio Installation Fails?

If Mind Studio fails to be installed, you are advised to uninstall Mind Studio and perform re-installation. Make sure that the uninstallation is successful, to avoid the affects of unthorough uninstallation.

For details, see 5.4.3 Uninstalling Mind Studio.

## 7.8 What Do I Do If I Cannot Access Mind Studio Using Chrome After Mind Studio Installation?

**Symptom**

**Solution**

After the Mind Studio installation is complete, https://IP:Port cannot be accessed using the Chrome browser.

Add the IP address of the server where Mind Studio is installed to the list of servers that do not have proxy servers. The setting method is as follows:

In the upper right corner of the Chrome browser, click . In the displayed dialog box, select Settings &gt; Advanced &gt; Open proxy settings. In the displayed Internet Properties dialog box, click LAN Settings. In the displayed Proxy Settings dialog box, add the server IP address of Mind Studio to the Exceptions text box, as shown in Figure 7-6.

## Solution 1

Figure 7-6 Adding a proxy exception

## 7.9 What Do I Do If Profiling Installation or Startup Fails?

If profiling fails to be installed, view the logs in ~/tools/log/profilerlog/ profiling.log. The possible causes are as follows.

## Symptom 1

The error information Restart httpd service ... Failed! is displayed in the log, indicating that the httpd service fails to be started.

## Possible Cause 1

The httpd service is not started, which is checked by running the following command:

ps -aux|grep httpd

1. Check whether the installation directory specified by the Mind Studio installation user has the 750 permission. If not, run the following command to assign the 750 permission to the installation directory: chmod 750 [Installation directory]

## Symptom 2

The error information Apache user: msvpUser cannot access dependency dir: ~/ tools/support/apache is displayed.

## Possible Cause 2

The permission on the installation directory is insufficient. As a result, the msvpUser user cannot access the installation directory.

1. Switch from the installation user to the msvpUser user by running the sudo su msvpUser command. If the switchover fails, switch to the root user and then to the msvpUser user by running the su msvpUser command.
2. Go to the installation directory /home/username/tools/support/apache level by level from the root directory. If Permission denied is displayed, the permissions on the directory are insufficient.

In the preceding directory, replace username with the Mind Studio installation user.

3. Check the groups to which the user belongs. The user belongs to msvpUser and ascend groups, as shown in Figure 7-7.

Figure 7-7 Checking the groups to which the msvpUser user belongs

1. In the directory for which Permission denied is displayed, run the ls -l command to check the permission and owner of the current directory, as shown in Figure 7-8. The group to which the current directory belongs is B882.

Figure 7-8 Checking the group to which the current directory belongs.

## Solution 2

2. If the permission of the installation directory is correct, verify the $JAVA_HOME configuration of the Mind Studio installation user.

Verification method:

Run the echo $JAVA_HOME command. If the command output is empty, $JAVA_HOME has to be manually configured. For details about how to configure the environment variable $JAVA_HOME, see Environment Preparation &gt; Installing Dependencies.

After the preceding configurations are complete, uninstall Mind Studio and reinstall it.

2. Check whether the permission on the current directory is 750.

Figure 7-8 shows that the permission on the current directory is 750. If the permission is less than 750, run the chmod 750 ascend command to escalate the permission.

3. Check whether the msvpUser user is a member of the owner group of the ascend folder.

As shown in Figure 7-7, the owner group of the msvpUser user is msvpUser or ascend, while the owner group of the user in Figure 7-8 is B882. Therefore, you need to add the msvpUser user to the B882 group by running the following commands:

```
su root usermod -a -G B882 msvpUser
```

Check whether the msvpUser user is added to the B882 group, as shown in Figure 7-9.

Figure 7-9 Checking the new group of the msvpUser user

4. Switch to the msvpUser user and go to the ascend directory again. Figure 7-10 shows that the ascend directory can be successfully accessed.
- ascend and B882 are examples only, which are subject to actual situations.
- After checking and resolving all the preceding symptoms, uninstall Mind Studio and reinstall it.

Figure 7-10 Checking the accessibility of the ascend directory

## 7.10 What Do I Do If the MongoDB Service Fails to Be Stopped During Uninstallation?

**Symptom**

During the uninstallation, the MongoDB service fails to be stopped due to abnormal operations (for example, manually deleting database files), as shown in Figure 7-11.

**Solution**

You can run the kill or pkill on Linux to forcibly stop the MongoDB, for example, pkill mongodb. Then, uninstall MongoDB.

## 7.11 What Do I Do If a WARNING or FAIL Result Is Returned in the Integrity Verification of a Software Package?

If a WARNING or FAIL result is returned in the integrity verification of a software package, the verification fails. Rectify the fault by referring to the handling suggestions described in Table 7-1.

Figure 7-11 MongoDB service stop failure during the uninstallation

Table 7-1 Verification result examples

| Verificatio n Result Descriptio n                                                        | Displayed Information                                                                                                                                                                                                                                                                                                                                                                                            | Verif icati on Res ult   | Solution                                                                                                                                  |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| The signature verificatio n is successful without exception.                             | gpg: Signature made Thu Jan 9 15:29:06 2014 CST using RSA key ID 27A74824 gpg: Good signature from "OpenPGP signature key for Huawei software (created on 30th Dec,2013) <support@huawei.com>"                                                                                                                                                                                                                   | PASS                     | N/A                                                                                                                                       |
| The signature verificatio n fails.                                                       | gpg: Signature made Thu Jan 9 15:29:06 2014 CST using RSA key ID 27A74824 gpg: BAD signature from "OpenPGP signature key for Huawei software (created on 30th Dec,2013) <support@huawei.com>"                                                                                                                                                                                                                    | FAIL                     | Download the target file again.                                                                                                           |
| The public key cannot be found.                                                          | gpg: Signature made Thu Jan 9 15:20:01 2014 CST using RSA key ID 27A74824 gpg: Can't check signature: public key not found                                                                                                                                                                                                                                                                                       | FAIL                     | Download the public key again. For details, see Configuring OpenPGP Public Keys.                                                          |
| The signature verificatio n is successful, but the public key is not ultimately trusted. | gpg: Signature made Thu Jan 9 15:29:06 2014 CST using RSA key ID 27A74824 gpg: Good signature from "OpenPGP signature key for Huawei software (created on 30th Dec,2013) <support@huawei.com>" gpg: WARNING: This key is not certified with a trusted signature! gpg: There is no indication that the signature belongs to the owner. Primary key fingerprint: B100 0AC3 8C41 525A 19BD C087 99AD 81DF 27A7 4824 | WAR NIN G                | After confirming that key ID (27A74824), set the trust level of Huawei public key to 5. For details, see Configuring OpenPGP Public Keys. |
| The correspon ding source file cannot be found.                                          | gpg: no signed data gpg: can't hash datafile: No data                                                                                                                                                                                                                                                                                                                                                            | FAIL                     | Download the target file again.                                                                                                           |
| The signature has expired.                                                      | gpg: Signature made 04/24/13 10:50:29 CST using RSA key ID 133B64E5 gpg: Expired signature from " OpenPGP signature test key <support@huawei.com>" gpg: Signature expired 04/25/13 10:50:29 CST                                                                                                                                     | FAIL                     | Download an updated target file.                               |
| The signature verificatio n is successful, but the public key has been revoked. | gpg: Signature made 06/13/13 11:14:49 CST using RSA key ID 133B64E5 gpg: Good signature from " OpenPGP signature test key <support@huawei.com>" gpg: WARNING: This key has been revoked by its owner! gpg: This could mean that the signature is forged. gpg: reason for revocation: Key is no longer used gpg: revocation comment: | WAR NIN G                | Download the latest public key and an updated target file.     |
| The correspon ding signature file cannot be found in the source file.           | None                                                                                                                                                                                                                                                                                                                                | WAR NIN G                | Download the signature file correspondin g to the target file. |

## 7.12 What Do I Do If the Online Help Documents Fail to Be Viewed?

**Symptom**

After installing Mind Studio on the Linux server, choose Help &gt; Documents as shown in Figure 7-12. The online help page is displayed. The content cannot be refreshed after a directory is clicked.

**Solution**

Figure 7-12 Navigation path for online help

Run the locale command to check whether the Linux server supports the UTF-8 character set, as shown in Figure 7-13. The following solutions are provided for the two situations.

Figure 7-13 Viewing the character set

1. If the Linux server does not support the UTF-8 character set: To install the UTF-8 character set, perform the following steps:
- a. Run the vi command to open the /etc/default/locale file and change the content to the following: LANG="en_US.UTF-8" LANGUAGE="en_US.UTF-8"

Run the :wq! command to save the file and exit.

- b. Run the following command: locale-gen -en_US:en

If an error is reported after this command is executed, ignore it and go to Step 2 directly.

- c. Run the following command to restart the server: reboot
2. If the Linux server supports the UTF-8 character set: In the directory where the online help documents are located, for example, :~tools/MindStudio-5.22.0/tomcat/webapps, run the following command:

convmv -f gb2312 -t UTF-8 --nosmart --notest -r docs

## 7.13 How Do I Configure the sshd_config File?

Perform the following steps:

Step 1 Run the following command to open the /etc/ssh/sshd_config file. If you do not have the permission, switch to the root user before running the following command:

sudo vim /etc/ssh/sshd_config

- Step 2 Run the /DenyUsers command to check whether DenyUsers is configured in the configuration file. If not, go to Step 2.1. If yes, go to Step 2.2.
1. Press Shift+g to go to the last line of the file, run the i command to enter the editing mode, and add the following content: DenyUsers msvpUser

msvpUser indicates the Apache user name. The value is the same as the value of apache_user in the env.conf configuration file for installation.

2. Run the i command to enter the editing mode. Add the Apache user name msvpUser to the end of the DenyUsers line.

Figure 7-14 shows the result.

Figure 7-14 Configuring DenyUsers

- Step 3 Run the :wq! command to save the file and exit.
- Step 4 Run the sudo sshd -t command to check whether the configuration file contains syntax errors. Then run the echo $? command to check the return value of the previous command. If the return value is 0, the syntax is correct. Go to Step 5. If the syntax is incorrect, correct it.
- Step 5 Runt the following command to restart the SSHD service.

sudo service sshd restart

----End

# 8 Appendix

## 8.1 Overview of Software Packages

For details about the Mind Studio and DDK installation packages, see Table 8-1.

Table 8-1 Description of the decompressed software packages

| Installation Package         | Content                         | Application Scenario                                                                                                                                           |
|------------------------------|---------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mini_mind_studio_Ubun tu.rar | MindStudio_Ubuntu- x86-64.tar | Mind Studio installation package for Ubuntu (x86)                                                                                                              |
| mini_mind_studio_Ubun tu.rar | install.sh                      | Installation script                                                                                                                                            |
| mini_mind_studio_Ubun tu.rar | check_sha.sh                    | Script used to verify the integrity of the preceding files. This script is automatically called to perform integrity check during the execution of install.sh. |
| mini_mind_studio_Ubun tu.rar | add_sudo.sh                     | Permission assignment script for the Mind Studio installation user                                                                                             |
| mini_mind_studio_Ubun tu.rar | del_sudo.sh                     | Permission removal script for the Mind Studio installation user                                                                                                |
| mini_mind_studio_Ubun tu.rar | env.conf                        | Configuration file                                                                                                                                             |
| mini_mind_studio_Ubun tu.rar | profiling_sudo.sh               | Profiling permission assignment script                                                                                                                         |
| MSpore_DDK-{version}- <uihost arch.os>-<host arch.os>-<device arch.os>.tar.gz For details about the parameter description, see Table 8-2. | ddk.tar.gz   | DDK installation package Installation script                                                                                                                   |
| MSpore_DDK-{version}- <uihost arch.os>-<host arch.os>-<device arch.os>.tar.gz For details about the parameter description, see Table 8-2. | install.sh   |                                                                                                                                                                |
| MSpore_DDK-{version}- <uihost arch.os>-<host arch.os>-<device arch.os>.tar.gz For details about the parameter description, see Table 8-2. | check_sha.sh | Script used to verify the integrity of the preceding files. This script is automatically called to perform integrity check during the execution of install.sh. |

The versions of the Mind Studio installation package and DDK installation package must be consistent.

Table 8-2 Naming rules of the DDK installation package

| Parameter        | Description                                                                                                                                                                                                                                                                                 |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| {version}        | Version number                                                                                                                                                                                                                                                                              |
| <uihost arch.os> | CPU architecture, OS, and version on the UI host side, for example, x86_64.ubuntu16.04 and x86_64.centOS7.4                                                                                                                                                                                 |
| <host arch.os>   | CPU architecture, OS, and version on the host side, for example, x86_64.ubuntu16.04 and x86_64.centOS7.4                                                                                                                                                                                    |
| <device arch.os> | CPU architecture, OS, and version on the device side, for example, aarch64.ubuntu16.04 and aarch64.miniOS ● Device-side architecture of the DK form (Atlas 200 DK), for example, aarch64.ubuntu16.04 ● Device-side architecture of the non-DK form (Atlas 300), for example, aarch64.miniOS |

## 8.2 Manual Installation

- Prerequisites

The operations required in 4 Environment Preparation and 5.1 Preparing for Installation have been completed.

- Procedure
- a. Decompress the installation package as the Mind Studio installation user. Run the following commands to decompress the Mind Studio installation package MindStudio_XXX-x86-64.tar obtained in Decompressing Installation Packages:

cd /home/username/director tar -zxvf MindStudio_XXX-x86-64.tar XXX indicates Ubuntu or CentOS. Replace it with an actual installation package during decompression.

- b. Switch to the root user and assign permissions to the Mind Studio installation user. (Perform permission assignment in the director directory where the installation package is stored.) su root

./add_sudo.sh username

Without permission assignment, the following information is displayed when you run the installation script, and the installation is stopped.

Please check if add_sudo.sh, del_sudo.sh exists and execute the add_sudo.sh script with root privileges

- c. Switch to the Mind Studio installation user and configure the env.conf file.

Go to the scripts directory generated after the decompression. The directory contains the parameter configuration file env.conf. Mandatory parameters are as follows:

- ip=&lt;your ip&gt;: IP address used for starting Mind Studio
- install_user: user for installing Mind Studio
- package_path: path of the permission assignment script add_sudo.sh

If you do not modify other parameters in the env.conf file, the default settings are used during the installation. To modify them, see Table 5-1.

- d. Run the installation script as the Mind Studio installation user.

After setting the parameters in the env.conf file, run the installation script mind_studio.sh in the scripts directory. The following installation choices are available.

- Run the following command to install Mind Studio and the DDK (normal installation):

bash mind_studio.sh install {absolute_ddk_path}

- Run the following command to install Mind Studio separately, which applies when Mind Studio is not properly installed: bash mind_studio.sh install
- Run the following command to install the DDK separately, which applies when the DDK is not properly installed and requires the Mind Studio restart after the installation):

bash mind_studio.sh installDDK {absolute_ddk_path}

{absolute_ddk_path} indicates the absolute path of the DDK, for example, / home/username/director/MSpore_DDK****tar.gz.

- e. Specify whether to back up the tools installation directory. (This message is displayed only when the user installation directory is not empty.) [INFO] /home/username/tools is not empty. Files in the directory will be cleared during installation. Are you sure to back up them? [Y/N]: (Input Y

or y and press Enter to exit the installation. Back up the contents in / home/username/tools and run the script again; or input N or n to delete the contents in /home/username/tools and continue the Mind Studio installation.)

- f. Follow the installation process to complete the installation.

If the message "Installation finished" is displayed, the installation is successful.

Regardless of the installation result, the del_sudo.sh script is automatically executed to revoke the permissions of the Mind Studio installation user. If you want to run the installation script again in the case of an installation failure, perform permission assignment again and re-install Mind Studio.

- If the installation fails, view the log file in ~/tools/log/mind_log and rectify the fault according to the error information.
- If a message is displayed indicating that profiling installation fails, view the log file in ~/tools/log/profilerlog and rectify the fault according to the error information. You can also obtain the solution to a specific problem by referring to 7.9 What Do I Do If Profiling Installation or Startup Fails?.

## 8.3 Open Source Third-Party Libraries

// cereal

// gflags

// glog

// opencv

cereal is a header-only BSD-licensed C++11 serialization library. It is designed to be fast, light-weight, and easy to extend. cereal takes arbitrary data types and reversibly turns them into different representations, such as compact binary encodings, XML, or JSON. The version currently used is 1.2.2.

For details, visit the cereal official website at http://uscilab.github.io/cereal/.

Google GFlags (GFlags), the Global Flags Editor, contains a Python-friendly C++ library that implements command-line flags processing, replacing systems like getopt(). The version currently used is 2.2.1.

For details, visit the GFlags official website at https://github.com/gflags/gflags.

Google glog is a library that implements application-level logging. This library provides logging APIs based on C++-style streams and various helper macros. Similar to assert defined in the standard C library, it provides more output information and flexibility.

For details, visit the glog official website at https://github.com/google/glog.

OpenCV (short for Open Source Computer Vision Library) is a library of programming functions mainly aimed at real-time computer vision. This cross-

## Protobuf

## Caffe

## TensorFlow

platform library sets its focus on real-time image processing, computer vision, and pattern recognition programs. The version currently used is 3.4.2.

For details, visit the OpenCV official website at https://opencv.org/.

Protobuf (short for Protocol buffers) are Google's language-neutral, platformneutral, extensible mechanism for serializing structured data. It is useful in developing programs to communicate with each other over a wire or for storing data. The version currently used is 3.5.1.

For details, visit the official website at https://developers.google.com/protocolbuffers/.

Caffe is short for Convolutional Architecture for Fast Feature Embedding. It is a commonly used deep learning framework in video and image processing applications. The version currently used is 1.0.

For details, visit the official website at http://caffe.berkeleyvision.org/.

TensorFlow is an open source software library for numerical computation using data flow graphs. The version currently used is 1.8.

For details, visit the official website at https://www.tensorflow.org/.

## 8.4 Change History

| Release Date   | Description                               |
|----------------|-------------------------------------------|
| 2020-05-30     | This issue is the first official release. |