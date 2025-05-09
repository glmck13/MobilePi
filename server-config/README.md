
# Server config
These instructions assume your Pi sits on a private 192.168.1.0 network behind a public-facing home router.

Install the following packages on your Pi:
```
apt install strongswan libstrongswan-extra-plugins libcharon-extra-plugins
```
Follow the instructions on your home router to forward TCP port 80/443 (for certificate updates) and UDP ports 4500 & 500 (for IPsec) to your Pi.  Optionally, configure the router to direct traffic for the client subnet (192.168.10.0/24) to the Pi.  That should enable hosts on your home network connect to remote clients while they're attached.  

Register a public domain name for your home IP address, and get an X.509 certificate.  Certificates are available through https://letsencrypt.org/. A utility to register and renew your certificate is available from https://certbot.eff.org/. Select Software="None of the above", and System="Debian (other)" on the main page for instructions on downloading & running the "cerbot-auto" utility.  After running the utility the first time, install the various .pem files under /etc/ipsec.d by linking back to /etc/letsencrypt:
```
ln -s /etc/letsencrypt/live/[your domain]/fullchain.pem /etc/ipsec.d/certs/fullchain.pem
ln -s /etc/letsencrypt/live/[your domain]/cert.pem /etc/ipsec.d/certs/cert.pem
ln -s /etc/letsencrypt/live/[your domain]/chain.pem /etc/ipsec.d/cacerts/chain.pem
ln -s /etc/letsencrypt/live/[your domain]/privkey.pem /etc/ipsec.d/private/privkey.pem
```
On some Debian/Ubuntu systems, the apparmor software may block the IPsec subsystem from accessing these symlinks.  In this case, check the rules in /etc/apparmor.d/local/usr.lib.ipsec.charon, and potentially add the directive: #include <abstractions/ssl_keys>

In /etc/sysctl.conf uncomment the line "net.ipv4.ip_forward=1".  Then add the following entry to root's crontab to configure iptables on reboot:
```
@reboot /usr/sbin/iptables -t nat -A POSTROUTING -s 192.168.10.0/24 -o eth0 -j MASQUERADE
```  
Lastly, configure /etc/ipsec.conf and /etc/ipsec.secrets (see sample files in repository), and reboot your Pi.
