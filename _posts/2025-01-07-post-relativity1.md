---
layout: post
title: Linux command in a nutshell
date: 2025-01-07 09:56:00-0400
description: Some linux commands that could ease your life
tags: linux
categories: data-science
giscus_comments: false
related_posts: false
related_publications: true
---

# List of linux commands that could save your life! 

## 1 – SYSTEM INFORMATION
- **Display Linux system information**:  
  `uname -a`

- **Display kernel release information**:  
  `uname -r`

- **Show which version of Redhat is installed**:  
  `cat /etc/redhat-release`

- **Show how long the system has been running and load average**:  
  `uptime`

- **Show system host name**:  
  `hostname`

- **Display the IP addresses of the host**:  
  `hostname -I`

- **Show system reboot history**:  
  `last reboot`

- **Show the current date and time**:  
  `date`

- **Show this month's calendar**:  
  `cal`

- **Display who is online**:  
  `w`

- **Who you are logged in as**:  
  `whoami`

---

## 2 – HARDWARE INFORMATION
- **Display messages in kernel ring buffer**:  
  `dmesg`

- **Display CPU information**:  
  `cat /proc/cpuinfo`

- **Display memory information**:  
  `cat /proc/meminfo`

- **Display free and used memory**:  
  `free -h`

- **Display PCI devices**:  
  `lspci -tv`

- **Display USB devices**:  
  `lsusb -tv`

- **Display DMI/SMBIOS (hardware info) from the BIOS**:  
  `dmidecode`

- **Show info about disk sda**:  
  `hdparm -i /dev/sda`

- **Perform a read speed test on disk sda**:  
  `hdparm -tT /dev/sda`

- **Test for unreadable blocks on disk sda**:  
  `badblocks -s /dev/sda`

---

## 3 – PERFORMANCE MONITORING AND STATISTICS
- **Display and manage the top processes**:  
  `top`

- **Interactive process viewer (top alternative)**:  
  `htop`

- **Display processor-related statistics**:  
  `mpstat 1`

- **Display virtual memory statistics**:  
  `vmstat 1`

- **Display I/O statistics**:  
  `iostat 1`

- **Display the last 100 syslog messages**:  
  `tail 100 /var/log/messages`

- **Capture and display all packets on interface eth0**:  
  `tcpdump -i eth0`

- **Monitor all traffic on port 80 (HTTP)**:  
  `tcpdump -i eth0 'port 80'`

- **List all open files on the system**:  
  `lsof`

- **List files opened by a user**:  
  `lsof -u user`

- **Display free and used memory**:  
  `free -h`

- **Execute "df -h", showing periodic updates**:  
  `watch df -h`

---

## 4 – USER INFORMATION AND MANAGEMENT
- **Display the user and group IDs of your current user**:  
  `id`

- **Display the last users who have logged onto the system**:  
  `last`

- **Show who is logged into the system**:  
  `who`

- **Show who is logged in and what they are doing**:  
  `w`

- **Create a group named "test"**:  
  `groupadd test`

- **Create a user account**:  
  `useradd -c "John Smith" -m john`

- **Delete a user account**:  
  `userdel john`

- **Add a user to a group**:  
  `usermod -aG sales john`

---

## 5 – FILE AND DIRECTORY COMMANDS
- **List all files in a long listing format**:  
  `ls -al`

- **Display the present working directory**:  
  `pwd`

- **Create a directory**:  
  `mkdir directory`

- **Remove a file**:  
  `rm file`

- **Remove a directory and its contents recursively**:  
  `rm -r directory`

- **Force removal of a file without prompting**:  
  `rm -f file`

- **Copy a file**:  
  `cp file1 file2`

- **Move or rename a file**:  
  `mv file1 file2`

- **Create a symbolic link**:  
  `ln -s /path/to/file linkname`

- **View the contents of a file**:  
  `cat file`

- **Browse a text file**:  
  `less file`

---

## 6 – PROCESS MANAGEMENT
- **Display your currently running processes**:  
  `ps`

- **Display all running processes**:  
  `ps -ef`

- **Kill a process by its ID**:  
  `kill pid`

- **Start a program in the background**:  
  `program &`

- **Bring a background job to the foreground**:  
  `fg`

---

## 7 – FILE PERMISSIONS
- **Change file permissions**:  
  `chmod 755 filename`

- **Recursive permission update**:  
  `chmod -R u+r files`

---

## 8 – NETWORKING
- **Display all network interfaces and IP addresses**:  
  `ifconfig -a`

- **Send ICMP echo requests to a host**:  
  `ping host`

- **Download a file from the internet**:  
  `wget http://domain.com/file`

---

## 9 – ARCHIVES (TAR FILES)
- **Create a tar archive**:  
  `tar cf archive.tar directory`

- **Extract a tar archive**:  
  `tar xf archive.tar`

---

## 10 – INSTALLING PACKAGES
- **Search for a package by keyword**:  
  `yum search keyword`

- **Install a package**:  
  `yum install package`

- **Install from a local file**:  
  `rpm -i package.rpm`

---

## 11 – SEARCH
- **Search for a pattern in a file**:  
  `grep pattern file`

- **Find files and directories by name**:  
  `locate name`

---

## 12 – SSH LOGINS
- **Connect to a host as your local username**:  
  `ssh host`

- **Generate an SSH key**:  
  `ssh-keygen -t rsa`

---

## 13 – FILE TRANSFERS
- **Securely copy a file**:  
  `scp file.txt server:/tmp`

- **Synchronize directories**:  
  `rsync -a /home /backups/`

---

## 14 – DISK USAGE
- **Show free and used space on mounted filesystems**:  
  `df -h`

- **Show disk usage for all files and directories**:  
  `du -ah`

---

## 15 – DIRECTORY NAVIGATION
- **Go to the parent directory**:  
  `cd ..`

- **Go to the home directory**:  
  `cd`

- **Change to a specific directory**:  
  `cd /etc`

---

## 16 – SCREEN
- **List all screens**:  
  `screen -ls`

- **Create a new screen**:  
  `screen -S <name>`

- **Detach from a screen**:  
  `ctrl + a + d`

- **Attach to a screen**:  
  `screen -r <name>`


