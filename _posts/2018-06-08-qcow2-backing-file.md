
Assume that you already have your base image as a qcow2 - Ubuntu cloud image for example. Below command can create 2 images for 2 guest machines basing same image.

```
qemu-img create -f qcow2 \
                -o backing_file=/path/to/base/base_image.qcow2 \
                /path/to/guest1/image2.qcow2
                

qemu-img create -f qcow2 \
                -o backing_file=/path/to/base/image.qcow2 \
                /path/to/guest2/image2.qcow2
```                

Then in your guest, use image1.qcow2 and image2.qcow2 as disk to create 2 guests. This file will only get the difference with the base image.

Check qemu-img's man page for more details. qemu-img also has commands to commit the overlay file changes into the base image, rebase on another base, etc.
