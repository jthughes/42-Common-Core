# 01 - Born2BeRoot
## The Project
Configure Debian to be a GUIless server, configuring an encrypted lvm partioning setup, configure user settings to be appropriately secure, setup a firewall, build a shell script to output system monitoring information using a chron job, and  configure some services like a functional Wordpress site hosted with lighttpd.

## Research
- Useful for [password policy configuration](https://www.server-world.info/en/note?os=Debian_11&p=pam&f=1)

## What I learned
This was a really interesting process, going through and working out how to meet all the requirements one by one. Mostly this was a process of slowly setting all the pieces up in a VM at home, while writing down in a file every single command I entered (or at least the essential ones to achieving the required result) so that I could minimised the time spent working in a VM on campus, as the devices on campus are not well suited to running VMs. 

Below is a sample of some of the sections of the document I wrote:

### Configure ``sudo``permissions
1. Create folder ``sudo`` in ``/var/log/``
   ```
   cd /var/log
   sudo mkdir sudo
   ```
2. Create file ``default`` in ``/etc/sudoers.d/``
   ```
   sudo touch /etc/sudoers.d/default
   ```
3. Add the following to the newly created ``default`` file using ``sudo nano``
   ```
   Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin/bin:snap/bin"
   Defaults requiretty
   Defaults logfile="/var/log/sudo/sudo.log"
   Defaults log_input, log_output
   Defaults badpass_message="Password not accepted, please retry."
   Defaults passwd_tries=3
   ```
   * ``tty`` is short for "teletypewriter", and is a command to output the name of a file to the terminal, and is used to check if the command is connected to a terminal (called by a user) or not (called by script).


### What is the difference bertween ``aptitude`` and ``apt``?
 - ``apt`` (which was previously ``apt-get``) is the default command line package manager in Debian for installing, updating, and removing packages.
 - ``aptitude`` is was built to be a more advanced version of ``apt``, doing things like automatically removing dependences no longer needed due to the depended package being remove, or allowing you to query why a command cannot be run. Can also notify of deprecated dependencies that are no longer officially suppported.
### What is ``SELinux`` and ``AppArmor``?
-  These are Linux kernel security modules that provide mandatory access control (MAC).
This means the administrator can set security policies that users cannot override.
-  SELinux is setup by default in distros like Fedora, openSUSE, RHEL (from which Rocky is derived), and CentOS.
-  AppArmor is setup by default in distros Debian.
-  The status of AppArmor can be checked with the following command:
   ```
   sudo aa-status
   ```
   or
   ```
   sudo /usr/sbin/aa-status
   ```