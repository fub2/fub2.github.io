### Predictable Network Interface Names

Starting from systemd v197, Ethernet NIC is no longer with name of eth0, ethc1. They become eno1, ens2, enp0f1, enx8990,etc. These are so-called predictable naming scheme.

* Rules:
-- biosdevname, take precedence
-- user has added udev rules
-- distro specific scheme

1. Names incorporating Firmware/BIOS provided index numbers for on-board devices (example: eno1)
2. Names incorporating Firmware/BIOS provided PCI Express hotplug slot index numbers (example: ens1)
3. Names incorporating physical/geographical location of the connector of the hardware (example: enp2s0)
3. Names incorporating the interfaces's MAC address (example: enx78e7d1ea46da)
4. Classic, unpredictable kernel-native ethX naming (example: eth0)


* Reference:

https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/
https://github.com/systemd/systemd/blob/master/src/udev/udev-builtin-net_id.c
