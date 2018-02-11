I always look to update the Linux virtual machine running on top of my Window 10 Desktop. Where Ubuntu has its daily build of cloud images published at their website. Natrually it is ideal for me to get familar how it is working and start working with it.

I have an VMWare workstation 12.x environment. Naturally first choice to deoploy VM with OVA file. Unfortunally, there's quite several issues when booting into kernel. It is mainly the disk issue. Kenel compains there was read/write/page sync problems when booting. Then I have to turn to use their QCOW2 file. [Artful  IMG file](https://cloud-images.ubuntu.com/artful/20180126/artful-server-cloudimg-amd64.img) works perfectly booting into login prompt. And yes, before using it you have to convert image format to VMDK.

First time using cloud image we'd have to know a little bit about cloud-init which is one module of OpenStack and its implementation is in [GitHub](https://github.com/openstack/cloud-init). Running cloud image seems to be not a widely apopted approach. We'd have to play a few tricks to have it run on top of non-cloud environment. Here comes step by step working it through:

# Prepare all required images

### VMDK image - booting image of your virtual machines
  1. Download a .img file of your target cloud image, whatever 16.04, 17.10.
  2. Convert it to VMDK format with `qemu-img`:
  ```qemu-img convert -f qcow2 /mnt/hgfs/vm/artful-server-cloudimg-amd64.img -O vmdk /mnt/hgfs/vm/artful.vmdk```
  3. Prepare cloud configuration data
    a. Prepare a file with name `meta-datawith` and content:
      ```
      instance-id: iid-local01
      local-hostname: cloud-img-host01
      ```
     b. Prepare another file with name `user-data` and content:
      ```
      #cloud-config

      password: Passw0rd
      chpasswd: { expire: False }
      ssh_pwauth: True
      ```
     c. Generate a seek ISO file to include these 2 files:
	 `genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data`
	 
	 4. Create a fresh virtual machine with VMWare workstation and point disk drive to the VMDK file you generated step 2 and point CD-ROM file point to the ISO file generated in step 3
	 5. Powering on VM and the login credential is ubuntu/Passw0rd as defined in user-data



## Resources and References
https://cloud-images.ubuntu.com/artful
http://cloudinit.readthedocs.io/en/latest/topics/datasources/nocloud.html
