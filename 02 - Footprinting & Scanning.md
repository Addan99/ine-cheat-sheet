
###
-c / -n: number of packet (-c linux / -n windows)
x.x.x.0: ping all range
###
ping -c 1 192.168.0.1
ping -c 1 192.168.0.0


###
-sn: no port scan (show only online hosts)
-PS: TCP SYN scan on port 80 (default) we can specify port -PS443,80-100
-T4: faster execution (use with caution)
-sS: TCP SYN scan (stealth scan)
-sV: Service version detection
-O: detect os system
--osscan-guest: force detection os system (-O --osscan-guest)
-sC: apply scripts
--script=: apply specific sceipt /usr/share/nmap/script | grep -e "mongo"
###
## tcp scan
nmap -sn -PS21,22,25,80,445,3389,8080 -T4 192.168.0.0/24

## udp scan common port 250
nmap -sU -sV -p1-250 demo.ine.local
nmap demo.ine.local -p 134 -sUV --script=discovery

### for undetermined port try
tftp demo.ine.local 134 

## detect firewall 
nmap -sA -p[open ports]: if result is unfiltred: no firewall, filtred:firewall active


## firewall evasion
-f --data-length 200: fragmented package for firewall evasion
-D 192.168.0.1: falsify origin Ip


## smb
nmap -p- -sS -sV demo.ine.local
  Host is up (0.0028s latency).
  PORT    STATE SERVICEVERSION
  135/tcp  open msrpc Microsoft Windows RPC
  139/tcp  open netbios-ssn Microsoft Windows netbios-ssn
  445/tcp  open microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds

nmap -p445 --script smb-enum-* --script-args smbusername=administrator,smbpassword=smbserver_771   demo.ine.local  

## (Optinal) manual connection to smb 
    smbclient //demo.ine.local/ADMIN$ -U administrator --password=smbserver_771 
