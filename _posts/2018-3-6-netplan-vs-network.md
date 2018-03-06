

## Commands

Netplan uses a set of subcommands to drive its behavior:

`netplan generate` : Use /etc/netplan to generate the required configuration for the renderers.

`netplan apply` : Apply all configuration for the renderers, restarting them as necessary.

`netplan ifupdown-migrate` : Attempt to generate an equivalent configuration to what is specified in /etc/network/interfaces.


### How do I bring interfaces up or down?

While we recommend that people do not change the state of network interfaces without a good reason and simply let the renderer configured in netplan handle the network interfaces it is meant to configure, you can always modify interface settings manually via the ip command.

To bring up an interface:

```ip link set eth0 up```

To bring and interface down:

```ip link set eth0 down```

See man ip for more information on how you can manipulate the state of network interfaces.









## reference

https://wiki.ubuntu.com/Netplan
