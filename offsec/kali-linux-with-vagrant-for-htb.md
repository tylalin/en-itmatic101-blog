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

- VirtualBox installation - https://www.virtualbox.org/wiki/Downloads
- Vagrant installation - https://developer.hashicorp.com/vagrant/install
- Visual Studio Code (vscode) - https://code.visualstudio.com/docs/setup/setup-overview
- Willingness to learn a bit of Vagrant's Ruby and Ansible's YAML syntax
- Basic Linux Sysadmin knowledge and skills

## Starting Point

```ruby
# -*- mode: ruby -*- 
# vi: set ft=ruby :

OS = "rolling"
BOX_IMAGE = "kalilinux/#{OS}"
NODE_COUNT = 1

Vagrant.configure("2") do |config|
  (1..NODE_COUNT).each do |i|     
    config.vm.define "#{OS}-#{i}" do |subconfig|       
      subconfig.vm.box = BOX_IMAGE       
      subconfig.vm.hostname = "#{OS}-#{i}"
      subconfig.vm.network :private_network, ip: "192.168.56.#{i + 100}"     
    end
  end 
  config.vm.provision "ansible_local" do |a|
    a.playbook = "playbook.yml"
  end  
end
```