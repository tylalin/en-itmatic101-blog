---
cover: ../.gitbook/assets/mikrotik_vlan_topology.png
coverY: 0
---

# Mikrotik VLAN Trunking

VLAN (Virtual Local Area Network) is not something new and unique to Mikrotik networking devices. It is an amazing technology we have to segregate the different LANs on Layer 2 switching. Different vendors implement the VLAN and VLAN trunking technology differently but all follows the same 802.1q standard for tagging and untagging VLANs on their devices.&#x20;

In this article, I would like to demostrate the VLAN capability on Mikrotik and how we can configure it easily in RouterOS. Note that most of the Mikrotik networking devices come with dedicated switch chip on RouterBoard to perform hardware offloading for more efficient switching at Layer 2 rather than passing the loads to main CPU. Since the Mikrotik device comes with RouterOS can configured as router with sub-interface for VLAN tagging which you would see it commonly in router-on-a-stick scenario, and switch with proper VLAN tagging and untagging to utilise the dedicated switch chip on RouterBoard.&#x20;

## Prerequisites&#x20;

* GNS3 Emulator
* Mikrotik CHR appliance setup on GNS3&#x20;
* Mikrotik RouterOS version 7.7
* Basic level of comfortableness with Mikrotik RouterOS CLI and GNS3 setup

## &#x20;Network Topology

<figure><img src="../.gitbook/assets/mikrotik_vlan_topology.png" alt=""><figcaption><p>Mikrotik VLANs</p></figcaption></figure>

* Two swtiches - mtr2 and mtr3; each has two VLANs namely ops1 and ops2
* One router - mtr1 which is bridging between two switches with routing capability for internet breakout to NAT1 cloud&#x20;

## Configuration

