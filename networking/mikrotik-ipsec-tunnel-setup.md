---
cover: ../.gitbook/assets/2023-02-16_07-30.png
coverY: 0
---

# Mikrotik IPsec Tunnel Setup

Although there are a few new and shiny VPN tunneling protocol like WireGuard, IPsec is still the king of enterprise grade for site-to-site VPN tunnteling. It is not as easy as WireGuard to setup on Mikrotik. Personally I like Mikrotik a lot of its RouterOS based on Linux and pricing model for all hardware. At least, it doesn't break my bank for all the features I want to work with.&#x20;

## Prerequisites&#x20;

* GNS3 Emulator
* Mikrotik CHR appliance setup on GNS3&#x20;
* Mikrotik RouterOS version 7.7
* Basic level of comfortableness with Mikrotik RouterOS CLI

## &#x20;Network Topology

<figure><img src="../.gitbook/assets/2023-02-16_07-30.png" alt=""><figcaption><p>Mikrotik IPsec Tunneling</p></figcaption></figure>

* Two sites - HQ and Coud. Each site has the IP address of 10.20.30.0/24 subnet as shown in the diagram.
* HQ has two VLANs - 100 for Dev team and 200 for OPS team at its site. Only Dev team can access to the resources in Cloud since it is not that cheap.
* Cloud has only one connected subnet of 172.31.1.0/24 where they host their Dev servers.
* Internet switch is the representation of the internet connection via NAT in GNS3.

