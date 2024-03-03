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

I have uploaded the source code of Vagrantfile and Ansible playbook.yml along with the required sample files on GitHub - https://github.com/tylalin/vagrant-kali

### Vagrantfile

Here is what my Vagrantfile looks like as show below.

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

The code is a Vagrantfile written in Ruby, used to configure and provision virtual machines (VMs) with Vagrant. Here's an explanation of each section:

1. **File Metadata**: 
   - `# -*- mode: ruby -*-` and `# vi: set ft=ruby :` are editor configuration comments. They indicate that the file should be treated as Ruby code by text editors like Emacs and Vim.

2. **Global Variables**:
   - `OS = "rolling"`: Specifies the operating system version. In this case, it's set to "rolling," indicating the rolling release version of Kali Linux.
   - `BOX_IMAGE = "kalilinux/#{OS}"`: Defines the Vagrant box image to be used. It's constructed dynamically based on the OS variable.
   - `NODE_COUNT = 1`: Sets the number of virtual machines to be created. In this case, it's set to create a single VM.

3. **Vagrant Configuration**:
   - `Vagrant.configure("2") do |config|`: Begins the Vagrant configuration block.
   - `(1..NODE_COUNT).each do |i|`: Loops through the range of 1 to NODE_COUNT (inclusive), creating multiple VMs if NODE_COUNT is greater than 1.
   - `config.vm.define "#{OS}-#{i}" do |subconfig|`: Defines a VM with a specific name based on the OS and index.
   - `subconfig.vm.box = BOX_IMAGE`: Specifies the Vagrant box image to use for the VM.
   - `subconfig.vm.hostname = "#{OS}-#{i}"`: Sets the hostname of the VM based on the OS and index.
   - `subconfig.vm.network :private_network, ip: "192.168.56.#{i + 100}"`: Configures a private network interface for the VM with a dynamically assigned IP address.

4. **Ansible Provisioning**:
   - `config.vm.provision "ansible_local" do |a|`: Configures Ansible provisioning to run locally on the VM.
   - `a.playbook = "playbook.yml"`: Specifies the Ansible playbook to be executed for provisioning. The playbook is named "playbook.yml".

This Vagrantfile sets up a single Kali Linux VM with a private network interface and provisions it using an Ansible playbook named "playbook.yml". The VM is named dynamically based on the OS and index specified in the global variables.

### Ansible (provisioner)

Following is what my Ansible playbook.yml file looks like. 

```yaml
---
- name: kali setup
  hosts: all
  become: true
  gather_facts: false
  
  tasks:
  - name: set timezone to Australia/Melbourne
    timezone:
      name: Australia/Melbourne

  - name: install tools
    apt:
      name: "{{ item }}"
      update_cache: true
    loop:
      - tmux        # enables a number of terminals (or windows) to be accessed and controlled from a single terminal like screen
      - feh         # fast, lightweight image viewer which uses imlib2
      - gobuster    # tool used to brute-force URIs including directories and files as well as DNS subdomains
      - nuclei      # fast, template based vulnerability scanner focusing on extensive configurability, massive extensibility and ease of use
      - dirsearch   # command-line tool designed to brute force directories and files in webservers
      - nishang     # framework and collection of scripts and payloads which enables usage of PowerShell for offensive security and post exploitation during Penetration Tests
      - seclists    # collection of multiple types of lists used during security assessments
      - steghide    # steganography program which hides bits of a data file in some of the least significant bits of another file in such a way that the existence of the data file is not visible and cannot be proven.
      - exiftool    # a free and open-source software program for reading, writing, and manipulating image, audio, video, and PDF metadata. 

  - name: copy ssh pubkey to remote 
    authorized_key:
      user: vagrant
      key: "{{ lookup('file', 'files/me.pub') }}"

  - block:
    - name: create htb directory 
      file:
        state: directory
        path: /home/vagrant/htb
      register: htb

    - name: copy htb ovpn file to remote
      copy: 
        src: files/lab_tylalin.ovpn
        dest: "{{ htb.path }}"

    - name: download pimpmykali with git
      git:
        repo: https://github.com/Dewalt-arch/pimpmykali.git
        dest: /home/vagrant/add-on
    become: true
    become_user: vagrant
```

The Ansible playbook is designed to set up a Kali Linux environment with various tools and configurations. Here's a breakdown of its components:

1. **Playbook Metadata**:
   - `name: kali setup`: Specifies the name of the playbook.
   - `hosts: all`: Indicates that the playbook will be applied to all hosts.
   - `become: true`: Allows the tasks to be executed with elevated privileges.
   - `gather_facts: false`: Disables the gathering of facts about the hosts.

2. **Tasks**:
   - **Set Timezone**: Configures the timezone to "Australia/Melbourne" using the `timezone` module.
   - **Install Tools**: Installs various tools using the `apt` module. Tools include `tmux`, `feh`, `gobuster`, `nuclei`, `dirsearch`, `nishang`, `seclists`, `steghide`, and `exiftool`.
   - **Copy SSH Public Key**: Copies an SSH public key to the remote host's `vagrant` user's authorized keys list, allowing passwordless SSH authentication.
   - **Block**: Defines a block of tasks that are executed sequentially.
     - **Create HTB Directory**: Creates a directory named "htb" in the home directory of the `vagrant` user.
     - **Copy HTB OVPN File**: Copies an OpenVPN configuration file (`lab_tylalin.ovpn`) to the "htb" directory.
     - **Download PimpMyKali with Git**: Clones the PimpMyKali repository from GitHub into the "/home/vagrant/add-on" directory.

3. **Additional Notes**:
   - The `become` and `become_user` directives are used within the block to execute tasks as the `vagrant` user with elevated privileges.
   - The `register` keyword is used to store the result of the "create htb directory" task, which can be referenced later if needed.

It automates the setup of a Kali Linux environment, installs essential tools, configures the timezone, sets up SSH authentication, and prepares directories and files required for penetration testing activities.