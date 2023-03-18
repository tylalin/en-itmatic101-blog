# Mikrotik RouterOS Hardening for your home internet connection

The price point of Mikrotik hAP devices are quite reasonable and fair compared to any major off the shefl brands like Netgear, TP-Link, D-Link, etc., Therefore, I have been deploying Mikrotik devices for friends and family for home internet connection. It is a kind of overkill to manage the devices remotely but there are many ways to do so. The [Mikrotik Pro](https://play.google.com/store/apps/details?id=com.mikrotik.android.tikapp\&hl=en\&gl=US\&pli=1) mobile app is one of the easy ways to remote access and administer them.&#x20;

Over the past couple of years, I have been researching and experimenting on how to secure the Mikrotik devices for the normal home users. So here is the best configuration I can think of to harden the Mikrotik hAP devices for home internet connections. I know that Mikrotik hAP devices come with default configuration out of the box to use but I guess it is not good enough.&#x20;

Here is the full configuraiton of Mikrotik hAP device with its hardening parts in it.&#x20;

```
# Create LAN side bridge interface 
/interface bridge
add name=lan

# Name the ether1 as wan
/interface ethernet
set [ find default-name=ether1 ] name=wan

# Create WireGuard interface for remote access to the site
/interface wireguard
add listen-port=13231 name=wireguard1

# Create lists for LAN and WAN so that it can referenced easily in other parts.
/interface list
add name=WAN
add name=LAN

# Create a wireless security profile for wifi SSID
/interface wireless security-profiles
add authentication-types=wpa-psk,wpa2-psk management-protection=allowed \
    mode=dynamic-keys name=wifi wpa2-pre-shared-key="wifi-password"

# Configure the wifi SSID and its associated security profile
# Note that there are a few configs that need to be noticed
# First, antenna-gain is set to 0 for more efficient and long range wifi connection
# Second, the SSID is hidden for better security
# Third, installation is set to indoor and country is set to australia
/interface wireless
set [ find default-name=wlan1 ] antenna-gain=0 band=2ghz-g/n channel-width=20/40mhz-XX \
    country=australia disabled=no frequency=auto hide-ssid=yes \
    installation=indoor mode=ap-bridge security-profile=wifi \
    ssid=2ghz-wifi wireless-protocol=802.11
set [ find default-name=wlan2 ] antenna-gain=0 band=5ghz-n/ac channel-width=20/40/80mhz-XXXX \
    country=australia disabled=no frequency=auto hide-ssid=yes \
    installation=indoor mode=ap-bridge security-profile=wifi \
    ssid=5ghz-wifi wireless-protocol=802.11
    
# Configure IP Pool and DHCP server for LAN side
/ip pool
add name=dhcp ranges=192.168.233.100-192.168.233.254
/ip dhcp-server
add address-pool=dhcp interface=lan name=dhcp1
/ip dhcp-server network
add address=192.168.233.0/24 dns-server=1.1.1.2,1.0.0.2 gateway=192.168.233.1 netmask=24

# Configure PPPoE-client to authenticate against ISP server
/interface pppoe-client
add add-default-route=yes disabled=no interface=wan name=pppoe-out1 \
    profile=default-encryption user=user1@goodisp.com.au password="passwd" \
    keepalive-timeout=10 add-default-route=yes default-route-distance=1 \
    dial-on-demand=no use-peer-dns=no allow=pap,chap,mschap1,mschap2
    
# Add relevant interfaces to lan bridge
/interface bridge port
add bridge=lan ingress-filtering=no interface=ether2
add bridge=lan ingress-filtering=no interface=ether3
add bridge=lan ingress-filtering=no interface=ether4
add bridge=lan ingress-filtering=no interface=ether5
add bridge=lan ingress-filtering=no interface=wlan2
add bridge=lan ingress-filtering=no interface=wlan1

# Add relevant interfaces to interface list
/interface list member
add interface=wan list=WAN
add interface=lan list=LAN
add interface=pppoe-out1 list=WAN

# Configure WireGuard peers for the remote user
/interface wireguard peers
add allowed-address=172.16.1.2/30 endpoint-port=13231 interface=wireguard1 \
    persistent-keepalive=25s public-key="gNJId0yhGvVnWljDvn6Q7htAekLcwp4kQkpBtrTw4jc="
    
# Configure interface IP addresses    
/ip address
add address=192.168.233.1/24 interface=lan network=192.168.233.0
add address=172.16.1.1/30 interface=wireguard1 network=172.16.1.0

# Disable default DHCP cleint config on ether1 or wan interface
/ip dhcp-client
add disabled=yes interface=wan

# Configure DNS 
/ip dns
set allow-remote-requests=yes servers=1.1.1.2,1.0.0.2

# Configure firewall filter rules
/ip firewall filter
add action=accept chain=input comment="defconf: accept established, related and untracked" \
    connection-state=established,related,untracked
add action=drop chain=input comment="defconf: drop invalid" connection-state=invalid \
    log-prefix=drop_forward_invalid
add action=drop chain=input comment="defconf: drop all not coming from LAN" \
    in-interface-list=!LAN log-prefix=drop_input_all src-address=!123.0.0.231
add action=fasttrack-connection chain=forward comment="defconf: fasttrack" \
    connection-state=established,related hw-offload=yes
add action=accept chain=forward comment="defconf: accept established, related and untracked" \
    connection-state=established,related,untracked
add action=drop chain=forward comment="defconf: drop invalid" \
    connection-state=invalid log-prefix=drop_forward_invalid
add action=drop chain=forward comment="defconf: drop all from WAN not DSTNATed" \
    connection-nat-state=!dstnat connection-state=new in-interface-list=WAN
   
# Configure source NAT masquerade for the internet breakout 
/ip firewall nat
add action=masquerade chain=srcnat out-interface=pppoe-out1 src-address=192.168.233.0/24

# Disable the service ports not in use
/ip firewall service-port
set ftp disabled=yes
set tftp disabled=yes
set h323 disabled=yes
set sip disabled=yes
set pptp disabled=yes

# Disable the ip services not in use and restrict the source addresses for ssh and winbox
/ip service
set telnet disabled=yes
set ftp disabled=yes
set www disabled=yes
set ssh address=192.168.233.0/24,123.0.0.231/32
set api disabled=yes
set winbox address=192.168.233.0/24,123.0.0.231/32
set api-ssl disabled=yes

# Set the correct timezone 
/system clock
set time-zone-autodetect=no time-zone-name=Australia/Darwin

# Set the system name
/system identity
set name=mtr01

# Set NTP client and configure with desired NTP servers IP
/system ntp client
set enabled=yes
/system ntp client servers
add address=129.250.35.250
add address=129.250.35.251

# To trigger a notification email upon startup in case of device rebooting
/system scheduler
add name=reboot_report on-event=":delay 60\r\
    \n/tool e-mail send to=tyla@email.com subject=\"mtr01: router has\
    \_been reboot at home!\" body=\"The mtr01 router has been r\
    eboot at home!\"" policy=\
    ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon \
    start-time=startup

# Disable bandwidth-server
/tool bandwidth-server
set enabled=no

# Configure SMTP server for emailing notification
/tool e-mail
set address=smtp.goodisp.com.au from=mtr01@email.com

# Restrict the layer2 MAC-server access from LAN interface list only
/tool mac-server
set allowed-interface-list=LAN
/tool mac-server mac-winbox
set allowed-interface-list=LAN

# Monitor the internal host on LAN side with PING status
/tool netwatch
add disabled=no down-script="/tool e-mail send to=tyla@email.com subject=\
    \"srv01: PING is down at home!\" body=\"The srv01 host's PING is down at home!\"" \
    host=192.168.233.233 http-codes="" interval=30m test-script="" timeout=5s type=icmp up-script=""
```
