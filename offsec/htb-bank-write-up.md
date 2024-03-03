---
cover: >-
  https://images.unsplash.com/photo-1571840615771-acc2e9f42641?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw4fHxiYW5rfGVufDB8fHx8MTcwMjA4MTg3OXww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# HTB: Bank Write-Up

## Recons
### nmap

Let's sniff the target machine to check if there is any opening to break in. 

```bash
# Nmap 7.94 scan initiated Sun Nov 12 02:18:04 2023 as: nmap -vvv -Pn -sCV --open -T4 -p0-65535 -oN bank.nmap 10.10.10.29
Nmap scan report for 10.10.10.29
Host is up, received user-set (0.33s latency).
Scanned at 2023-11-12 02:18:04 EST for 84s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAMJ+YATka9wvs0FTz8iNWs6uCiLqSFhmBYoYAorFpozVGkCkU1aEJ7biybFTw/qzS9pbSsaYA+3LyUyvh3BSPGEt1BgGW/H29MuXjkznwVz60JqL4GqaJzYSL3smYYdr3KdJQI/QSvf34WU3pife6LRmJaVk+ETh3wPclyecNtedAAAAFQC1Zb2O2LzvAWf20FdsK8HRPlrx1wAAAIBIBAhLmVd3Tz+o+6Oz39g4Um1le8d3DETINWk3myRvPw8hcnRwAFe1+14h3RX4fr+LKXoR/tYrI138PJyiyl+YtQWhZnJ7j8lqnKRU2YibtnUc44kP9FhUqeAcBNjj4qwG9GyQSWm/Q5CbOokgaa6WfdcnwsUMim0h2Ad8YdU1kAAAAIBy3dOOD8jKHeBdE/oXGG0X9tKSFZv1gPr/kZ7NfqUF0kHU3oZTNK8/2qR0SNHgrZ2cLgKTIuneGS8lauXjC66NNMoUkJcMHpwRkYC0A86LDmhES6OuPsQwAjr1AtUZn97QjYu1d6WPfhTdsRYBuCotgKh2SBkzV1Bcz77Tnp56JA==
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDc0rofjHtpSlqkDjjnkEiYcbUrMH0Q4a6PcxqsR3updDGBWu/RK7AGWRSjPn13uil/nl44XF/fkULy7FoXXskByLCHP8FS2gYJApQMvI9n81ERojEA0NIi6VZKP19bl1VFTk7Q5rEPIpab2xqYMBayb1ch7iP95n3iayvHEt/7cSTsddGWKeALi+rrujpnryNViiOIWpqDv+RWtbc2Wuc/FTeGSOt1LBTbtKcLwEehBG+Ym8o8iKTd+zfVudu7v1g3W2Aa3zLuTcePRKLUK3Q2D7k+5aJnWrekpiARQm3NmMkv1NuDLeW3amVBCv6DRJPBqEgSeGMGsnqkR8CKHO9/
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDH30xnPq1XEub/UFQ2KoHXh9LFKMNMkt60xYF3OrEp1Y5XQd0QyeLXwm6tIqWtb0rWda/ivDgmiB4GzCIMf/HQ=
|   256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIA8MYjFyo+4OwYGTzeuyNd998y6cOx56mIuciim1cvKh
53/tcp open  domain  syn-ack ttl 63 ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.7 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.7 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Nov 12 02:19:28 2023 -- 1 IP address (1 host up) scanned in 83.86 seconds
```

The provided Nmap scan result reveals information about a target host with the IP address 10.10.10.29. Here's a breakdown of the findings:

1. **SSH Service (Port 22)**:
   - State: Open
   - Service: SSH (Secure Shell)
   - Version: OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8
   - Encryption Key Types:
     - DSA (Digital Signature Algorithm)
     - RSA (Rivest-Shamir-Adleman)
     - ECDSA (Elliptic Curve Digital Signature Algorithm)
     - ED25519 (Edwards-curve Digital Signature Algorithm)
   - Operating System: Ubuntu Linux
   
   This indicates that the SSH service is available on the host, allowing secure remote access. The version information can be useful for identifying potential vulnerabilities or compatibility issues.

2. **Domain Service (Port 53)**:
   - State: Open
   - Service: Domain (DNS - Domain Name System)
   - Version: ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
   
   The Domain service being open suggests that this host is likely responsible for DNS resolution within the network. The version information can help in assessing potential vulnerabilities or compatibility with other DNS systems.

3. **HTTP Service (Port 80)**:
   - State: Open
   - Service: HTTP (Hypertext Transfer Protocol)
   - Version: Apache httpd 2.4.7 (Ubuntu)
   - Server Header: Apache/2.4.7 (Ubuntu)
   - HTTP Methods Supported: POST, OPTIONS, GET, HEAD
   - HTTP Title: Apache2 Ubuntu Default Page: It works
   
   The HTTP service being open indicates that the host is running a web server, serving content over the HTTP protocol. The version information can be valuable for understanding potential vulnerabilities or compatibility issues with web applications running on this server.

4. **Additional Information**:
   - Latency: The host responded with a latency of 0.33 seconds, indicating a relatively quick response time.
   - Operating System: Detected as Linux, with the Common Platform Enumeration (CPE) specifying the Linux kernel.

This Nmap scan provides valuable insights into the services running on the target host, allowing for further analysis and potential identification of security risks or areas of interest for further investigation.

### whatweb

Since the Nmap scan result shows that the TCP port 80 HTTP is opening, I would like to confirm its operating system and version.

```bash
whatweb http://10.10.10.29                                       
http://10.10.10.29 [200 OK] Apache[2.4.7], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[10.10.10.29], Title[Apache2 Ubuntu Default Page: It works]
```

The provided output is from a command-line tool called WhatWeb, which is used for web fingerprinting or identifying the technologies used by a website. Here's an analysis of the output:

1. **URL**: http://10.10.10.29
   - This is the URL of the target website being analyzed.

2. **Response Status**: [200 OK]
   - Indicates that the web server responded with a successful HTTP status code, meaning the request was processed without errors.

3. **Web Server**: Apache [2.4.7]
   - Specifies the web server software being used, which in this case is Apache version 2.4.7. Apache is a widely used open-source web server software.

4. **Country**: RESERVED [ZZ]
   - The country field typically provides information about the geographical location of the server based on its IP address. However, in this case, it shows "RESERVED" with the country code "ZZ", which suggests that the country information is not available or reserved.

5. **HTTP Server**: Ubuntu Linux [Apache/2.4.7 (Ubuntu)]
   - Indicates the underlying operating system and its version, which is Ubuntu Linux. Additionally, it mentions that Apache version 2.4.7 is specifically configured for Ubuntu.

6. **IP Address**: 10.10.10.29
   - Specifies the IP address of the target server.

7. **Title**: Apache2 Ubuntu Default Page: It works
   - Provides the title of the webpage, which is the default page served by Apache on an Ubuntu system. This is often displayed when no specific content is configured for the root URL of the web server.

In summary, the WhatWeb analysis reveals that the target website is hosted on a server running Apache version 2.4.7 on Ubuntu Linux. The default page for Apache on Ubuntu is being served, indicating that the web server is operational and accessible.

### DNS

To make my life a bit easier and work with DNS, I add the following entry to my local Kali machine's /etc/hosts file. 

```bash
sudo vi /etc/hosts 
10.10.10.29 bank.htb
```

#### dig


## Exploits

