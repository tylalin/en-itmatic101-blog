# Customise VM template with cloud-init on Promox

Promox is my favourite KVM backend virtualisation implementation for my home lab and test environment. Very easy to install and configure to start with compared to the full blonde KVM on Debian based or Red Hat based Linux distribution. It could have been easier these days since the Cockpit web admin interface has matured a lot with all virtualisation related add-on. And I have been using Promox as my preferred virtualisation platform for quite some times now.

Following is the step-by-step guide on how to create a customised VM template with cloud-init on Promox. Ubuntu 22.04 LTS cloud image would be used as base image for this activity. Note that the cloud image comes with cloud-init installed, and it is purposed to use with any cloud platform providers like AWS or Azure. But it can also be utilised for the virtualisation platforms like Promox or VMware ESXi.&#x20;

## Prerequisite

* To customise the cloud image, the tool named "virt-customize" is used. Here is how to install the required package for "virt-customize".\
  $ `apt update -y && apt install libguestfs-tools -y`
* ``

