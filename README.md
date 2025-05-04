# MobilePi
Configuration to establish a Pi-to-Pi IPsec VPN using Strongswan, plus instructions for USB-tethering to an iPhone hotspot
## Background
This repository contains instructions & associated config files to connect a client-side Pi to a server-side Pi over an IPsec VPN using Strongswan.  Server-side authentication is achieved using X.509 certificates issued though https://letsencrypt.org/ using https://certbot.eff.org/ utilities, while client-side authentication uses EAP & MSCHAPv2.  I also provide instructions for how to tether the Pi to an iPhone over USB in order to obtain cellular Internet access in environments where local WiFi is not avilable.  
