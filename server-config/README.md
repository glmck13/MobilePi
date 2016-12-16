
# Configuring IKEv2/IPsec VPN server using server-side certificate & client EAP-MSCHAPv2 authentication
These instructions assume your Pi sits on a private 192.168.1.0 network behind a public-facing home router.

1. apt-get install the following packages on your Pi:
```
strongswan
strongswan-charon
strongswan-libcharon
strongswan-starter
libcharon-extra-plugins
libstrongswan
libstrongswan-standard-plugins
```
2. Register a public domain name for your home router.  I have a dynamic DNS name registered through dyndns.org,
which my home router updates on every reboot.

3. Get an X.509 certificate for your server.  Certificates are available through https://letsencrypt.org/.
A utility to register and renew your certificate is available from https://certbot.eff.org/.
Select Software="None of the above", and System="Debian (other)" on the main page for instructions
on downloading & running the "cerbot-auto" utility.  After running the utility the first time, install the
various .pem files under /etc/ipsec.d by linking back to /etc/letsencrypt:
```
ln -s /etc/letsencrypt/archive/[your domain]/fullchain1.pem /etc/ipsec.d/certs/fullchain.pem
ln -s /etc/letsencrypt/archive/[your domain]/cert1.pem /etc/ipsec.d/certs/cert.pem
ln -s /etc/letsencrypt/archive/[your domain]/chain1.pem /etc/ipsec.d/cacerts/chain.pem
ln -s /etc/letsencrypt/archive/[your domain]/privkey1.pem /etc/ipsec.d/private/privkey.pem

```
4. Following the cerbot instructions, update root's crontab to renew your certificate once (or more) per day.
The sample crontab.root file in the repository will renew your cert at 5:30AM daily.

5. In /etc/sysctl.conf uncomment the line "net.ipv4.ip_forward=1".  In addition, update /etc/rc.local
to configure iptables on reboot (see sample file in repository).

6. Follow the instructions on your home router to forward UDP ports 4500 & 500 to your Pi.

7. Configure /etc/ipsec.conf and /etc/ipsec.secrets (see same files in repository).
