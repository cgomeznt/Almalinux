Como deshabilitar ipv6 en REHL 9
=================================

32.1. Disabling IPv6 on a connection using nmcli
You can use the nmcli utility to disable the IPv6 protocol on the command line.

Prerequisites

The system uses NetworkManager to manage network interfaces.

Procedure

Optionally, display the list of network connections::

	# nmcli connection show
	NAME    UUID                                  TYPE      DEVICE
	Example 7a7e0151-9c18-4e6f-89ee-65bb2d64d365  ethernet  enp1s0
	...
	
	
Set the ipv6.method parameter of the connection to disabled::

	# nmcli connection modify Example ipv6.method "disabled"
	
Restart the network connection::

	# nmcli connection up Example
	
Verification

Display the IP settings of the device::

	# ip address show enp1s0
	2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
		link/ether 52:54:00:6b:74:be brd ff:ff:ff:ff:ff:ff
		inet 192.0.2.1/24 brd 192.10.2.255 scope global noprefixroute enp1s0
		   valid_lft forever preferred_lft forever
		   
If no inet6 entry is displayed, IPv6 is disabled on the device.

Verify that the /proc/sys/net/ipv6/conf/enp1s0/disable_ipv6 file now contains the value 1::

	# cat /proc/sys/net/ipv6/conf/enp1s0/disable_ipv6
	1
	
The value 1 means that IPv6 is disabled for the device.


Ahora lo hacemos en el GRUB
-------------------------------

Edit the file: /etc/default/grub y en la linea "GRUB_CMDLINE_LINUX" agregamos unicamente al final "ipv6.disable=1", es decir, NO TOCAMOS más nada::

	# vi /etc/default/grub 

	GRUB_TIMEOUT=5
	GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
	GRUB_DEFAULT=saved
	GRUB_DISABLE_SUBMENU=true
	GRUB_TERMINAL_OUTPUT="console"
	GRUB_CMDLINE_LINUX="crashkernel=1G-4G:192M,4G-64G:256M,64G-:512M resume=/dev/mapper/rl-swap rd.lvm.lv=rl/root rd.lvm.lv=rl/swap ipv6.disable=1"
	GRUB_DISABLE_RECOVERY="true"
	GRUB_ENABLE_BLSCFG=true

	
Compilamos nuvamente el Grub::

	# grub2-mkconfig -o /boot/grub2/grub.cfg
	Generating grub configuration file ...

	Adding boot menu entry for UEFI Firmware Settings ...
	done
	
Reiniciamos el equipo::

	init 6
	
Por ultimo ya podemos hacer el "ip address show" y un "netstat -natp" y no veremos rastro de ipv6.



https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_and_managing_networking/using-networkmanager-to-disable-ipv6-for-a-specific-connection_configuring-and-managing-networking


chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://www.ibm.com/support/pages/system/files/support/swg/swgdocs.nsf/0/61f7bf85ccb221c8852581ae006011bb/$FILE/MQ-9-disable-ipv6-rhel-7.3.pdf