---
cover: ../.gitbook/assets/Mikrotik_PPPoE.png
coverY: 0
---

# Mikrotik Use Cases - PPPoE, ECMP, Failover, Recursive Routing and WireGuard

Mikrotik's RouterOS is a very versatile platform for all sorts of possible network setup scenarios in both carrier and enterprise environments. As long as the appropriate Mikrotik harward devices are used in the design, the capability of its platform is unlimited unlike other networking giants. It is even getting better with the latest and greatest version of its RouterOS 7.\*. Although the syntax can be a bit painful to adapt for the Mikrotik CLI veterans, it is still worth it to get the latest features like WireGuard, ZeroTier, etc., on the receiving end. For instance, most of the new Mikrotik platforms lately come with ARM 32 bit CPU architecture to have more advanced features like containerisation in the box. It is quite ambitious to implement all the bleeding edge features in the RouterOS but I quite appreciate their adventurous spirit of "can do" attitude towards the very old fashioned industry of networking.&#x20;

In this article, I have been setting up a GNS3 lab to experiment its RouterOS 7.7 features as well as an attempt to keep my rusty networking skills to be refreshed. There are a few use cases I have been wanting to test on this lab as listed below.

* PPPoE server and client configuration
* ECMP configuration for multi-WAN connections
* Failover WAN
* Recursive routing in simple setup
* WireGuard setup for site-to-site VPN

## Prerequisites&#x20;

* GNS3 Emulator
* Mikrotik CHR appliance setup on GNS3&#x20;
* Mikrotik RouterOS version 7.7
* Basic level of comfortableness with Mikrotik RouterOS CLI and GNS3 setup
* Basic understanding of VPN tunneling and WireGuard in general

## &#x20;Network Topology

<figure><img src="../.gitbook/assets/Mikrotik_PPPoE.png" alt=""><figcaption><p>Mikrotik Use Cases - PPPoE, ECMP, Recursive Routing and WireGuard</p></figcaption></figure>

* One core router "core" to simulate the internet backbone/core connected to NAT1 for the internet breakout connection.
* Two ISP routers "isp1" and "isp2" to simulate the ISP's PPPoE servers on the providers' end.
* Three customer routers "mtr1", "mtr2" and "mtr3" to simulate the enterprise customers' end. "mtr2" is especially used for ECMP and Recursive Routing since it is connected to both "isp1" and "isp2" routers.&#x20;
* Behind each mtr router, it has various LAN subnets for PCs.

### Configuration

#### Core router config

Here is the full configuration and its description of "core" router

```
# Assign the static IP addresses for both downstream isp1 and isp2 routers
/ip address
add address=123.1.1.1/30 interface=ether2 network=123.1.1.0
add address=123.1.1.5/30 interface=ether3 network=123.1.1.4

# Configure ether1 as dhcp-client interface (default)
/ip dhcp-client
add interface=ether1

# Configure masquerade NAT firewall rule for the internet breakout
/ip firewall nat
add action=masquerade chain=srcnat out-interface=ether1

# Configure the static routes to both isp1's and isp2's downstream links towards end customers
/ip route
add dst-address=121.0.0.0/24 gateway=123.1.1.2
add dst-address=122.0.0.0/24 gateway=123.1.1.6

# Set the system name to core
/system identity
set name=core
```

#### ISP routers config&#x20;

Both isp1 and isp2 routers config and its description can be found in the below code snippets.

Here is how I configure the isp1 router.&#x20;

```
# Create a new bridge "pppoe-bridge" to serve multiple customers
/interface bridge
add name=pppoe-bridge

# Create an IP pool for PPPoE server
/ip pool
add name=pppoe-pool ranges=121.0.0.2-121.0.0.254

# Set ppp profile for PPPoE server for its required parameters
/ppp profile
add local-address=121.0.0.1 name=pppoe-server remote-address=pppoe-pool

# Add the ports ether2 and ether3 to newly created pppoe-bridge
/interface bridge port
add bridge=pppoe-bridge interface=ether2
add bridge=pppoe-bridge interface=ether3

# Configure pppoe-server interface with its profile and bridge interface 
/interface pppoe-server server
add default-profile=pppoe-server disabled=no interface=pppoe-bridge service-name=pppoe-service

# Configure the static IP address to ether1 for upstream connection
/ip address
add address=123.1.1.2/30 interface=ether1 network=123.1.1.0

# Disable the default dhcp-client config on ether1
/ip dhcp-client
add disabled=yes interface=ether1

# Configure masquerade NAT firewall rule for the internet breakout
/ip firewall nat
add action=masquerade chain=srcnat out-interface=ether1

# Configure the static route to upstream core router
/ip route
add dst-address=0.0.0.0/0 gateway=123.1.1.1

# Create two PPPoE user accounts associating to its relevant PPPoE server profile
/ppp secret
add name=isp1user1 password=isp1user1 profile=pppoe-server service=pppoe
add name=isp1user2 password=isp1user2 profile=pppoe-server service=pppoe

# Set system name to isp1
/system identity
set name=isp1
```

Here is how I configure the isp2 router.&#x20;

```
# Create a new bridge "pppoe-bridge" to serve multiple customers
/interface bridge
add name=pppoe-bridge

# Create an IP pool for PPPoE server
/ip pool
add name=pppoe-pool ranges=122.0.0.2-122.0.0.254

# Set ppp profile for PPPoE server for its required parameters
/ppp profile
add local-address=122.0.0.1 name=pppoe-server remote-address=pppoe-pool

# Add the ports ether2 and ether3 to newly created pppoe-bridge
/interface bridge port
add bridge=pppoe-bridge interface=ether2
add bridge=pppoe-bridge interface=ether3

# Configure pppoe-server interface with its profile and bridge interface 
/interface pppoe-server server
add default-profile=pppoe-server disabled=no interface=pppoe-bridge service-name=pppoe-service

# Configure the static IP address to ether1 for upstream connection
/ip address
add address=123.1.1.6/30 interface=ether1 network=123.1.1.4

# Disable the default dhcp-client config on ether1
/ip dhcp-client
add disabled=yes interface=ether1

# Configure masquerade NAT firewall rule for the internet breakout
/ip firewall nat
add action=masquerade chain=srcnat out-interface=ether1

# Configure the static route to upstream core router
/ip route
add dst-address=0.0.0.0/0 gateway=123.1.1.5

# Create two PPPoE user accounts associating to its relevant PPPoE server profile
/ppp secret
add name=isp2user1 password=isp2user1 profile=pppoe-server service=pppoe
add name=isp2user2 password=isp2user2 profile=pppoe-server service=pppoe

# Set system name to isp1
/system identity
set name=isp2
```

#### Customer routers config&#x20;

Note that both mtr1 and mtr3 configs are very similar but mtr2 is configured quite differently since it is connected to both isp1 and isp2 routers for upstream internet connection.&#x20;

Here is the full mtr1 router configuration wtih PPPoE Client and WireGuard site-to-site VPN setup.

```
# Create a new bridge "lan-bridge" to serve the customer LAN
/interface bridge
add name=lan-bridge

# Configure PPPoE-client with its assigned useranme and password
# Note that add-default-route parameter is set to yes which creates a dynamic default route
/interface pppoe-client
add add-default-route=yes disabled=no interface=ether1 name=pppoe-out \
    service-name=pppoe-service user=isp1user1 password=isp1user1

# Configure WireGuard interface 
/interface wireguard
add listen-port=13231 mtu=1420 name=wireguard1

# Create an IP Pool for DHCP server to serve its LAN
/ip pool
add name=dhcp1 ranges=192.168.1.200-192.168.1.254

# Configure the DHCP server
/ip dhcp-server
add address-pool=dhcp1 interface=lan-bridge name=dhcp1

# Add ether6,7 and 8 port to lan-bridge
/interface bridge port
add bridge=lan-bridge interface=ether6
add bridge=lan-bridge interface=ether7
add bridge=lan-bridge interface=ether8

# Configure WireGuard peers mtr3 to initiate the VPN tunnel
/interface wireguard peers
add allowed-address=10.0.0.2/32,192.168.3.0/24 endpoint-address=122.0.0.253 \
    endpoint-port=13231 persistent-keepalive=25s interface=wireguard1 \ 
    public-key="wFKr1GSkYTcXLLeyAnlG3Qe1c3TLqycB9Lwkgqbu3DM="

# Assign IP addresses for lan-bridge and WireGuard interfaces
/ip address
add address=192.168.1.1/24 interface=lan-bridge network=192.168.1.0
add address=10.0.0.1/30 interface=wireguard1 network=10.0.0.0

# Disable dhcp-client on ether1 (default)
/ip dhcp-client
add disabled=yes interface=ether1

# Configure DHCP server network paramenters
/ip dhcp-server network
add address=192.168.1.0/24 dns-server=1.1.1.1 gateway=192.168.1.1

# Configure masquerade NAT firewall rule for the internet breakout
/ip firewall nat
add action=masquerade chain=srcnat out-interface=pppoe-out

# Configure a static route to mtr3's LAN network thru wireguard1 interface
/ip route
add dst-address=192.168.3.0/24 gateway=wireguard1

# Set system name to mtr1
/system identity
set name=mtr1
```

A couple of facts about mtr1 router setup - it uses the PPPoE accounts isp1user1 created on isp1 router and it has been assumed to have a dynamic IP address assigned by its ISP so it has to initiate the WireGuard VPN connection to the other end mtr3 which has a static IP address assigned by its ISP. Note that the allowed-address is used to allow the WireGuard interface IP and LAN IP range on the other side of its tunnel. The endpoint-address is for the public IP address of mtr3.&#x20;

Here is the full mtr2 router configuration wtih ECMP for multi-home PPPoE connections.

```
# Create a new bridge "lan-bridge" to serve the customer LAN
/interface bridge
add name=lan-bridge

# Configure both PPPoE connections with its assgined PPPoE accounts 
# Note that both PPPoE client configs have add-default-route set to yes which creates 
# dynamic default routes with same distance value. ECMP is in effect by this stage.
/interface pppoe-client
add add-default-route=yes disabled=no interface=ether1 name=pppoe-out \
    service-name=pppoe-service user=isp1user2 password=isp1user2
add add-default-route=yes disabled=no interface=ether2 name=pppoe-out1 \
    service-name=pppoe-service user=isp2user2 password=isp2user2

# Create an IP Pool for DHCP server to serve its LAN
/ip pool
add name=dhcp1 ranges=192.168.2.200-192.168.2.254

# Configure the DHCP server
/ip dhcp-server
add address-pool=dhcp1 interface=lan-bridge name=dhcp1

# Add ether6,7 and 8 port to lan-bridge
/interface bridge port
add bridge=lan-bridge interface=ether6
add bridge=lan-bridge interface=ether7
add bridge=lan-bridge interface=ether8

# Assign IP addresses for lan-bridge interface
/ip address
add address=192.168.2.1/24 interface=lan-bridge network=192.168.2.0

# Disable dhcp-client on ether1 (default)
/ip dhcp-client
add disabled=yes interface=ether1

# Configure DHCP server network paramenters
/ip dhcp-server network
add address=192.168.2.0/24 dns-server=1.0.0.1 gateway=192.168.2.1

# Configure masquerade NAT firewall rules for the internet breakouts
/ip firewall nat
add action=masquerade chain=srcnat out-interface=pppoe-out
add action=masquerade chain=srcnat out-interface=pppoe-out1

# Set system name to mtr2
/system identity
set name=mtr2
```

Instead of ECMP, mtr2 can be also configured as failover WAN connection between two PPPoE connections as below.&#x20;

```
# Configure both PPPoE connections with its assgined PPPoE accounts 
# Note that both PPPoE client configs don't have add-default-route set to yes which 
# requires to create the static routes to each PPPoE connection with different distances.
/interface pppoe-client
add disabled=no interface=ether1 name=pppoe-out service-name=pppoe-service user=isp1user2 password=isp1user2
add disabled=no interface=ether2 name=pppoe-out1 service-name=pppoe-service user=isp2user2 password=isp2user2

# Set isp1 as the primary default gateway and isp2 as the secondary for failover WAN
/ip route
add disabled=no dst-address=0.0.0.0/0 gateway=pppoe-out
add disabled=no distance=2 dst-address=0.0.0.0/0 gateway=pppoe-out1
```

For recursive routing, ensure that mtr2 is pingable to Google 8.8.8.8 and Cloudflare 1.1.1.1 first then start configuring the routes as below.&#x20;

```
# Configure both PPPoE connections with its assgined PPPoE accounts 
# Note that both PPPoE client configs don't have add-default-route set to yes which 
# requires to create the static routes to each PPPoE connection with different distances.
/interface pppoe-client
add disabled=no interface=ether1 name=pppoe-out service-name=pppoe-service user=isp1user2 password=isp1user2
add disabled=no interface=ether2 name=pppoe-out1 service-name=pppoe-service user=isp2user2 password=isp2user2

# Configure recursive routing with 8.8.8.8 and 1.1.1.1 on both PPPoE connections
/ip route
add check-gateway=ping dst-address=0.0.0.0/0 gateway=1.1.1.1 target-scope=11
add check-gateway=ping distance=2 dst-address=0.0.0.0/0 gateway=8.8.8.8 target-scope=11
add check-gateway=none dst-address=1.1.1.1 gateway=pppoe-out scope=10
add check-gateway=none dst-address=8.8.8.8 gateway=pppoe-out1 scope=10
```

The recursive routing is very useful when both of its immediate links to upstream from mtr2 are up but the reachability to the internet is broken due to disconnect or malfunction at the links between core and isp routers. Note that check-gateway parameter is set to ping for the first two routes for ping reachability check on each link.&#x20;

Here is how I configure mtr3 for its PPPoE client configuration and site-to-site WireGuard VPN conection.&#x20;

```
# Create a new bridge "lan-bridge" to serve the customer LAN
/interface bridge
add name=lan-bridge

# Configure PPPoE-client with its assigned useranme and password
# Note that add-default-route parameter is set to yes which creates a dynamic default route
/interface pppoe-client
add add-default-route=yes disabled=no interface=ether1 name=pppoe-out \
    service-name=pppoe-service user=isp2user1 password=isp2user1

# Configure WireGuard interface 
/interface wireguard
add listen-port=13231 mtu=1420 name=wireguard1

# Create an IP Pool for DHCP server to serve its LAN
/ip pool
add name=dhcp1 ranges=192.168.3.200-192.168.3.254

# Configure the DHCP server
/ip dhcp-server
add address-pool=dhcp1 interface=lan-bridge name=dhcp1

# Add ether6,7 and 8 port to lan-bridge
/interface bridge port
add bridge=lan-bridge interface=ether6
add bridge=lan-bridge interface=ether7
add bridge=lan-bridge interface=ether8

# Configure WireGuard peers mtr3 to initiate the VPN tunnel
# Note that it doesn't have endpoint address and port configured
/interface wireguard peers
add allowed-address=10.0.0.1/32,192.168.1.0/24 interface=wireguard1 \
    persistent-keepalive=25s public-key="c/RL12ckSwJWK/LN8IrgP+lP6Yggb6hhczY4jztKfE8="

# Assign IP addresses for lan-bridge and WireGuard interfaces
/ip address
add address=192.168.3.1/24 interface=lan-bridge network=192.168.3.0
add address=10.0.0.2/30 interface=wireguard1 network=10.0.0.0

# Disable dhcp-client on ether1 (default)
/ip dhcp-client
add disabled=yes interface=ether1

# Configure DHCP server network paramenters
/ip dhcp-server network
add address=192.168.3.0/24 dns-server=8.8.8.8 gateway=192.168.3.1

# Configure masquerade NAT firewall rule for the internet breakout
/ip firewall nat
add action=masquerade chain=srcnat out-interface=pppoe-out

# Configure a static route to mtr1's LAN network thru wireguard1 interface
/ip route
add dst-address=192.168.1.0/24 gateway=wireguard1

# Set system name to mtr3
/system identity
set name=mtr3
```

With the configs on both mtr1 and mtr3, LAN from each side should have the connectivity to the other side via WireGuard site-to-site VPN tunnel.&#x20;
