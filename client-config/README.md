# Client config
apt-get install the following packages on your Pi:
```
libcharon-extra-plugins
libstrongswan
libstrongswan-extra-plugins
libstrongswan-standard-plugins
charon-cmd
strongswan
strongswan-charon
strongswan-libcharon
strongswan-starter
```
The letsencrypt cert on the VPN server is signed by Digital Signature Trust (DST).
In order to establish a chain of trust on the client, DST's certificate needs to be
sitting under /etc/ipsec.d/cacerts.  Fortunately, DSTs cert is already installed under
/etc/ssl/certs, so it's a simple matter to just link the certficate between the two
directories:
```
ln -s /etc/ssl/certs/DST_Root_CA_X3.pem /etc/ipsec.d/cacerts/DST_Root_CA_X3.pem
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
