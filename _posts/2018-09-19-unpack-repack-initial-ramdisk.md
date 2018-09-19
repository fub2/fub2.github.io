### Unpack initial ramdisk in a new folder

```
# mkdir test
# cd test
# zcat /boot/initrd-2.6.18-164.6.1.el5.img | cpio -idmv
```

After making all necessary changes, use commands to repack and compress initrd image

```
# find . | cpio -o -c | gzip -9 > /boot/test.img
```

### For image compressed with xz format, these commands are to extract image:

```
# mkdir /tmp/initrd
# cd /tmp/initrd
# xz -dc < initrd.img | cpio --quiet -i --make-directories 
```

Then to repack:

```
# cd /tmp/initrd
# find . 2>/dev/null | cpio --quiet -c -o | xz -9 --format=lzma >"new_initrd.img"
```
