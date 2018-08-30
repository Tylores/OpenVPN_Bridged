# OpenVPN_Bridged
OpenVPN setup with a tap interface on Ubuntu 16.04

## Initial Setup
Follow steps 1-6 in the following link:

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-16-04

## Bridged : Server Config

To create a bridged vpn connection, you must create a virtual bridge within the server to pass packets between interfaces.

``` bash
sudo apt-get install bridge-utils
```

Now we need to copy the files created in steps 1-6 to the /etc/openvpn directory.

 ``` bash
 cd ~/openvpn-ca/keys
sudo cp ca.crt server.crt server.key ta.key dh2048.pem /etc/openvpn
```

Next, copy the sample openvpn server.conf file into the same directory

``` bash
gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz | sudo tee /etc/openvpn/server.conf
```

The last step is to modify the server.conf file to create an ethernet tunnel.

``` base
sudo nano /etc/openvpn/server.conf
```
Disable "dev tun" by prefacing with a ";" and enable "dev tap0" by removing the ";".

```
# "dev tun" will create a routed IP tunnel,
# "dev tap" will create an ethernet tunnel.
# Use "dev tap0" if you are ethernet bridging
# and have precreated a tap0 virtual interface
# and bridged it with your ethernet interface.
# If you want to control access policies
# over the VPN, you must create firewall
# rules for the the TUN/TAP interface.
# On non-Windows systems, you can give
# an explicit unit number, such as tun0.
# On Windows, use "dev-node" for this.
# On most systems, the VPN will not function
# unless you partially or fully disable
# the firewall for the TUN/TAP interface.
dev tap0
;dev tun
```

Disable server by prefacing with ";".

```
# Configure server mode and supply a VPN subnet
# for OpenVPN to draw client addresses from.
# The server will take 10.8.0.1 for itself,
# the rest will be made available to clients.
# Each client will be able to reach the server
# on 10.8.0.1. Comment this line out if you are
# ethernet bridging. See the man page for more info.
;server 10.8.0.0 255.255.255.0
```

Enable server-bridge by removing ";" and set subnet.

```
# Configure server mode for ethernet bridging.
# You must first use your OS's bridging capability
# to bridge the TAP interface with the ethernet
# NIC interface.  Then you must manually set the
# IP/netmask on the bridge interface, here we
# assume 10.8.0.4/255.255.255.0.  Finally we
# must set aside an IP range in this subnet
# (start=10.8.0.50 end=10.8.0.100) to allocate
# to connecting clients.  Leave this line commented
# out unless you are ethernet bridging.
server-bridge 10.8.0.4 255.255.255.0 10.8.0.50 10.8.0.100
```

For our purposes we will configure all clients to redirect all traffic through the VPN by enabling push by removing ";".

```
# If enabled, this directive will configure
# all clients to redirect their default
# network gateway through the VPN, causing
# all IP traffic such as web browsing and
# and DNS lookups to go through the VPN
# (The OpenVPN server machine may need to NAT
# or bridge the TUN/TAP interface to the internet
# in order for this to work properly).
push "redirect-gateway def1 bypass-dhcp"
```




