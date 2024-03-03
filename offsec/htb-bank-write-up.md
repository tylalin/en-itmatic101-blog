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

### DNS

#### dig

With a standard DNS tool called 'dig' available in Linux, we can pull it off the following DNS recon on the target machine. 

```bash
# get the domain's zone details with dig from dns server 
$ dig @10.10.10.29 bank.htb axfr 

; <<>> DiG 9.19.17-1-Debian <<>> @10.10.10.29 bank.htb axfr
; (1 server found)
;; global options: +cmd
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
bank.htb.               604800  IN      NS      ns.bank.htb.
bank.htb.               604800  IN      A       10.10.10.29
ns.bank.htb.            604800  IN      A       10.10.10.29
www.bank.htb.           604800  IN      CNAME   bank.htb.
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
;; Query time: 504 msec
;; SERVER: 10.10.10.29#53(10.10.10.29) (TCP)
;; WHEN: Sun Nov 12 02:49:32 EST 2023
;; XFR size: 6 records (messages 1, bytes 171)

-------------------------------------------------------------------------------

# filter the output a bit more with grep regex
$ dig @10.10.10.29 bank.htb axfr | grep -E '(\w+\.)?\w+\.htb' 
; <<>> DiG 9.19.17-1-Debian <<>> @10.10.10.29 bank.htb axfr
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
bank.htb.               604800  IN      NS      ns.bank.htb.
bank.htb.               604800  IN      A       10.10.10.29
ns.bank.htb.            604800  IN      A       10.10.10.29
www.bank.htb.           604800  IN      CNAME   bank.htb.
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800

-------------------------------------------------------------------------------

# print only the matches with grep -o
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' 
bank.htb
bank.htb
bank.htb
chris.bank.htb
bank.htb
ns.bank.htb
bank.htb
ns.bank.htb
www.bank.htb
bank.htb
bank.htb
bank.htb
chris.bank.htb

-------------------------------------------------------------------------------

# sort and print only unique with sort -u 
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u
bank.htb
chris.bank.htb
ns.bank.htb
www.bank.htb

-------------------------------------------------------------------------------

# translate or replace newline \n with space ' '
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u | tr '\n' ' '
bank.htb chris.bank.htb ns.bank.htb www.bank.htb 

-------------------------------------------------------------------------------

# manipulate the output the way we want with awk
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u | tr '\n' ' ' | awk '{print "10.10.10.29\t" $1 " " $2 " " $3 " " $4}'
10.10.10.29     bank.htb chris.bank.htb ns.bank.htb www.bank.htb

-------------------------------------------------------------------------------

# copy the output to clipboard with xclip instead of print out
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u | tr '\n' ' ' | awk '{print "10.10.10.29\t" $1 " " $2 " " $3 " " $4}' | xclip -selection clipboard

```

The series of commands demonstrate the process of retrieving domain zone details using the `dig` command from a DNS server, filtering the output with `grep`, `sort`, and `awk`, and finally copying the manipulated output to the clipboard using `xclip`. Here's an explanation of each step:

1. **Original `dig` Command**:
   - The initial command requests a zone transfer (`axfr`) for the domain `bank.htb` from the DNS server located at `10.10.10.29`.

2. **Filtered Output with `grep`**:
   - `grep -E '(\w+\.)?\w+\.htb'`: Filters the output to include only lines containing domain names with the `.htb` extension.

3. **Print Matches Only with `grep -o`**:
   - `grep -oE '(\w+\.)?\w+\.htb'`: Prints only the matched domain names, one per line.

4. **Sort and Print Unique Entries with `sort -u`**:
   - `sort -u`: Sorts the domain names alphabetically and prints only unique entries.

5. **Translate Newlines to Spaces with `tr`**:
   - `tr '\n' ' '`: Translates newline characters to spaces, combining all domain names into a single line.

6. **Manipulate Output with `awk`**:
   - `awk '{print "10.10.10.29\t" $1 " " $2 " " $3 " " $4}'`: Formats the output to include the DNS server IP address (`10.10.10.29`) followed by the domain names.

7. **Copy Output to Clipboard with `xclip`**:
   - `xclip -selection clipboard`: Copies the manipulated output to the clipboard for easy pasting into other applications.

Overall, this series of commands provides a streamlined way to retrieve domain zone details from a DNS server, filter and manipulate the output, and then copy it to the clipboard for further use. This can be particularly useful for network administrators or security professionals conducting DNS-related investigations or audits.

To make my life a bit easier and work with DNS, I add the following entry to my local Kali machine's /etc/hosts file. 

```bash
$ sudo vi /etc/hosts 
10.10.10.29     bank.htb chris.bank.htb ns.bank.htb www.bank.htb
```

### Web
#### WhatWeb

Since the Nmap scan result shows that the TCP port 80 HTTP is opening, I would like to confirm its operating system and version.

```bash
$ whatweb http://10.10.10.29                                       
http://10.10.10.29 [200 OK] Apache[2.4.7], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[10.10.10.29], Title[Apache2 Ubuntu Default Page: It works]
```

It is a command-line tool called WhatWeb, which is used for web fingerprinting or identifying the technologies used by a website. Here's an analysis of the output:

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

WhatWeb analysis reveals that the target website is hosted on a server running Apache version 2.4.7 on Ubuntu Linux. The default page for Apache on Ubuntu is being served, indicating that the web server is operational and accessible.

#### Aquatone

```bash
# download aquatone from github
$ wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip

# unzip the zip file
$ unzip aquatone_linux_amd64_1.7.0.zip

# get the oupt to bank.urls for aquatone
$ dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u > bank.urls

# it looks like this
$ cat bank.urls 
bank.htb
chris.bank.htb
ns.bank.htb
www.bank.htb

# use sed to substitute or put http:// prefix to every line
$ sed -i 's/^/http:\/\//gi' bank.urls

# verify the sed output
$ cat bank.urls 
http://bank.htb
http://chris.bank.htb
http://ns.bank.htb
http://www.bank.htb

# feed it to aquaton
$ cat bank.urls | ./aquatone 
aquatone v1.7.0 started at 2023-11-12T04:24:36-05:00

Using unreliable Google Chrome for screenshots. Install Chromium for better results.

Targets    : 4
Threads    : 2
Ports      : 80, 443, 8000, 8080, 8443
Output dir : .

http://ns.bank.htb: 200 OK
http://www.bank.htb: 200 OK
http://bank.htb: 200 OK
http://chris.bank.htb: 200 OK
http://ns.bank.htb: screenshot successful
http://www.bank.htb: screenshot successful
http://bank.htb: screenshot successful
http://chris.bank.htb: screenshot successful
Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2023-11-12T04:24:36-05:00
 - Finished at : 2023-11-12T04:24:43-05:00
 - Duration    : 7s

Requests:
 - Successful : 4
 - Failed     : 0

 - 2xx : 4
 - 3xx : 0
 - 4xx : 0
 - 5xx : 0

Screenshots:
 - Successful : 4
 - Failed     : 0

Wrote HTML report to: aquatone_report.html

# open aquatone_report.html with firefox
$ firefox aquatone_report.html&

# feh is a light-weight image viewer to png screenshots
# install feh with apt so that we can view the screenshots of aquatone outputs
$ sudo apt install feh

```

It illustrates the process of using Aquatone, a reconnaissance tool, to capture screenshots and generate reports for a list of domain URLs. Here's a breakdown of each step:

1. **Download Aquatone**:
   - Fetches the Aquatone release from GitHub using `wget`.
   ```
   wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
   ```

2. **Unzip Aquatone**:
   - Extracts the downloaded Aquatone zip file using `unzip`.
   ```
   unzip aquatone_linux_amd64_1.7.0.zip
   ```

3. **Retrieve Domain URLs with `dig`**:
   - Performs a zone transfer for the domain `bank.htb` from the DNS server at `10.10.10.29`, filters and sorts unique domain names, and saves them to a file named `bank.urls`.
   ```
   dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u > bank.urls
   ```

4. **Prepend `http://` to URLs with `sed`**:
   - Adds `http://` prefix to each line in `bank.urls`.
   ```
   sed -i 's/^/http:\/\//gi' bank.urls
   ```

5. **Feed URLs to Aquatone**:
   - Pipes the contents of `bank.urls` to Aquatone for scanning.
   ```
   cat bank.urls | ./aquatone
   ```

6. **Open Aquatone HTML Report**:
   - Opens the generated HTML report in a web browser (Firefox) for review.
   ```
   firefox aquatone_report.html&
   ```

7. **View Screenshots with `feh`**:
   - If desired, use the `feh` image viewer to browse the screenshots captured by Aquatone.
   ```
   sudo apt install feh
   ```

These commands demonstrate a streamlined process for using Aquatone to perform reconnaissance on a list of domain URLs, capturing screenshots and generating reports for further analysis.

#### Gobuster

```bash
# web scan with gobuster 
$ gobuster dir --threads 100 --wordlist /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt --url http://bank.htb 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://bank.htb
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 305] [--> http://bank.htb/uploads/]
/assets               (Status: 301) [Size: 304] [--> http://bank.htb/assets/]
/inc                  (Status: 301) [Size: 301] [--> http://bank.htb/inc/]
/server-status        (Status: 403) [Size: 288]
/balance-transfer     (Status: 301) [Size: 314] [--> http://bank.htb/balance-transfer/]
Progress: 220560 / 220561 (100.00%)
===============================================================
Finished
===============================================================
```

The output displays the results of a web scan conducted with Gobuster, a popular directory and file brute-forcing tool. Here's an explanation of the scan:

1. **Tool Information**:
   - Gobuster version 3.6 is being used for the scan. Gobuster is a tool used to brute-force: URIs (directories and files) in web sites, DNS subdomains (with wildcard support), Virtual Host names on target web servers, Open Amazon S3 buckets, Open Google Cloud buckets and TFTP servers. Gobuster is useful for pentesters, ethical hackers and forensics experts. It also can be used for security tests.

2. **Scan Configuration**:
   - **URL**: http://bank.htb
   - **Method**: GET
   - **Threads**: 100
   - **Wordlist**: /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
   - **Negative Status codes**: 404 (ignore 404 Not Found responses)
   - **User Agent**: gobuster/3.6
   - **Timeout**: 10 seconds

3. **Results**:
   - The scan is performed in directory enumeration mode.
   - Directories or paths found during the scan:
     - **/uploads**: Redirects to http://bank.htb/uploads/
     - **/assets**: Redirects to http://bank.htb/assets/
     - **/inc**: Redirects to http://bank.htb/inc/
     - **/server-status**: Access forbidden (Status: 403 Forbidden)
     - **/balance-transfer**: Redirects to http://bank.htb/balance-transfer/

Gobuster successfully identified several directories and paths on the target website http://bank.htb, along with their corresponding status codes and sizes. This information can be valuable for further reconnaissance and vulnerability assessment, allowing security professionals to explore potentially sensitive areas of the web application. Additionally, the presence of directories like "uploads," "assets," and "balance-transfer" may provide avenues for further investigation or exploitation.


## Exploits

