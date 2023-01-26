* To quickly identify all available Ethernet interfaces, you can use the ip command:
	```
	ip a
	```

* Show all information about network adapters:
	```
	sudo lshw -class network
	```

* We can define our adapters in `/etc/netplan/*.yaml and then use `sudo netplan apply` 
for applying our changes
  - for example: in below `99-installer-config.yaml` we say to interface with mac
   `00:11:22:33:44:55` to use dhcp and use the name `eth_lan0`:

	```
	network:
		version: 2
		renderer: networkd
		ethernets:
			eth_lan0:
			dhcp4: true
			match:
				macaddress: 00:11:22:33:44:55
			set-name: eth_lan0
	``` 

* `ethtool` is a program that displays and changes Ethernet card settings such as 
auto-negotiation, port speed, duplex mode, and Wake-on-LAN:
	```
	sudo ethtool eth0
	```

* If we want to change an network configuration temporary we use `ip` command:
	```
	sudo ip addr add 10.102.66.200/24 dev enp0s3
	```

* With `ip` we can set link up or down:
	```
	ip link set dev enp0s3 up
	ip link set dev enp0s3 down
	```

* Verify the ip address configuration:
	```
	ip address show dev enp0s3
	```

* show routes:
	```
	ip route show
	```

* Add a default gateway:
	```
	sudo ip route add default via 192.168.1.1
	```

* del a default gateway:
	```
	sudo ip route del default via 192.168.1.1
	```

* If we need temporary dns we add them to `/etc/resolv.conf`:
	```
	nameserver 8.8.8.8
	nameserver 1.1.1.1
	```

* if we want to flush all ip addresses from an interface:
	```
	ip addr flush eth0
	```

* if we want to work with ipv6 we use `-6` switch with `ip` command.

* File `/etc/resolv.conf` is a symlink to `/run/systemd/resolve/stub-resolv.conf`
and all our changes to `/etc/resolv.conf` will remove on reboot.

* If we want our enp0s3 interface use dhcp we configure 
`/etc/netplan/99_config.yaml` with this content:
	```
	network:
	version: 2
	renderer: networkd
	ethernets:
		enp3s0:
		dhcp4: true
	```
  - then we use `sudo netplan apply` so configuration can be applied.