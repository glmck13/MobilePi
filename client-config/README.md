# Client config

## Install required packages
Install the following packages on your Pi:
```
apt install strongswan libstrongswan-extra-plugins libcharon-extra-plugins charon-cmd resolvconf
```

## Install Certificate Authority (CA) certs
The letsencrypt cert on the VPN server is signed by Internet Security Research Group (ISRG).  See [this article](https://letsencrypt.org/2019/04/15/transitioning-to-isrg-root.html) for details.
In order to establish a chain of trust on the client, ISRG's certificate needs to be
populated under /etc/ipsec.d/cacerts.  Fortunately, ISRG's cert is already installed under
/etc/ssl/certs, so it's a simple matter to just link the certficate between the two
directories:
```
ln -s /usr/share/ca-certificates/mozilla/ISRG_Root_X1.crt /etc/ipsec.d/cacerts/ISRG_Root_X1.pem
ln -s /usr/share/ca-certificates/mozilla/DST_Root_CA_X3.crt /etc/ipsec.d/cacerts/DST_Root_CA_X3.pem
```
On some Debian/Ubuntu systems, the apparmor software may block the IPsec subsystem from accessing these symlinks.  In this case, check the rules in /etc/apparmor.d/local/usr.lib.ipsec.charon, and potentially add the directive: #include <abstractions/ssl_keys>

## Enter VPN settings
Next, configure /etc/ipsec.conf and /etc/ipsec.secrets (see sample files in repository).  

## Adjust DNS settings if necessary
If you notice that Strongswan fails to update the DNS settings on your host after the VPN tunnel starts, check out the documentation for its [resolve plugin](
https://docs.strongswan.org/docs/latest/plugins/resolve.html).  In my case I needed to changfe the iface parameter to wlo2 on my Ubuntu laptop to get things to work.

## Start VPN
You have two options for starting the VPN (both must be run as root):
```
ipsec up yourvpnname
```
-or-
```
charon-cmd --profile ikev2-eap --host yourvpnserver.domain.name --identity yourvpnname --cert /etc/ipsec.d/cacerts/DST_Root_CA_X3.pem
```
If your connection freezes intermittently, you may have a problem with packet fragmentation if large packets inside the tunnel don't fit within the MTU defined on the encapsulating link.  To work around this, decrease the MTU size for the tunnel by editing the "mtu" value within /etc/strongswan.d/charon/kernel-netlink.conf.
# iPhone tethering
Install the following packages on your Pi:
```
apt install ipheth-utils libimobiledevice-utils libmtp-runtime usbmuxd
```
Add the following entry to /etc/network/interfaces to automatically configure the interface & IPsec tunnel:
```
allow-hotplug eth1
iface eth1 inet dhcp
    post-up /usr/sbin/ipsec up yourvpnname
    pre-down /usr/sbin/ipsec down yourvpnname
```
