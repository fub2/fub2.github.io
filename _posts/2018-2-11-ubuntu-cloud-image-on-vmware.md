I always look to update the Linux virtual machine running on top of VMware workstation on Window 10 Desktop. Where Ubuntu has its daily build of cloud images published at their website. Natrually it is ideal for me to get familar how it is working and start working with it.

I have an VMWare workstation 12.x environment. Naturally first choice to deoploy VM with OVA file. Unfortunally, there's quite several issues when booting into kernel. It is mainly the disk issue. Kenel complains there was read/write/page sync problems when booting. Then I have to turn to use their QCOW2 file. [Bionic Beaver IMG file](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img) works perfectly booting into login prompt. And yes, before using it you have to convert image format to VMDK.

First time using cloud image we'd have to know a little bit about cloud-init which is one module of OpenStack and its implementation is in [GitHub](https://github.com/openstack/cloud-init). Running cloud image seems to be not a widely apopted approach. We'd have to play a few tricks to have it run on top of non-cloud environment. Here comes step by step working it through:

# Prepare all required images

### VMDK image - booting image of your virtual machines

  1. Download a .img file of your target cloud image, whatever 16.04, 17.10 or 18.04 LTS.
  
  2. Check basic info of this qcow2 image and update its vitual disk size as required (optional)
  
```
qemu-img info artful-server-cloudimg-amd64.img
qemu-img resize artful-server-cloudimg-amd64.img 40G
```
  
  3. Convert it to VMDK format with `qemu-img`:
  
  ```qemu-img convert -f qcow2 artful-server-cloudimg-amd64.img -O vmdk artful.vmdk```
  
  4. Prepare cloud configuration data
  ..* Prepare a file with name `meta-data` with and content:
    
```
instance-id: iid-local01
local-hostname: cloud-img-host01
```

  ..* Prepare another file with name `user-data` and content:
     
```
#cloud-config

password: Passw0rd
chpasswd: { expire: False }
ssh_pwauth: True
```
      
  ..* (optional)prepare network-config file if you'd like to specify networking configuration on boot:
    
```
## /network-config on NoCloud cidata disk
## version 1 format
## version 2 is completely different, see the docs
## version 2 is not supported by Fedora
---
version: 1
config:
- type: physical
  name: eth0
  mac_address: 00:60:16:93:b9:1a
  subnets:
  - type: static
    address: 192.168.125.210
    netmask: 255.255.255.0
    routes:
    - network: 0.0.0.0
      netmask: 0.0.0.0
      gateway: 192.168.125.1
- type: nameserver
  address: [192.168.125.1, 8.8.8.8, 8.8.4.4]
  search: [example.com, foo.biz, bar.info]
```
      
  ..* Generate a seek ISO file to include these 2 files:
    
```genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data``` or
```genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data network-config```
	 
  5. Create a fresh virtual machine with VMWare workstation and point disk drive to the VMDK file you generated step 2 and point CD-ROM file point to the ISO file generated in step 3
  
  6. Powering on VM and the login credential is ubuntu/Passw0rd as defined in user-data

### What if you'd like to get rid of cloud-init stuff

* wait until the VM boots
* login
* Execute:

```
echo 'datasource_list: [ None ]' | sudo -s tee /etc/cloud/cloud.cfg.d/90_dpkg.cfg
sudo apt-get purge cloud-init
sudo rm -rf /etc/cloud/; sudo rm -rf /var/lib/cloud/
```

Or run command of `dpkg-reconfigure cloud-init` and select option of "None: Failsafe datasource"

* reboot


## Resources and References
https://cloud-images.ubuntu.com/artful
http://cloudinit.readthedocs.io/en/latest/topics/datasources/nocloud.html
