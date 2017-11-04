
# Server config
These instructions assume your Pi sits on a private 192.168.1.0 network behind a public-facing home router.

apt-get install the following packages on your Pi:
```
strongswan
libstrongswan-extra-plugins
libcharon-extra-plugins
```
Register a public domain name for your home router.  I have a dynamic DNS name registered through dyndns.org,
which is updated by my home router on every reboot.  

Follow the instructions on your home router to forward TCP port 443 (for certificate updates) and
UDP ports 4500 & 500 (for IPsec) to your Pi.  Also, if possible, add a commend to the router to direct traffic for the client subnet (192.168.10.0/24) to the Pi.  That should enable hosts on your home network connect to remote clients while they're attached.  

Get an X.509 certificate for your server.  Certificates are available through https://letsencrypt.org/.
A utility to register and renew your certificate is available from https://certbot.eff.org/.
Select Software="None of the above", and System="Debian (other)" on the main page for instructions
on downloading & running the "cerbot-auto" utility.  After running the utility the first time, install the
various .pem files under /etc/ipsec.d by linking back to /etc/letsencrypt:
```
ln -s /etc/letsencrypt/live/[your domain]/fullchain.pem /etc/ipsec.d/certs/fullchain.pem
ln -s /etc/letsencrypt/live/[your domain]/cert.pem /etc/ipsec.d/certs/cert.pem
ln -s /etc/letsencrypt/live/[your domain]/chain.pem /etc/ipsec.d/cacerts/chain.pem
ln -s /etc/letsencrypt/live/[your domain]/privkey.pem /etc/ipsec.d/private/privkey.pem
```
Following the cerbot instructions, update root's crontab to renew your certificate once (or more) per day.
The sample crontab.root file in the repository will renew your cert at 5:30AM daily.  

In /etc/sysctl.conf uncomment the line "net.ipv4.ip_forward=1".  In addition, update /etc/rc.local
to configure iptables on reboot (see sample file in repository).  

Configure /etc/ipsec.conf and /etc/ipsec.secrets (see sample files in repository).  

Reboot your Pi.

## References
The following are some useful reference sites I used to distill the information above:
- https://www.strongswan.org/
- https://dcamero.azurewebsites.net/
- http://thomas.irmscher.bayern/crypto/VPN-with-Pi-StrongSwan-IKEv2-and-EAP-MSCHAPv2-for-Windows-Phone-8.1.html

