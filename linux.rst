.. contents:: Linux Tips

Command Line Shortcuts
===========================

Command Editing Shortcuts
----------------------------

- Ctrl + a – go to the start of the command line
- Ctrl + e – go to the end of the command line
- Ctrl + k – delete from cursor to the end of the command line
- Ctrl + u – delete from cursor to the start of the command line
- Ctrl + w – delete from cursor to start of word (i.e. delete backwards one word)
- Ctrl + y – paste word or text that was cut using one of the deletion shortcuts after the cursor
- Alt  + b – move backward one word (or go to start of word the cursor is currently on)
- Alt  + f – move forward one word (or go to end of word the cursor is currently on)
- Alt  + t – swap current word with previous
- Ctrl + t – swap character under cursor with the previous one
- Ctrl + backspace - delete a previous word (support path delimeter, such as /)

Command Recall Shortcuts
---------------------------

- Ctrl + r – search the history backwards
- Ctrl + g - quite the search
- Ctrl + p – previous command in history (i.e. walk back through the command history)
- Ctrl + n – next command in history (i.e. walk forward through the command history)

- Alt + . – use the last word of the previous command

Command Control Shortcuts
----------------------------

- Ctrl + l – clear the screen
- Ctrl + c – terminate the command
- Ctrl + z – suspend/stop the command
- Ctrl + s – freeze the terminal(stops the output to the screen)
- Ctrl + q – unfreeze the terminal(allow output to the screen)

List Shortcuts/Bindings
--------------------------

- sh/bash

  ::

    help bind
    bind -p
    bind -p | grep '^"\\C-'
    bind -p | grep '^"\\e'
    (\C-: Ctrl +, \e: meta/Alt +)

- zsh

  ::

    man zshzle
    bindkey -l
    bindkey -M <keymap name>
    bindkey -M emacs | grep '^"\^'
    bindkey -M emacs | grep -i '^"^\['

Long Command Edit/edit-command-line
--------------------------------------

 - export EDITOR='vim'
 - <Ctrl+x><Ctrl+e>
 - :wq

Change Line Editing Mode
---------------------------

- bash: set -o vi
- zsh : bindkey <-e|-v>

Command Quick Substitution
-----------------------------

- ^string1^string2^     - Repeat the last command, replacing string1 with string2. Equivalent to !!:s/string1/string2/
- !!gs/string1/string2/ - Repeat the last command, replacing all string1 with string2
- Refer to: https://www.gnu.org/software/bash/manual/bashref.html#History-Interaction

Cutting Edge Tools
=====================

Modern Unix
-------------

A set of unix tools improving daily efficiency - https://github.com/ibraheemdev/modern-unix

pandoc
---------

a general markup converter supporting md, rst, etc.

::

  pandoc <file name with suffix> | w3m -T text/html
  pandoc -s --toc <file name with suffix> [--metadata title=<title string>] | w3m -T text/html

tshark
---------

Terminal based Wireshark.


::

  tshark --color -i eth0 -f "port 8080"
  tshark --color -i eth0 -d udp.port=4789,vxlan -c 3 -f "port 4789"
  tshark --color -V -i eth0

ranger
---------

a great command line file browser.

::

  sudo apt install ranger
  ranger

Keyboard Mapping/Shortcuts Cheatsheet: https://ranger.github.io/cheatsheet.png

*Configuration:*

- Use vi as the default editor:

  ::

    export VISUAL='vim'
    export EDITOR='vim'

    (Note: handle_extension in ~/.config/ranger/scope.sh may need to be modified when vim is not used)

- Enable syntax highlighting:

  ::

    (in ~/.config/ranger/scope.sh, enable below line but comment out the highlight line)
    pygmentize -f "${pygmentize_format}" -O "style=${PYGMENTIZE_STYLE}" -- "${FILE_PATH}" && exit 5

- Integrate with fzf: refer to https://github.com/ranger/ranger/wiki/Commands

- Customize applications to use when open a given type of files

  1. ranger --copy-config=rifle if ~/.config/ranger/rifle.conf does not exist;
  2. Edit rifle.conf to associate files with applications;

ripgrep
----------

ripgrep is a line-oriented search tool that recursively searches your current directory for a regex pattern while respecting your gitignore(use **--no-ignore** to ignore those ignore files) rules. It is much more faster than any other tools, like grep, fd, etc.

::

  rg -e <pattern>
  rg -i -e <pattern>
  rg -F <fixed string>
  rg --no-ignore <pattern>

fzf
------

A command-line fuzzy finder, which integrates well with other tools.

::

  # Search history
  Ctrl + r
  # Change into a directory
  Alt  + c
  # Edit a file
  vim <path>/**<TAB>
  # Change into a directory
  cd  <path>/**<TAB>
  # Traverse the file system while respecting .gitignore
  rg -e <pattern> | fzf

fd
-----

fd is a simple, fast and user-friendly alternative to find. fd ignore files defined in .gitignore, to search files including such files, use option **--no-ignore**.

::

  fd <pattern>
  fd -F <pattern>
  fd -i <pattern>
  fd --no-ignore <pattern>

curlftpfs
------------

mount a ftp share as a normal file system:

::

  curlftpfs ftp://<site url> <mount point>

jq
-----

Reference:

- https://stedolan.github.io/jq/tutorial/
- https://programminghistorian.org/en/lessons/json-and-jq

**Exapmples**

::

  # validate if the conent of a document is a legal json string + pretty format
  cat <file name>.json | jq '.'
  # select objects based on field match
  tct_cli vpc eni list | jq -r '.[] | select(.NetworkInterfaceName | test("metaeni-80"))'
  # reverse the match
  tct_cli vpc eni list | jq -r '.[] | select(.NetworkInterfaceName | test("metaeni-80") | not)'
  # output selected fields as csv - use jq -r to avoid \"
  tct_cli vpc eni list | jq -r '.[] | select(.NetworkInterfaceName | test("metaeni-80")) | [.NetworkInterfaceName, .NetworkInterfaceId] | @csv'

yq
-----

yq is similar as jq, but it is used to translate yaml/xml to json:

::

  cat <file name>.yaml | yq '.'

gpg
------

Encryp/decrypt a file.

::

  gpg -c <file>
  gpg -d <file>

busybox
-----------

BusyBox combines tiny versions of many common UNIX utilities into a single small executable. Since it provides binary download, it can be used on Unix/Linux based systems which do not support package instalaltion (scp busybox onto them and run directly).

Busybox ships with a large num. of applets (refer to `its document <https://busybox.net/downloads/BusyBox.html>`_ for details). Below is an example how to use busybox as a HTTP server:

::

  busybox httpd -p 0.0.0.0:8080 <html site root>
  pkill busybox

moreutils
------------

**moreutils** is a software package containing quite some useful tools can be leveraged during daily work.

- errno: list ERRNO and their short descriptions;
- ifdata: get NIC information, such as MTU, ip, etc., which can be used without further processing;
- combine: combine 2 x files together based on boolean operations;
- lckdo: run a program with a lock.

Package Mangement
====================

Which package provides the binary
------------------------------------

- RHEL/CentOS

  ::

    yum whatprovides nslookup

- Arch

  ::

    sudo pacman -Fy
    pacman -Fx <file name>

- Ubuntu

  ::

    sudo apt-get install apt-file
    sudo apt-file update
    apt-file search <file name>

Install a specified version RPM through yum
----------------------------------------------

::

  # yum --showduplicates list <package name>
  # yum install <package name>-<version>

arch aur package helper yay
------------------------------

Yet Another Yogurt - An AUR Helper Written in Go for archlinux based distros:

- Search a package : yay -Ss <package>
- Install a package: yay -S <package>
- Upgrade pacakges : yay -Syu --aur

Install a Package with a Specific Version on Ubuntu
------------------------------------------------------

::

  apt policy <package name>
  apt install <package name>=<version>

View package groups on Arch
------------------------------

::

  pacman -Sg[g]
  pacman -Qg[g]

List all available versions of a packge with yum
---------------------------------------------------

::

  [root@wnh9h1 yum.repos.d]# yum --showduplicates list kernel-uek.x86_64 | head
  Installed Packages
  kernel-uek.x86_64              3.8.13-35.3.1.el7uek                @anaconda/7.0
  Available Packages
  kernel-uek.x86_64              3.8.13-35.3.1.el7uek                ol7_UEKR3
  kernel-uek.x86_64              3.8.13-35.3.2.el7uek                ol7_UEKR3
  kernel-uek.x86_64              3.8.13-35.3.3.el7uek                ol7_UEKR3
  kernel-uek.x86_64              3.8.13-35.3.4.el7uek                ol7_UEKR3
  kernel-uek.x86_64              3.8.13-35.3.5.el7uek                ol7_UEKR3

EPEL for RHEL/CentOS/Fedora
-------------------------------

EPEL stands for **Extra Pacakges for Enterprise Linux**, a.k.a repositories for extra packages, which contains lots of tools such as fio, ipvsadm, etc.

::

  yum install epel-release

Fedora Copr
---------------

Fedora Copr is an easy-to-use automatic build system providing a package repository as its output. It can be used as package repositories for non official (including packages which are not covered by epel).

How to leverage Copr:

#. Go to https://copr.fedorainfracloud.org/;
#. Search the package which is not in the official repositories and epel, say "fasd";
#. Select/click the project which is the best from the result list;
#. Click the "Repo Download" link based on the target release;
#. Copy the URL field of the browser (not the content of the repo), say https://copr.fedorainfracloud.org/coprs/rdnetto/fasd/repo/fedora-33/rdnetto-fasd-fedora-33.repo for fasd;
#. sudo yum-config-manager --add-repo=<the repo link just copied>;
#. Check /etc/yum.repos.d/<the newly created repo name>.repo to make sure the contents generated is correct.

   For example, the baseurl for fasd is https://download.copr.fedorainfracloud.org/results/rdnetto/fasd/fedora-$releasever-$basearch/. If the OS used is CentOS 8, this will be interpreted as https://download.copr.fedorainfracloud.org/results/rdnetto/fasd/fedora-8-x86_64/ which is of course not correct. To fix this issue, hard code the url as https://download.copr.fedorainfracloud.org/results/rdnetto/fasd/fedora-33-$basearch/.

#. Done.

Install package offline on Arch
----------------------------------

1. Find the package by surfing: https://www.archlinux.org/packages/
2. **Download From Mirror** from the package page, the file <package name>.pkg.tar.xz will be downloaded;
3. sudo pacman -U <package name>.pkg.tar.xz

Choose Arch mirror
---------------------

Official Mirror List
~~~~~~~~~~~~~~~~~~~~~~~

- https://www.archlinux.org/mirrorlist/all/

List by Speed(based on local test)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
  sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup
  rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist
  pacman -Syy

Server Side Ranking
~~~~~~~~~~~~~~~~~~~~~~

::

  reflector --latest 10 --protocol http --protocol https --sort rate --save /etc/pacman.d/mirrorlist
  reflector --country China --country Singapore --country 'United States' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

Shortcut for Manjaro
~~~~~~~~~~~~~~~~~~~~~~~

::

  sudo pacman-mirrors --fasttrack && sudo pacman -Syyu

Only use mirrors from a country
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  sudo pacman-mirrors -c China && sudo pacman -Syyu

Create a local yum repo with DVD iso
---------------------------------------

- Disable all other repositories by make "enabled=0" on all files under /etc/yum.repos.d;
- Mount the iso: mount -o loop
- Create a repo config file under /etc/yum.repos.d with below contents, the name can be anything:

  ::

    [Repo Name]
    name=Description name
    baseurl=file://absolute path to the mount point
    enabled=1

- yum clean all
- yum repolist : You should be able to see the new repo
- Or through command line: yum-config-manager --add-repo file:///<Mount point> (Public key should be imported with command like "rpm --import /media/RPM-GPG-KEY-redhat-beta" before installing packages with the newly added repo )


dnf
-------

dnf, which means dandified yum, is the default package manager for replacing yum.

Configuration
~~~~~~~~~~~~~~~~~~

- /etc/dnf/dnf.conf: dnf configuration
- /etc/yum.repos.d: repo definitions

List
~~~~~~~~

- dnf list --all: list all installed and available packages
- dnf list [<--installed\|--available\|--extras\|--obsoletes\|--recent>] [expression]: list packages [matching expression]
- dnf list --upgrades [expression]: list upgradable pacakges [matching expression]
- dnf list --autoremove: list orphaned packages

Info
~~~~~~~~~

- dnf info <package name>: show information for package
- dnf provides <path/to/file>: show packages own the file

Install
~~~~~~~~~~~

- dnf install <package name>: install package
- dnf install <path/to/local/rpm>: install a local rpm package
- dnf reinstall <package name>: reinstall package
- dnf downgrade <package name>: downgrade package

History
~~~~~~~~~~~~

- dnf history list: list dnf transactions
- dnf history info transaction: show info for a particular transaction
- dnf history redo transaction: redo a transaction
- dnf history rollback transaction: rollback a transaction
- dnf history undo transaction: undo a transaction

Update
~~~~~~~~~~

- dnf check-update: check if updates are available
- dnf upgrade: upgrade packages to latest version
- dnf upgrade-minimal: update major patches and security

Repo
~~~~~~~~

- dnf repolist [<--enabled\|--disabled\|--all>]: list repos
- dnf config­manager --add-repo=URL: add a repo

Note: config­manager is a dnf plugin which needs to be installed(dnf install dnf-plugins-core)

Group
~~~~~~~~~~

- dnf group summary group: show installed and available groups
- dnf group info <group name>: show information for a group
- dng group list [expression]: list groups [matching expression]

Uninstall
~~~~~~~~~~~~~~~

- dnf remove <package name>: remove a package
- dnf autoremove: remote orphaned packages

Services
============

Service Logs
----------------

- Check service logs based on time window

  ::

    systemctl | grep '<service name>' ---> locate the service unit name
    journalctl -S <time stamp> -u <service name>

- Check latest logs

  ::

    journalctl -f ---> As tail

- Do not wrap log lines

  ::

    journalctl --all --output cat -u <service name>

- Clean logs

  ::

    journalctl --flush --rotate
    journalctl --vacuum-time=1s

Reload configuration file without restarting service
--------------------------------------------------------

SIGHUP as a notification about terminal closing event does not make sense for a daemon, because deamons are detached from their terminal. So the system will never send this signal to them. Then it is common practice for daemons to use it for another meaning, typically reloading the daemon's configuration.

::

  kill -s HUP <daemon pid>

Use Chrony for time sync
----------------------------

Modern Linux distributions start to use Chrony as the default application for time sync (NTP) instead of the classic ntpd. Chrony comes with 2 x programs:

- chronyd: the background daemon
- chronyc: CLI interface

Usage:

- Configuration (/etc/chrony.conf or /etc/chrony/chrony.conf) (Chrony NTP server and client use the same configuration)

  ::

    # Define the NTP server sources
    server 192.168.16.22 iburst

    # If it is configured as a NTP server, enable below options
    # Serve time even if not synchronized to a time source.
    #local stratum 0
    # Allow NTP client access from local network.
    #allow 192.168.0.0/16

- Start the service

  ::

    systemctl enable chronyd.service
    systemctl start chronyd.service

- Check NTP sources

  ::

    chronyc sources -v

- Check current time sync status

  ::

    chronyc tracking

- If time has been synced, it will be reflected from command "timedatectl"
- To sync time immediately

  ::

    chronyc makestep

Postfix
-----------

Configure Postfix as SMTP Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A SMTP server is able to send emails but not receive emails. It is useful for situations such as sending notifications which does not expect any reply.

- Installation

  ::

    # dnf install postfix
    pacman -S postfix

- Restrict access

  ::

    # /etc/postfix/main.cf
    # Use any of below solution to ensure hackers cannot leverage this server to send spam
    # Solution 1
    # inet_interfaces = ALL
    # mynetworks = 127.0.0.0/8, 10.10.10.0/24
    # Solution 2
    inet_interfaces = loopback-only
    inet_interfaces = localhost

- Define Relay SMTP Server

  ::

    # By default, postfix sends email directly to the Internet. However, this won't work
    # sometimes. For example, when there is a firewall or other security rules between postfix
    # and the receivers, the email cannot be delivered.
    # Relay SMTP servers can be used to work around the problem - trusted internally and
    # forward emails on behalf of postfix
    relayhost = [10.10.10.10]

- Start the service

  ::

    systemctl start postfix

Send Emails from CLI
~~~~~~~~~~~~~~~~~~~~~~~~

::

  # Simple command
  echo -e "Subject: Test email\n\nThis is a test email\n" | sendmail -t <recevier@xxx.xxx>

  # Or with here document to contain more mail meta
  cat <<EOF | sendmail -t
  To: recipient@example.com
  Subject: Testing
  From: sender@example.com

  This is a test message
  EOF

Check and Clear Mail Queues
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  # Check queues
  mailq
  # Delete mails from queueus
  postsuper -d ALL

kdump config
---------------

1. Install "kernel-debuginfo-common" and "kernel-debuginfo", by default, these two packages are not kept in yum repository, they need to be downloaded from internet;
#. Install "kexec-tools" and "crash":

   - yum install kexec-tools
   - yum install crash

#. Edit grub.cfg, append "crashkernel=yM@xMparameter " to kernel:

   - Y : memory reserved for dump-capture kernel;
   - X : the beginning of the reserved memory;
   - This can be done with command: grubby --update-kernel=ALL --args="crashkernel=yM@xM";
   - "crashkernel=yM@0" or "crashkernel=yM" should be used if kdump service cannot start;

#. Reboot and check with command: cat /proc/iomem | grep 'Crash kernel';
#. Configure /etc/kdump.conf to set dump path and other options, by default, only below two options are required:

   - path /var/crash
   - core_collector makedumpfile -c -d 31

#. "service kdump restart" if the configuration file has been changed;
#. Trigger a dump:

   - echo "1" > /proc/sys/kernel/sysrq
   - echo "c" > /proc/sysrq-trigger

#. System will begin dump and reboot;
#. Check if vmcore file is generated under the kdump path;
#. Done.

MISC Tips
============

List table of contents of manpage
------------------------------------

Based on the level of title you want to see, below commands can be used(3 stands for 3 x levels of titles).

::

  man ovs-vsctl | grep '^ \{0,3\}[A-Z]'

Here Document
----------------

Here document in shell is used to feed a command list(multiple line of strings) to an interactive program or a command, such as ftp, cat, ex.

It has 2 x forms:

- Respect leading tabs(but not spaces): <<EOF
- Suppress leading tabs: <<-EOF

Define a variable containing multiple lines of string
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Note**: a variable should be enclosed in double quotes while referring to it, otherwise, it will be treated as a single line string due to the shell expansion.

::

  read -d '' var_name <<-EOF
  line1
  ...
  EOF
  echo "$var_name"

Redirect here document output
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  {
     mongo 192.168.1.101/ycsb <<EOF
     use ycsb;
     sh.status(true);
     EOF
  }  | tee -a /tmp/output

Avoid variable interpretation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  cat > /tmp/a.sh << "EOF"
  var1=$( ls -l )
  for i in `seq 1 10`; do
    echo $i
  done
  EOF

Here String
--------------

**<<<** is here string, a form of here document. It is used as: COMMAND <<< $WORD, where $WORD is expanded and fed to the stdin of COMMAND.

Sample:

::

  while read -r line; do
  command1
  command2
  ......
  done <<< "$variable_name"

awk
------

Built-in Variables
~~~~~~~~~~~~~~~~~~~~~

- FS : input field separator
- OFS: output field separator
- RS : record separator
- ORS: output record separator
- NF : number of fields
- NR : number of records

Common Command Format
~~~~~~~~~~~~~~~~~~~~~~~~

::

  awk '
     BEGIN { actions }
     /pattern/ { actions }
     /pattern/ { actions }
     .....
     END { actions }
  ' filenames

awk define variables
~~~~~~~~~~~~~~~~~~~~~~~

-v <variable name>=<variable value>

Examples:

::

  awk -v name=Jerry 'BEGIN{printf "Name = %s\n", name}'
  awk -F= -v key=$1 '{if($1==key) print $2}'
  Notes:
    1. The first $1 is the first shell positional parameter;
    2. The second $1, and the following $2 is the first and second column/field of a input record.

Get lines whose fields/columns is a special word
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  awk '$7=="some_word" {for(i=1;i<=NF;++i){printf "%s ", $i}; printf "\n"}'

Get lines whose fields/columns match a sepcial word
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  awk '$7~/some_word/ {for(i=1;i<=NF;++i){printf "%s ", $i}; printf "\n"}'

Output a range of fields
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  awk '{for(i=3;i<=8;++i){printf "%s ", $i}; printf "\n"}'

Calculate the sum of a column
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  awk '{sum += $3}END{print sum}'

Calculate duplicate rows with hash
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  # column 1 is used as the key, and calculate the sum when it is the same
  awk '{cnt[$1] += $2}END{for (k in cnt) print k, cnt[k]}'

ssh
-------

ssh client configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Configuration file: ~/.ssh/config(mode 400, and create if it does not exist);
2. man ssh_config to find all supported options;
3. Format:

   ::

     Host <host pattern, such as *, ip, fqdn>
         <Option Name> <Option Value>
         ......
     --- OR ---
     Host <host pattern, such as *, ip, fqdn>
         <Option Name>=<Option Value>
         ......

4. Examples:

   - Disable host key checking:

     ::

       Host *
           StrictHostKeyChecking no
           UserKnownHostsFile /dev/null

   - Use ssh v1 only

     ::

       Host *
           Protocol 1

Add ssh public key to remote servers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To configure key based ssh login, the ssl public key (generated with ssh-keygen -t rsa) needs to be copied and appended to the file **~/.ssh/authorized_keys** on remote servers.

Command **ssh-copy-id** can be leveraged to do the work automatically.

Enable Additional SSH Key Algorithms
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When ssh to some equipment, errors as below may be prompted:

::

  no matching key exchange method found. Their offer: xxx, yyy

To login such equipement:

::

  ssh -oKexAlgorithms=+xxx <user>@<equipment>

Run multiple Remote Commands with SSH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  # ssh <user>@<host> ""
  ssh root@192.168.10.10 "while : ; do top -b -o '+%MEM' | head -n 10; echo; sleep 3; done"
  ssh root@192.168.10.10 "while : ; do top -b -o '+%MEM' | head -n 10; echo; sleep 3; done"
  ssh root@192.168.10.10 "vmstat -w -S m 5 10"
  ssh root@192.168.10.10 "while :; do docker stats --no-stream; echo; sleep 5; done"

Run commands without password by using sshpass
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  sshpass -p <password> ssh -p <port> -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ConnectTimeout=5 <IP> '<commands>'

Show Process Information
-----------------------------

Show cpu, memory, etc. usage per process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ps command can be used with customized output format to show per process inforamtion including cpu, mem, cgroups, etc.

::

  ps -e -o "pid,%cpu,%mem,state,tname,time,command"

List Non-Kernel Process
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  ps --ppid 2 -p 2 --deselect

List Task/Process Switch Stats
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  pidstat -w

Sort based on fields with top
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::


  # Refer to section "FIELDS / Columns" of "man top" for supported fields
  top -b -o '+%MEM'

Only show specified processes with top
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  top -c -p <process id, ...>

Show process threads
~~~~~~~~~~~~~~~~~~~~~~~~

::

  ps -T -p <pid>
  top -H -p <pid>

Show the CPU process/thread is running on
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  # psr is the physical cpu
  ps -F -p <pid>
  ps -T -F -p <pid>
  ps -T -p 41869 -o pid,spid,psr,comm
  taskset -acp <pid>

Disable auto logout for CLI console
-------------------------------------

::

  # add to /etc/profile to persistent the setting
  export TMOUT=0

Command line calculation with bc
-----------------------------------

By default, bash does not support floating point calculation. For example, below expressions are not valid:

::

  # [[]] does not support floating point
  A=100.1
  B=100.1
  if [[ $A -eq $b ]]; then
    echo "Equal"
  fi

  # $(()) does not support floating point
  $((A + B))

To calculate floating point with bash, use bc as below:

::

  bc -l <<< "scale=10; $A == $B"
  bc <<< "scale=10; $A + $B"

Run a script automatically during system boot
------------------------------------------------

Previously, such tasks are achieved by leveraging rc.local, bash profile, etc. However, customized systemd service nowadays is much better for the same purpose.

1. Define a customized systemd service:

   - Create a plain text file under /etc/systemd/system as below, name it as route_add.service for example:

     ::

       [Unit]
       Description=Add customized ip routes
       After=network.service

       [Service]
       Type=oneshot
       ExecStart=/usr/local/bin/route_add.sh

       [Install]
       WantedBy=multi-user.target

   - Refer to manpage systemd.service and systemd.unit for the detailed explanations on each paramaters.

2. Create the actual script, such as /usr/local/bin/route_add.sh in our example, and assign exec permission with chmod a+x /usr/local/bin/route_add.sh
3. Enable and run it:

   ::

     systemctl enable route_add.service
     systemctl start route_add.service

Keep running a script in the background during system boot
-------------------------------------------------------------

A service Type can be defined as oneshot, simple, forking, etc. When it is needed to keep a script running in the background forever, **forking** can be leveraged as below.

::

  $ cat /opt/ycsb.sh
  #!/bin/bash

  (/usr/bin/screen -d -m /home/elk/ycsb-0.15.0/bin/ycsb run mongodb -s -P /home/elk/ycsb-0.15.0/workloads/workloada) &
  $ cat /etc/systemd/system/ycsb.service
  [Unit]
  Description=Start MongoDB Benchmarking
  After=mongodb.service

  [Service]
  Type=forking
  ExecStart=/opt/ycsb.sh

  [Install]
  WantedBy=multi-user.target

**Notes**: **fork** needs to be implemented by the app or the script to be executed.

Fork implementation with shell
---------------------------------

There are 2 x formats to achive forking with shell:

1. Through a function

   ::

     function abc() { xxx; xxx; ... }
     abc &

2. Through an anonymous function

   ::

     (xxx; xxx; ...) &

Redhat Linux vmcore Analyzing Getting Started
------------------------------------------------

::
  rpm -ivh crash-<version>.<platform>.rpm
  rpm -ivh kernel-debuginfo-<version>.<platform>.rpm kernel-debuginfo-common-<version>.<platform>.rpm
  crash /<absolute path to the system map file used for debug> /<path to the vmlinux used for debug>  /<path to the vmcore file>

Delete Character with Yast2
------------------------------

- Ctrl + H

Disable IPv6
---------------

- sysctl

  - Add below contents in /etc/sysctl.conf

    ::

      net.ipv6.conf.all.disable_ipv6 = 1
      net.ipv6.conf.default.disable_ipv6 = 1
      net.ipv6.conf.lo.disable_ipv6 = 1

  - sysctl -p
  - cat /proc/sys/net/ipv6/conf/all/disable_ipv6 ===> If output is 1, IPv6 has been disabled. If not, try reboot the server.
  - Delete the IPv6 localhost definition entry from /etc/hosts
  - Regenerate the initial ram disk (initrd) on RHEL/CentOS: "dracut -f"

- Grub: add "ipv6.disable=1" to the linux line

  ::

     linux   /boot/vmlinuz-xxx xxx xxx ipv6.disable=1

Recode file to UTF-8
-----------------------

- recode -f UTF-8 <file name>

- Get driver name

  ::

    [root@LPAR2 ~]# lspci -k
    …...
    f7:01.0 Ethernet controller: Intel Corporation 82576 Gigabit Network Connection (rev 01)
            Subsystem: Intel Corporation Device 0000
            Kernel driver in use: igb
            Kernel modules: igb

sudoers: <user> ALL = (<user to act as>) <commands>
------------------------------------------------------

::

  Examples:
    # User "alan" can run commands "/bin/ls" and "/bin/kill" as user "root", "bin" or group "operator", "system"
    alan   ALL = (root, bin : operator, system) /bin/ls, /bin/kill
    # User "superadm" can run all commands as anyone
    superadm  ALL=(ALL)   ALL
    # User "adm" can sudo run all "root"'s commands without password'
    adm ALL = (root) NOPASSWD:ALL
    # Users in group "wheel" can run all commands as anyone
    %wheel ALL=(ALL) ALL

Grub2 change boot order
--------------------------
::

  awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
  grub2-editenv list
  grub2-set-default 2
  grub2-editenv list

Disable console log
----------------------

::

  # dmesg -n 1

lsof tips
------------

- lsof <file> ---> Which processes are using the file
- lsof +D <directory> ---> Which processed are accessing the directory, and which files under the directory are being accessed

Delete trailing new line
---------------------------

::

  #tr -d '\n'

Change trailing new line to some other character
---------------------------------------------------

::

  #tr '\n' ','

Bash wait
------------

::

  While : ; do
      pids=""
      <process 1/command 1>  &
      pids="$pids $!"
      ……  &
      <process N/command N> &
      pids="$pids $!"
      for id in $pids; do
          wait $id
          echo $?
      done
  done

Use shell variable in sed
----------------------------

::

  sed -i -e "s/bindIp:.*$/bindIp: $IP_ADDR/" /etc/mongod.conf

Make grep match for only 1 time
----------------------------------

::

  # grep -m1 …...

Shell debugging
------------------

::

  #!/bin/bash -xv
  export PS4='+(${BASH_SOURCE}:${LINENO}):${FUNCNAME[0]:+${FUNCNAME[0]}(): }'
  --- OR ---
  set -o errexit == set -e
  set -o xtrace == set -x
  export PS4='+(${BASH_SOURCE}:${LINENO}):${FUNCNAME[0]:+${FUNCNAME[0]}(): }'

Regular Expression Comparision for sed/vim/awk/grep/etc.
-----------------------------------------------------------

::

  # txt2regex --showmeta

Print section between two regular expressions
------------------------------------------------

::

  # sed -n -e '/reg1/,/reg2/p' <file>

Delete broken links
----------------------

::

  find /etc/apache2 -type l **! -exec test -e {} \;** -print | sudo xargs rm

Find and sort by time
------------------------

::

  find . -type f -printf '%T@ %p\n' | sort -k 1 -n [-r]

Find files newer than
-------------------------

::

  find . -type f -newermt '2021-02-05'
  find -newermt "$(date '+%Y-%m-%d %H:%M:%S' -d '10 minutes ago')"

Sort based on several fields
-------------------------------

sort -k <field 1 order> -k <field 2 ordr> ... [-n] [-r]

Single line for loop with background jobs
--------------------------------------------

::

  # & is enough, if &; is used, an error will be triggered
  # refer to https://unix.stackexchange.com/questions/91684/use-ampersand-in-single-line-bash-loop
  for((i=1;i<=255;i+=1)); do echo $i; /opt/app1 & done

String Contains in Bash
--------------------------

- Leverage Wildcard

  ::

    if [[ "$string" == *"$substring"*  ]]; then
      echo "'$string' contains '$substring'"
    else
      echo "'$string' does not contain '$substring'"
    done

- Leverage Regular Expression

  ::

    if [[ "$string" =~ $substring  ]]; then
      echo "'$string' contains '$substring'"
    else
      echo "'$string' does not contain '$substring'"
    fi

Tarball with xz
------------------

xz is a newer compression tool than gz, bz, bz2, etc. It delivers better compression ratio and performance.

::

  tar -cJf <archive.tar.xz> <files>

Check shared object/library dependencies
-------------------------------------------

::

  ldd <object or executable file>
  LD_DEBUG=libs ldd <object or executable file>

Check object/executable file information
-------------------------------------------

- objdump: display info for object files
- nm: list symbols from object files
- pahole: show data structures of object files(including running kernels)
- readelf: display info for ELF files
- ldd: print object dependencies

::

  # Disamble
  objdump -S <ELF file>
  # Display dynamic symbol tables
  objdump -T <ELF file>
  readelf --dyn-syms <ELF file>
  # Show dynamic dependencies
  readelf -d <ELF file> | grep -i need
  ldd <ELF file>
  # show struct task_struct of running kernel
  pahole task_struct

systemcall summary
--------------------

::

  # get a summary of all system calls
  man syscalls
  # get system call convention, including registers used for syscall parameters
  man syscall

Record and replay linux CMD screen
-------------------------------------

::

  script --timing=file.tm script.out

  cmd1
  cmd2
  ...
  exit

  scriptreplay --timing file.tm --typescript script.out

Check nfs IO stat
--------------------

::

  nfsstat -l

zsh tips
-----------

Common
~~~~~~~~~

- zsh reference card: http://www.bash2zsh.com/zsh_refcard/refcard.pdf
- zsh tips: http://grml.org/zsh/zsh-lovers.html

zsh set/unset options
~~~~~~~~~~~~~~~~~~~~~~~~

::

  setopt # Display all enabled options
  setopt HIST_IGNORE_ALL_DUPS
  unsetopt # Display all off options
  unsetopt HIST_IGNORE_ALL_DUPS

Development Tools on different distros
-----------------------------------------

- Arch

  ::

    sudo pacman -S base-devel

- Ubuntu

  ::

    sudo apt-get install build-essential

- RHEL/CentOS

  ::

    sudo yum groupinstall "Development Tools"

- SuSE

  ::

    sudo zypper install -t pattern devel_C_C++

Assign hostname dynamically with DHCP
----------------------------------------

1. **option host-name** can be used to assign a hostname while assigning IP - https://www.isc.org/wp-content/uploads/2017/08/dhcp41options.html;
2. **dhcp-eval** can be leveraged to generate a hostname dynamically - https://www.isc.org/wp-content/uploads/2017/08/dhcp41eval.html.

Delete VM on Linux with virsh
--------------------------------

::

  virsh list
  virsh dumpxml VM_NAME | grep 'source file'
  # OR as below
  # virsh dumpxml --domain VM_NAME | grep 'source file'
  # <source file='/nfswheel/kvm/VM_NAME.qcow2'/>
  virsh shutdown VM_NAME
  # OR as below
  # virsh destroy VM_NAME
  virsh snapshot-list VM_NAME
  virsh snapshot-delete VM_NAME
  virsh undefine VM_NAME
  rm -rf <VM source file>

Configure IP with netctl on Arch
-----------------------------------

1. Create profiles

   ::

     cd /etc/netctl
     cp examples/ethernet-static ethernet-ensXXX
     cp examples/ethernet-dhcp ethernet-ensYYY
     # Modify ethernet-ensXXX ethernet-ensYYY

2. Disable NetworkManager

   ::

     systemctl stop NetworkManage
     systemctl disable NetworkManage

3. Enable profiles

   ::

     netctl enable ethernet-ensXXX
     netctl enable ethernet-ensYYY

4. Start profiles

   ::

     netctl start ethernet-ensXXX
     netctl start ethernet-ensYYY

5. Reenable profiles: after changing a profile, it must be re-enable

   ::

     netctl reenable profile

Change System Clock
----------------------

timedatectl is a new utility, which comes as a part of systemd system and service manager, a replacement for old traditional date command used in sysvinit daemon.

::

  timedatectl list-timezones
  timedatectl set-timezone Asia/Shanghai

Change System Locale
-----------------------

::

  localectl --help

Show CPU Summary
---------------------

Show CPU architecture, features, sockers, cores, etc.

::

  lscpu


Use openssl to download a certificate
-----------------------------------------

::

  openssl s_client -showcerts -connect <IP or FQDN>:<Port> </dev/null 2>/dev/null | openssl x509 -outform PEM > ca.pem

Setup CA with OpenSSL
-------------------------

This tip only lists the most important commands for easy reference. For more information, refer to the `original doc <https://gist.github.com/soarez/9688998>`_.

**Applicant Part:**

- Generate an RSA private key for CA:

  ::

    openssl genrsa -out example.org.key 2048

- Inspect the key:

  ::

    openssl rsa -in example.org.key -noout -text

- Extract RSA public key from the private key:

  ::

    openssl rsa -in example.org.key -pubout -out example.org.pubkey
    openssl rsa -in example.org.pubkey -pubin -noout -text

- Generate a CSR (Certificate Signing Request):

  ::

    openssl req -new -key example.org.key -out example.org.csr
    openssl req -in example.org.csr -noout -text

**CA Part:**

- Generate a private key for the root CA:

  ::

    openssl genrsa -out ca.key 2048

- Generate a self signed certificate for the CA:

  ::

    openssl req -new -x509 -key ca.key -out ca.crt

- Sign the applicant CSR to generate a certificate:

  ::

    openssl x509 -req -in example.org.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out example.org.crt
    openssl x509 -in example.org.crt -noout -text

- Verify the serial number assigned:

  ::

    cat ca.srl
    openssl x509 -in example.org.crt -noout -text | grep 'Serial Number' -A1

- Verify the certificate:

  ::

    openssl verify -CAfile ca.crt example.org.crt

ipmitool
------------

- Get system status

  ::

    # IPMI interface will either lan or lanplus
    ipmitool -I lanplus -H 192.168.10.10 -U admin -P password chassis status

- Power Ops

  ::

    ipmitool -I lanplus -H 192.168.10.10 -U admin -P password power <on|off|soft|reset>

- Change boot order

  ::

    ipmitool -I lanplus -H 192.168.10.10 -U admin -P password chassis bootdev <bios|pxe|cdrom|...>

- Reset IPMI controller

  ::

    ipmitool -I lanplus -H 192.168.10.10 -U admin -P password mc reset [warm|cold]

- Create a console connection

  ::

    ipmitool -I lanplus -H 192.168.10.10 -U admin -P password sol activate

SLES HA Cluster
-------------------

Cluster Environment:

 - node1: 192.168.10.10 (eth0)
 - node2: 192.168.10.20 (eth0)
 - Virtual IP: 192.168.10.30
 - Shared disks:

   * /dev/mapper/mpatha
   * /dev/mapper/mpathb

Steps:

- Update /etc/hosts

  ::

    192.168.10.10 node1
    192.168.10.20 node2

- Setup NTP: refer to "Use Chrony for time sync" within the same document
- Setup the software Watchdog (softdog)

  ::

    echo softdog > /etc/modules-load.d/watchdog.conf
    echo softdog > /etc/modules-load.d/watchdog.conf
    systemctl restart systemd-modules-load
    lsmod | grep softdog

- Init HA cluster from node1

  ::

    ha-cluster-init -u -i eth0 -s /dev/mapper/mpatha
    crm status

- Join the HA cluster from node2

  ::

    ha-cluster-join -c node1
    crm status

- Check the configuration

  ::

    # All the configuration is recorded within CIB (/var/lib/pacemaker/cib/cib.xml)
    # Command "cibadmin -Q" can be used to show the raw xml contents
    crm configure show

- Adjust SBD options

  ::

    # Add below line into /etc/sysconfig/sbd
    SBD_OPTS="-W"

- Adjust SBD options for multipathing device

  ::

    sbd -d /dev/mapper/mpatha -4 180 -1 90 create

- Restart the cluster to apply the changes

  ::

    crm cluster stop
    crm cluster start
    crm status
    sbd -d /dev/mapper/mpatha list

- Make sure below packages are installed before going further

  ::

    zypper search -s dlm-kmp
    zypper install dlm-kmp-default
    zypper search -s ocfs2-kmp
    zypper install ocfs2-kmp-default
    reboot
    # Select the associated kernel during boot!

- Create OCFS2 Volumes

  ::

    mkfs.ocfs2 -N 2 /dev/mapper/mpathb

- Mount:

  * Manual mount:

    ::

      mkdir /mnt/mpathb
      mount.ocfs2 /dev/mapper/mpathb /mnt/mpathb
      umount /mnt/mpathb

  * Automatic mount through crm(recommended):

    * GUI:

      + Access SuSE Hawk for cluster admin with default account hacluster/linux: https://192.168.10.<10|20|30>:7630
      + Create OCFS2 cluster resource by following: Hawk -> Configuration -> Wizards -> File System -> OCFS2 File System

    * CLI:

      ::

        crm configure
        primitive dlm ocf:pacemaker:controld
            op start timeout=90
            op stop timeout=60

        group g-dlm dlm

        clone c-dlm g-dlm meta interleave=true

        primitive mpathj ocf:heartbeat:Filesystem
            directory="/mnt/perf"
            fstype="ocfs2"
            device="/dev/mapper/mpathb"
            op start timeout=60s
            op stop timeout=60s
            op monitor interval=20s timeout=40s
        modgroup g-dlm add mpathb
        exit
        crm configure show
        crm status

- Frequently used commands

  * Interactive: crm [|configure|mon|resource|etc.]
  * Show current status: crm_mon -1
  * List resources: crm resource list
  * Start/stop/restart: crm resource start/stop/restart <resource name>
  * Clears the failure counter and re-checks the resource state: crm resource cleanup <resource name>
  * Delete a resource: crm configure show; crm configure delete <resource name>; crm configure show

Check initramfs contents
----------------------------

::

  lsinitrd <initrd image>

Caculate the size of hugepage used by a specified process
--------------------------------------------------------------

::

  # say the huge page size is 2M
  grep -B 11 'KernelPageSize:     2048 kB' /proc/[PID]/smaps | grep "^Size:" | awk 'BEGIN{sum=0}{sum+=$2}END{print sum/1024}'

Caculate used huge pages of a system
--------------------------------------

::

  # say the huge page size is 2M
  nr=`cat /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages`
  free=`cat /sys/kernel/mm/hugepages/hugepages-2048kB/free_hugepages`
  used=$((nr - free))
  echo $((used*2))M;
  echo $((used*2/1024))G

Make a process run on spcified cpu cores
-------------------------------------------

::

  # query current affinity
  taskset -cp <pid>
  # change the affinity
  taskset -cp <cpu cores, such as 1,2,3> <pid>

Get a random int within a range
---------------------------------

::

  N=$(shuf -i 1-100 -n 1)
  echo $N

Create an array based on command output
------------------------------------------

::

  a1=( $(ps -T -o pid,tid,psr,comm -p `pgrep -f 92e50bee-568d-4cc9-ad5a-617a6eb8206e` | grep CPU | awk '{print $2}' ) )
  echo ${a[*]}

Show cpu and cache topology
-----------------------------

::

  # Install hwloc and hwloc-gui at first
  lstopo-no-graphics --no-io --no-legend --of txt

Create a samba server
----------------------

#. samba, samba-client needs to be installed at first
#. Create dirs

   ::

     mkdir -p /samba/private
     mkdir -p /samba/public

#. Create users

   ::

     groupadd smbgrp
     useradd user1 # private access
     usermod -aG smbgrp user1
     smbpasswd -a user1
     usermod -aG smbgrp nobody # public access with nobody

#. Change dir access permissions

   ::

     chgrp smbgrp /samba/private
     chown nobody.smbgrp /samba/public

#. Samba server configuration

   ::

     # /etc/samba/smb.conf - delete original contents
     [global]
     workgroup = WORKGROUP
     security = user
     map to guest = bad user
     wins support = no
     dns proxy = no

     [public]
     path = /samba/public
     guest ok = yes
     force user = nobody
     browsable = yes
     writable = yes

     [private]
     path = /samba/private
     valid users = @smbgrp
     guest ok = no
     browsable = yes
     writable = yes

#. Restart service

   ::

     systemctl restart smb
     systemctl restart nmb

#. Done

Disks
========

List all SCSI devices
------------------------

**sg_map** can be used to list all devices support SCSI, such as sd, sr, st, etc. In the meanwhile, it can also list the well known host:bus:scsi:lun inforamtion as lsscsi.

Note: sg stands for generic SCSI driver, it is generalized (but lower level) than its siblings(sd, sr, etc.) and tends to be used on SCSI devices that don't fit into the already serviced categories. When the type for a SCSI device cannot be recognized, it will be shown as a sg device.

::

  # sg_map -x
  /dev/sg0  1 0 0 0  5  /dev/sr0
  /dev/sg1  2 0 0 0  0  /dev/sda

**lsblk** can also help list quite some information about block devices:

::

  # List SCSI devices
  lsblk -S
  # Show topology information
  lsblk -Tt
  # Show devices and associated file system information
  lsblk -f
  # Show device paths
  lsblk -p

Create a LV with all free space
----------------------------------

::

  lvcreate -l 100%FREE -n <LV name> <VG name>

Parted
---------

- fdisk cannot create partitions larger than 2TB, parted should be used under such situation.
- Select a target disk for partitioning: parted->print devices->select
- Create a partition: mklabel->unit->mkpart
- **Notes** : if error "Warning: The resulting partition is not properly aligned for best performance." is hit, you could use mkpart primary 0% 100% , this will align the disk automatically for you.

sg_inq/sg3_inq
-----------------

::

  # sg_inq -p 0 /dev/<device name>
   Only hex output supported. sg_vpd decodes more pages.
  VPD INQUIRY, page code=0x00:
     [PQual=0  Peripheral device type: disk]
     Supported VPD pages:
       0x0        Supported VPD pages
       0x80       Unit serial number
       0x83       Device identification
       0x8f       Third party copy
       0xb0       Block limits (sbc2)
       0xb1       Block device characteristics (sbc3)
       0xb2       Logical block provisioning (sbc3)
  # sg_inq -p 0x83 /dev/<device name>

Rescan/discover LUN/disk without reboot
------------------------------------------

- FC

  ::

    # find . -name "scan"
    # echo '- - -' > ./devices/pci0000:00/0000:00:07.1/ata1/host0/scsi_host/host0/scan
    ---OR---
    # echo '- - -' > /sys/class/scsi_host/host0/scan
    …
    # lsblk

- iSCSI

  ::

      iscsiadm -m session
      iscsiadm -m session --sid=<session ID> --rescan
      # or rescan all sessions
      iscsiadm -m session --rescan

Remove a SCSI/SAN disk when it is dead
-----------------------------------------

::

  ~$ sudo lsscsi
  [0:2:0:0]    disk    Lenovo   720i             4.23  /dev/sda
  [0:2:1:0]    disk    Lenovo   720i             4.23  /dev/sdb
  [0:2:2:0]    disk    Lenovo   720i             4.23  /dev/sdc
  [0:2:3:0]    disk    Lenovo   720i             4.23  /dev/sdd
  [1:0:0:0]    disk    Single   Flash Reader     1.00  /dev/sde
  [4:0:0:0]    cd/dvd  PLDS     DVD-RW DU8A5SH   BL61  /dev/sr0
  [14:0:1:0]   disk    DGC      LUNZ             4100  /dev/sdf

  ~$ echo 1 | sudo tee /sys/bus/scsi/devices/${H:B:T:L}/delete
  (Note: H:B:T:L is the bus address output of lsscsi for sdf)

  ~$ sudo lsscsi
  [0:2:0:0]    disk    Lenovo   720i             4.23  /dev/sda
  [0:2:1:0]    disk    Lenovo   720i             4.23  /dev/sdb
  [0:2:2:0]    disk    Lenovo   720i             4.23  /dev/sdc
  [0:2:3:0]    disk    Lenovo   720i             4.23  /dev/sdd
  [1:0:0:0]    disk    Single   Flash Reader     1.00  /dev/sde
  [4:0:0:0]    cd/dvd  PLDS     DVD-RW DU8A5SH   BL61  /dev/sr0

View/Create/Remove SCSI Persistent Reservation Keys
------------------------------------------------------

Refer to https://access.redhat.com/solutions/43402

Tool needed - sg3_utils
~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  yum install sg3_utils

View registered keys
~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --in -k -d /dev/<DEVICE>

View the reservations
~~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --in -r -d /dev/<DEVICE>

View more info about keys
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --in -s -d /dev/<DEVICE>

Register a key
~~~~~~~~~~~~~~~~~

::

  sg_persist --out --register --param-sark=<KEY> /dev/<DEVICE>

Take out a reservation
~~~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --out --reserve --param-rk=<KEY> --prout-type=<TYPE> /dev/<DEVICE>

Release a reservation
~~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --out --release --param-rk=<KEY> --prout-type=<TYPE> /dev/<DEVICE>

Unregister a key
~~~~~~~~~~~~~~~~~~~

::

  sg_persist --out --register --param-rk=<KEY> /dev/<DEVICE>

Clear the reservation and all registered keys
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  sg_persist --out --clear --param-rk=<KEY> /dev/<DEVICE>

A simple script to clear all reservations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  #!/usr/bin/bash

  DEVICE=$1

  KEYS=`sg_persist --in -k -d $DEVICE | grep '^ \+0x' | awk '{print $1}' | uniq`

  for k in $KEYS; do
    sg_persist --out --clear --param-rk=${k} ${DEVICE}
  done
