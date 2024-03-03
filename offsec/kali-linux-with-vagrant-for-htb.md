---
cover: >-
  https://images.unsplash.com/photo-1549605659-32d82da3a059?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw0fHxrYWxpJTIwTGludXh8ZW58MHx8fHwxNzA5NDQxNzUxfDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Kali Linux with Vagrant and Ansible for HTB

## Why?

Ever since I embarked on setting up my Offensive Security (OffSec) lab environment, I foresaw the need to streamline the process in case of mishaps with my Kali Linux VM in VirtualBox. And indeed, there were a few instances where experimentation led to unintended consequences. To mitigate this, I resolved to automate the setup every time I needed to reset or modify the Kali Linux VM.

Given my familiarity with Vagrant for managing virtual environments, it made perfect sense to leverage it for setting up the Kali Linux VM in VirtualBox. I stumbled upon the Kali Linux Vagrant box available on HashiCorp's Vagrant Cloud site, accessible at https://app.vagrantup.com/kalilinux/boxes/rolling. For provisioning, I opted for Ansible without hesitation, confident in its ability to prepare the VM for my OffSec endeavors.

## Prerequisites
