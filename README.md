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
disable "dev tun" by prefacing with a ";" and enable "dev tap0" by removing the ";".

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
dev tap
;dev tun
```

