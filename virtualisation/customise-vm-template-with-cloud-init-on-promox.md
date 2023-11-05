---
cover: >-
  https://images.unsplash.com/photo-1588200908342-23b585c03e26?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw5fHx0ZW1wbGF0ZXxlbnwwfHx8fDE2OTkxNzc4ODJ8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Customise VM template with cloud-init on Promox

Promox is my favourite KVM backend virtualisation implementation for my home lab and test environment. Very easy to install and configure to start with compared to the full blonde KVM on Debian based or Red Hat based Linux distribution. It could have been easier these days since the Cockpit web admin interface has matured a lot with all virtualisation related add-on. But I have been using Promox as my preferred virtualisation platform for quite some times now so might as well stick to it rather than mucking around with Cockpit.&#x20;

Following is the step-by-step guide on how to create a customised VM template with cloud-init on Promox. Ubuntu 22.04 LTS cloud image will be used as the base image for this activity. Note that the cloud image comes with cloud-init installed, and it is purposed to use with any cloud platform providers like AWS or Azure. But it can also be utilised for the virtualisation platforms like Promox or VMware ESXi.&#x20;

## Prerequisite

* To customise the cloud image, the tool named "virt-customize" is used. Here is how to install the required package for "virt-customize". **Note** - the Promox repo must be disabled to install it if there is no proper subscription with Promox. \
  \
  $ `apt update -y && apt install libguestfs-tools -y`

## Steps to prep the cloud image with cloud-init&#x20;

* SSH into the Promox's shell with root account
*   Download the Ubuntu 22.04 LTS cloud image \


    <pre><code><strong>$ wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
    </strong></code></pre>


*   Use the following 'virt-customize' commands to prep the downloaded cloud image.\


    ```
    # install all desired packages in the cloud image 
    $ virt-customize -a jammy-server-cloudimg-amd64.img --install qemu-guest-agent,vim,bash-completion,wget,curl,unzip

    # update all installed packages in the image
    $ virt-customize -a jammy-server-cloudimg-amd64.img --update

    # set preferred timezone
    $ virt-customize -a jammy-server-cloudimg-amd64.img --timezone "Australia/Darwin"

    # copy the ssh pubkey into the image
    # that id_rsa.pub needs to be uploaded to the host in piror to run this command
    $ virt-customize -a jammy-server-cloudimg-amd64.img --ssh-inject root:file:./id_rsa.pub
    ```


*   Now create a new VM instance by using the prep cloud image and configure as below. Note that do not startup the VM before converting it into VM template. \


    ```
    # create a new VM instance with desired specs
    $ qm create 1001 --name "jammy-server-cloudimg-template" --memory 1024 --cores 1 --net0 virtio,bridge=vmbr0

    # import the prep cloud image to the VM with target storage location
    $ qm importdisk 1001 jammy-server-cloudimg-amd64.img local-lvm

    # setup the system disk with required scsi parameters
    $ qm set 1001 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-1001-disk-0

    # setup the boot disk
    $ qm set 1001 --boot c --bootdisk scsi0

    # attach the cloudinit
    $ qm set 1001 --ide2 local-lvm:cloudinit

    # setup the serial vga parameters for web console sessions
    $ qm set 1001 --serial0 socket --vga serial0

    # enable the qemu-guest-agent 
    $ qm set 1001 --agent enabled=1

    # create a login user
    $ qm set 1001 --ciuser tyla

    # set a password for the login user
    $ qm set 1001 --cipassword "secret"

    # set the ipconfig to dhcp
    $ qm set 1001 --ipconfig0 ip=dhcp

    # resize the attached disk
    $ qm resize 1001 scsi0 32G

    # convert the VM instance into a VM template
    $ qm template 1001
    ```


*   As the VM template is ready, it is time to spin up the new VM instance based on the template and start it to check if the VM was configured properly. Then shutdown the VM and delete as following. \


    ```
    # spin up a new VM instance with the VM template
    $ qm clone 1001 100 --name ubuntu-srv01

    # startup the VM
    $ qm start 100

    # stop the VM and teardown
    $ qm stop 100 && qm destroy 100
    ```

