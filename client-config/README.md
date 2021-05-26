# Client config
apt-get install the following packages on your Pi:
```
strongswan
libstrongswan-extra-plugins
libcharon-extra-plugins
charon-cmd
resolvconf
```
The letsencrypt cert on the VPN server is signed by Internet Security Research Group (ISRG).  See [this article](https://letsencrypt.org/2019/04/15/transitioning-to-isrg-root.html) for details.
In order to establish a chain of trust on the client, ISRG's certificate needs to be
populated under /etc/ipsec.d/cacerts.  Fortunately, ISRG's cert is already installed under
/etc/ssl/certs, so it's a simple matter to just link the certficate between the two
directories:
```
ln -s /usr/share/ca-certificates/mozilla/ISRG_Root_X1.crt /etc/ipsec.d/cacerts/ISRG_Root_X1.pem
ln -s /usr/share/ca-certificates/mozilla/DST_Root_CA_X3.crt /etc/ipsec.d/cacerts/DST_Root_CA_X3.pem
```

Next, configure /etc/ipsec.conf and /etc/ipsec.secrets (see sample files in repository).  

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
apt-get install the following packages on your Pi:
```
ipheth-utils
libimobiledevice-utils
libmtp-runtime
usbmuxd
```
Add the following entry to /etc/network/interfaces to automatically configure the interface & IPsec tunnel:
```
allow-hotplug eth1
iface eth1 inet dhcp
    post-up /usr/sbin/ipsec up yourvpnname
    pre-down /usr/sbin/ipsec down yourvpnname
```
