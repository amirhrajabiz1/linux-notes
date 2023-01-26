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

* If we want configure our enp0s3 interface staticly, we configure
`/etc/netplan/99_config.yaml` with this content:
	```
	network:
	version: 2
	renderer: networkd
	ethernets:
		eth0:
		addresses:
			- 10.10.10.2/24
		routes:
			- to: default
			via: 10.10.10.1
		nameservers:
			search: [mydomain.com, otherdomain.com]
			addresses: [10.10.10.1, 1.1.1.1]
	```
  - then we use `sudo netplan apply` so configuration can be applied.
  - What is `search` in above file?
   - assume you ping the host `server1`, then the system query These:
    1. First query this FQDN `server1.mydomain.com`.
	2. if above dns query gives no respond, query this `server1.otherdomain.com`
   - If there was no respond, DNS server will provide a result of notfound and
     the DNS query will fail

* Show loopback interface:
	```
	ip address show lo
	```

* `/etc/resolv.conf` is a file for dns configs. You can change this file for temperary purposes. The system of dhcp server can change it.

* In `/etc/hosts` we can put host names with ips:
	```
	127.0.0.1   localhost
	127.0.1.1   ubuntu-server
	10.0.0.11   server1 server1.example.com vpn
	10.0.0.12   server2 server2.example.com mail
	10.0.0.13   server3 server3.example.com www
	10.0.0.14   server4 server4.example.com file
	```

* How ubuntu finds out where looks for dns first?
> Ubuntu looks in `/etc/nsswitch.conf` file in `hosts` entry.


* The `networkctl` can query the status of network links

* If we go to `/etc/networkd-dispatcher` we see several directories like:
 - `carrier.d`
 - `off.d`
 - `routable.d`
* If we put `do.sh` in `off.d` and issue `networkd-dispatcher` command,
 now if an interface go to `off` state the `do.sh` runs.