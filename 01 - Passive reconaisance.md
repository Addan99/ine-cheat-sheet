dnsenum
dig

-- for discovery Ip in range RANGE(192.168.x.0)
sudo nmap -sn RANGE/24

###
-p-: all port
-p 80,443: specific port
-p1-100: range port
-Pn: omitir blocking IP
-sU; UDP
-sV: version
-o: operative system
-sC: script nmap
-oN test.txt: output file text
-oX test.xml: ouput file xml
###
nmap IP -Pn


-- sudo apt-get install netdiscover -y
sudo netdicover -r 192.168.0.0/24
