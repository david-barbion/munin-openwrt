# OpenWRT munin plugins

## Switch plugins

* switchif monitors RX and TX trafic of desired switch ports
* switcherr monitors reported errors of desired switch ports

## Installation

SSH must be installed and started. Then, you must have root access to your device. You need a working munin agent on your router, munin-lite is a good start.

Muninlite a munin agent written in Bash. It can be extended with custom plugins. Execute this commands to install it:

```
opkg update
opkg install muninlite
opkg install ethtool
```

In order to execute additional plugins, you will have to modify `/usr/sbin/munin-node`: add "plugindir\_" to the PLUGINS variable, example:

```bash
PLUGINS="plugindir_ cpu if_ if_err_ load memory processes netstat uptime interrupts irqstats"
```

Then create the directory `/usr/sbin/munin-node-plugin.d/`:

```
mkdir -p /usr/sbin/munin-node-plugin.d/
```

After that, put the the two scripts into this directory. Each script must be renamed/linked to match the switch device and port number:

```
mv switchif_switchX_portX switchif_switch0_port0
ln -s switchif_switch0_port0 switchif_switch0_port1
mv switcherr_switchX_portX switcherr_switch0_port0
ln -s switcherr_switch0_port0 switcherr_switch0_port1
chmod +x switchif_switch0_port0
chmod +x switcherr_switch0_port0
```

This will allow to monitor both port0 and port1 of switch0.

## How to find switch and port informations

To determine switch number and available ports of your router, execute the command `swconfig`:

```
root@ap:~# swconfig list
Found: switch0 - ag71xx-mdio.0
```

Then this command will display port informations:

```
root@ap:~# swconfig dev switch0 show
Global attributes:
	enable_vlan: 1
	enable_mirror_rx: 0
	enable_mirror_tx: 0
	mirror_monitor_port: 0
	mirror_source_port: 0
	arl_table: address resolution table
Port 0: MAC xx:xx:xx:xx:xx:xx
[...]
```

To get available ports:
```
root@ap:~# swconfig dev switch0 show | egrep ^Port
Port 0:
Port 1:
Port 2:
Port 3:
Port 4:
Port 5:
Port 6:
```


