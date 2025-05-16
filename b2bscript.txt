#!/bin/bash

# The architecture of your operating system and its kernel version.
echo -n "#Architecture: "; uname -a

# The number of physical processors.
echo -n "#CPU physical : "
lscpu | grep "^Socket(s):" | awk '{print $2}'

# The number of virtual processors.
echo -n "#vCPU : "; nproc

# The current available RAM on your server and its utilization rate as a percentage.
echo -n "#Memory Usage: "
free --mega | grep "Mem:" | awk '{printf "%dMB/%dMB (%.2f%%)\n", int($3), int($2), int($3 / $2 * 100)}'

# The current available storage on your server and its utilization rate as a percentage.
echo -n "#Disk Usage: "
df -h --total | grep "total" | awk '{print $3"B/"$2"B ("$5")"}'

# The current utilization rate of your processors as a percentage.
echo -n "#CPU load: "
mpstat | grep "PM" | awk 'NR==2{printf "%.1f%%\n", (100 - $13)}'

# The date and time of the last reboot.
echo -n "#Last boot: "
who -b | grep "boot" | awk '{print $3, $4}'

# Whether LVM is active or not.
echo -n "#LVM use: "
lvdisplay | grep -m1 "LV Status" | awk '{if ($3 == "available") print "YES"; else print "NO"}'

# The number of active connections.
estab_count=$(ss --summary | grep -o "estab [0-9]*" | cut -d' ' -f2)
estab_result="#Connections TCP : $estab_count ESTABLISHED"
echo "$estab_result"

# The number of users using the server.
echo -n "#User log: "
who | awk '{print $1}' | sort -u | wc -l

# The IPv4 address of your server and its MAC (Media Access Control) address.
ip_address=$(ip -4 a | grep inet | grep -v "127.0.0.1" | awk 'NR==1{print $2}' | cut -d '/' -f1)
mac_address=$(ip a | grep ether | awk 'NR==1{print "("$2")"}')
echo "#Network: IP $ip_address $mac_address"

# The number of commands executed with sudo
cmd=$(grep -c 'COMMAND=' /var/log/sudo/sudo.log)
echo "#Sudo : $cmd cmd"

