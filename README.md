# Linux-server-security
With this guide you can keep your Linux servers safe

## Physical security
There are many tools to bypass the security of your servers physically. A Pendrive, an OMG cable, a CD etc. So do not have your servers available to anyone.

### BIOS/UEFI
* Password protected editing options.
* Disable boot media selection.
* Disable, if possible, USB booting and network cards, so if the attacker has access to the peripherals, an extra barrier will be found.

###GRUB
From GRUB you can easily get root access, so we have to add a master password to GRUB. This password will be encrypted in MD5 or if possible pbkdf2. Another recommendation would be to encrypt the disks in case the attacker shuts down the servers and takes the disks.

### Good practices
* TMOUT environment variable
By default in distributions that use bash there is a variable called TMOUT that is initially disabled. Its content consists of closing an inactive session in time.

* Prevent accidental restart and shutdown
(chmod a -x /etc/acpi/powerbtn-acpi-support.sh)

## Perimeter Protection

### iptables
Iptables is an application that allows the management, configuration and handling of packet filtering.

### Network level monitoring
* IDS Snort

## Internal network protection

### Spoofing or identity theft
In many cases, it is trusted that users will make correct use of it. The most common attacks are MITM or DDos but there are more types.

* ARP poisoning
This attack consists, as its name suggests, in poisoning the ARP cache tables of the victim machines. By poisoning the ARP cache it is possible to achieve a MITM environment.

* Protection against ARP Poisoning
It has been proven that a MITM attack taking advantage of the ARP functionality is very dangerous if it is carried out transparently, ensuring that the user does not suspect that he is being attacked.

* Static ARP entries
The most obvious of these is the use of static ARP entries on client machines so that they cannot be successfully attacked. This is a very simple and easy method to carry out only if you are in a small environment.

* ARP monitoring
As discussed earlier, it is not always possible or feasible to maintain static ARP entries on machines in an environment. If possible, the attack would certainly be prevented, but the network administrator may not have been aware that ARP attacks were being attempted. That is why there are applications that run as daemons on network machines that detect if an ARP Poisoning attack is being suffered, avoiding it and notifying the network administrator.

* Use of the /etc/hosts file
If static ARP entries were difficult to maintain, establishing an IP - Name mapping may be even more difficult.

* DHCP Spoofing
This attack consists of implementing an alternative DHCP server in order to assign IP addresses to those machines that acquire them automatically.

## Application layer protection

### Cages with chroot
If an app, PDF or other file is infected it can have serious consequences. For this we could use a virtual machine or Sandbox.
As its name suggests, with chroot the root of the system is modified for a certain process, making it believe that this is really the root of the files of the
system. This assumes that the process running inside a chrooted jail will "see" only what you want it to see. Initially, a cage is a completely empty casing, without any content, and it will be necessary to put in it the fair and necessary components to allow the execution of an application. Before moving on to an example of chroot, it is necessary to know some particularities and recommendations for its use:

* Never use chroot for a process running as root. It doesn't make any sense to do it like this since the root user will always be able to get out of the jail to the real filesystem.
* Include in the cage the minimum components necessary for the execution of a process. In the example you will see how to know these components and how to add them to the cage.
* Keep file permissions as restrictive as possible so as not to give a potential attacker even the slightest opportunity to modify files at will, executables, etc.
* Never use a chroot jail as the only security method implemented. It won't do any good at all.

### Resource limitation
In any type of environment, be it in production or testing, it is very important to control the resources that are used on a machine. On numerous occasions, the default settings or the limits that are established are not enough.

### Set a password policy
Passwords will expire every "x" time.
A minimum of characters, numbers, special characters.
Maximum attempts to access.

### Storage fees
When a disk or partition reaches its maximum capacity, the system, depending on the situation, will stop responding and providing its services if space is not freed. To prevent these situations, so-called quotas are used, which are nothing more than the establishment of storage limits for users or groups.

### Port Knocking
It is a mechanism that allows you to create a security layer by obscurity, that is, it helps to protect a system resource by hiding it.
For example with the SSH port to try to secure said environment, you want to prevent network scans, with nmap for example, from revealing that this machine
It has an SSH service. The idea is that initially the port used by SSH is blocked by firewall rules and only when a user wants access is access granted exclusively to him.

### SPA, Single Packet Authorization
Although Port-Knocking can be useful a priori, it is too basic a concept and if an attacker acquires the sequence of packets used, they could end up enabling access to the SSH server. Of course you would then have to deal with the security of the SSH service but this first layer would have easily broken it. It can be considered as a variant of Port-Knocking, however, a sequence of packets is not sent, but rather a single packet with encrypted information. This adds privacy and authentication.

### HIDS, Host-based Intrusion Detection System
It is a system or series of mechanisms by which changes, accesses and irregularities in a machine are detected. The benefit that a manager obtains from
systems using this type of software is that you will be alerted and your interaction may not even be required to solve a potential problem detected.

## development environment

### MySQL
* listening direction
If we add the parameter (bind-address=127.0.0.1) we manage to block accesses that do not come from localhost.
*Loading local files
By default, from the MySQL console the following could be done: mysql> select load_file('/etc/passwd');
To disable the loading of files from MySQL and avoid a possible leak of information from the file system through SQLi, it is necessary to establish the following configuration in the file (/etc/mysql/my.cnf)
* Rename the root user
mysql> update mysql.user set user='ivan' where user='root';
mysql> flush privileges;
* Check existence of anonymous users
Many MySQL installations create anonymous users by default that could be used to extract information. In order to solve it:
mysql> select user users,host from mysql.user where user='''';
* Control user privileges
* mysql_secure_installation
It is a script responsible for securing certain aspects of MySQL. Specifically, it will look for anonymous users, it will establish the password of the administrator user, it will eliminate the test databases, etc. It is recommended to run this script, either before or after the settings already mentioned.

### PHP
* expose_php
If an attacker performs 'fingerprinting' they can obtain the version of PHP used. To avoid it in the /etc/php/apache2/php.ini file and add (expose_php= Off)
* display errors
To disable the output of errors that may display sensitive information (display_errors= Off)
* openbasedir
One of the most common attacks on web applications are called Path Traversal. The most classic example for this is trying to get files with sensitive information like /etc/passwd. To avoid this (open_basedir= /var/www)
* disable functions
It is possible to disable certain PHP functions. This can be useful in situations where an attacker manages to inject some kind of shell.
(disable_functions= phpinfo, exec, system, eval, shell_exec, ini_set)
* Disable RFI
On numerous occasions, there are badly programmed scripts that, through GET or POST parameters, would allow the inclusion of malicious code by an attacker. There are two options to disable this behavior, both to include script code and to open files.
(allow_url_fopen=Off)
(allow_url_include=Off)

### Security in the SSH protocol
Basic Directives
First, the sshdconfig file is treated:
* Port: Indicates on which port the SSH service will listen, by default it is 22 but we can change it.
* PermiiRootLogin: This directive prohibits a user from logging into the server with the root user. This prevents brute force attacks on the root user.
* MaxAuthTries: This directive prevents brute force attacks from endlessly trying credentials.
* LoginGraceTime: This directive indicates the maximum time, in seconds, to introduce the credentials in the authentication.
* AllowGroups: This directive specifies the name of the groups to which users who can log in remotely using the SSH protocol belong.
* AllowUsers: It can be specified in the configuration file followed by a list of users that can log in through the SSH protocol.
* Ciphers: This directive specifies which ciphers the protocol version will support.
* TCPKeepAlive: Disabling this directive allows to prevent impersonation attacks, spoofing type attacks.
* DenyGroups: This directive is similar to AllowGroups but with a deny approach.
* DenyUsers: Contrary to the AllowUsers directive, any user associated with this directive will not be able to log in remotely using the SSH protocol.

* Fail2ban
Its function is to penalize the connection, either by means of a block, of an origin that tries to carry out a brute force process. In other words, when an IP address or several try to carry out a brute force attack on a service, such as FTP, SSH, etc., this application will detect and penalize said behaviors.


### Privilege escalation
In Linux there are many ways to obtain privileges, we are going to comment on some of them:
* By SUDO
* Buffer overflow
* Write permission on the passwd file
* Capabilities
* Special permissions
* Exploits
* CRON task

