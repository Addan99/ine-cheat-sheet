## metasploit
## first run postgres
service postgresql start
/etc/init.d/postgresql start

## create xml
nmap -Pn  demo.ine.local -oX <file-xml>.xml 

## run metaxploid
msfconsole 

## create workspace
workspace -a <name-workspace>

## use workspace
workspace  <name-workspace>

## import xml file
db_import <file-xml>.xml 

## use nmap to add more info
db_nmap

## hosts
## services
## vulnr

## module
search <modulo_name>  (search portscan)
use <name/index>
show optios
set <option> <value>
run

## meterpreter
shell
bin/bash -i

ip_target_2: search ip target 2
run autoroute -s <ip_target_2>  // decapred
run post/multi/manage/autoroute SUBNET=192.19.122.0 NETMASK=255.255.255.0


## glovar variable
setg RHOSTS <IP>


Find the default tcp ports used by smbd.
    Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-22 19:30 IST
    Nmap scan report for demo.ine.local (192.231.27.3)
    Host is up (0.000028s latency).
    Not shown: 998 closed tcp ports (reset)
    PORT    STATE SERVICE     VERSION
    139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)
    445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)
    MAC Address: 02:42:C0:E7:1B:03 (Unknown)
    Service Info: Host: SAMBA-RECON


Find the default udp ports used by nmbd.
    nmap -sU -p137,138 -sV  demo.ine.local 
    Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-22 19:39 IST
    Nmap scan report for demo.ine.local (192.231.27.3)
    Host is up (0.000055s latency).

    PORT    STATE         SERVICE     VERSION
    137/udp open          netbios-ns  Samba nmbd netbios-ns (workgroup: RECONLABS)
    138/udp open|filtered netbios-dgm
    MAC Address: 02:42:C0:E7:1B:03 (Unknown)
    Service Info: Host: SAMBA-RECON


What is the workgroup name of samba server?
    RECONLABS


Find the exact version of samba server by using appropriate nmap script.
    | smb-os-discovery: 
    |   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
    |   Computer name: demo
    |   NetBIOS computer name: SAMBA-RECON\x00
    |   Domain name: ine.local
    |   FQDN: demo.ine.local
    |_  System time: 2026-02-22T14:07:35+00:00

Find the exact version of samba server by using smb_version metasploit module.
    Module options (auxiliary/scanner/smb/smb_version):

    Name     Current Setting  Required  Description
    ----     ---------------  --------  -----------
    RHOSTS   demo.ine.local   yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
    RPORT                     no        The target port (TCP)
    THREADS  1                yes       The number of concurrent threads (max one per host)


    msf6 auxiliary(scanner/smb/smb_version) > run
        [*] 192.231.27.3:445      - SMB Detected (versions:1, 2, 3) (preferred dialect:SMB 3.1.1) (compression capabilities:) (encryption capabilities:AES-128-CCM) (signatures:optional) (guid:{626d6173-2d61-6572-636f-6e0000000000}) (authentication domain:SAMBA-RECON)
        [*] 192.231.27.3:445      -   Host could not be identified: Windows 6.1 (Samba 4.3.11-Ubuntu)
        [*] demo.ine.local:       - Scanned 1 of 1 hosts (100% complete)
        [*] Auxiliary module execution completed


What is the NetBIOS computer name of samba server? Use appropriate nmap scripts.
    nmap -sU -p137 -sV --script=smb-system-info  demo.ine.local                         
        PORT    STATE SERVICE    VERSION
        137/udp open  netbios-ns Samba nmbd netbios-ns (workgroup: RECONLABS)
        MAC Address: 02:42:C0:E7:1B:03 (Unknown)
        Service Info: Host: SAMBA-RECON


Find the NetBIOS computer name of samba server using nmblookup
    nmblookup -A demo.ine.local                                                         
    Looking up status of 192.231.27.3
            SAMBA-RECON     <00> -         H <ACTIVE> 
            SAMBA-RECON     <03> -         H <ACTIVE> 
            SAMBA-RECON     <20> -         H <ACTIVE> 
            ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE> 
            RECONLABS       <00> - <GROUP> H <ACTIVE> 
            RECONLABS       <1d> -         H <ACTIVE> 
            RECONLABS       <1e> - <GROUP> H <ACTIVE> 

            MAC Address = 00-00-00-00-00-00


Using smbclient determine whether anonymous connection (null session) is allowed on the samba server or not.
    smbclient -L //192.231.27.3/ -N                                                     

            Sharename       Type      Comment
            ---------       ----      -------
            public          Disk      
            john            Disk      
            aisha           Disk      
            emma            Disk      
            everyone        Disk      
            IPC$            IPC       IPC Service (samba.recon.lab)
    Reconnecting with SMB1 for workgroup listing.

            Server               Comment
            ---------            -------

            Workgroup            Master
            ---------            -------
            RECONLABS            SAMBA-RECON


Using rpcclient determine whether anonymous connection (null session) is allowed on the samba server or not.
    rpcclient  192.231.27.3 -N -U ""                                                    
    rpcclient $> ls

auxiliary/scanner/http/apache_userdir_enum
auxiliary/scanner/http/brute_dirs
auxiliary/scanner/http/dir_scanner
auxiliary/scanner/http/dir_listing
auxiliary/scanner/http/http_put
auxiliary/scanner/http/files_dir
auxiliary/scanner/http/http_login | bob 123321
auxiliary/scanner/http/http_header
auxiliary/scanner/http/http_version  | Apache/2.4.18 
auxiliary/scanner/http/robots_txt  | /secure /data


auxiliary/scanner/mysql/mysql_version
auxiliary/scanner/mysql/mysql_login | brute force
auxiliary/admin/mysql/mysql_enum | info
auxiliary/admin/mysql/mysql_sql
auxiliary/scanner/mysql/mysql_file_enum
auxiliary/scanner/mysql/mysql_hashdump | extract users hash
auxiliary/scanner/mysql/mysql_schemadump | db schema
auxiliary/scanner/mysql/mysql_writable_dirs | enumerate dir path  | set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt



auxiliary/scanner/ssh/ssh_version
auxiliary/scanner/ssh/ssh_login


## SMTP
What is the SMTP server name and banner.
    telnet demo.ine.local 25
    SERVER: Postfix
    Banner: openmailbox.xyz
Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).
    nc demo.ine.local 25
Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.
    vrfy admin
    252 2.0.0 admin


Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.
    vrfy commander
    550 5.1.1 <commander>: Recipient address rejected: User unknown in local recipient table


What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.
    telnet demo.ine.local 25
    HELO attacker.xyz
    EHLO attacker.xyz
How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.
    smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local
        existse.local: admin
        existse.local: administrator
        existse.local: mail
        existse.local: postmaster
        existse.local: root
        existse.local: sales
        existse.local: support
        demo.ine.local: www-data

How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.
    _apt, admin, administrator, backup, bin, daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postfix, postmaster, proxy, sync, sys, uucp, www-data

Connect to SMTP service using telnet and send a fake mail to root user.
    telnet demo.ine.local 25
        HELO attacker.xyz
        mail from: admin@attacker.xyz
        rcpt to:root@openmailbox.xyz
        data
        Subject: Hi Root
        Hello,
        This is a fake mail sent using telnet command.
        From,
        Admin
        .

Send a fake mail to root user using sendemail command.
    sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no