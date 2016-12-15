# MobilePi
Configuration to establish a Pi-to-Pi IPsec VPN using Strongswan, plus instructions for USB-tethering to an iPhone hotspot
## Background
The apps in [MyVitals](https://github.com/glmck13/MyVitals) and [PiBarScan](https://github.com/glmck13/PiBarScan) connect a variety of Bluetooth devices to a Pi.  I realized the designs could be made more flexible by splitting the apps on the Pi into two sets: those that communicate directly with the Bluetooth devices (client-side apps), and the remainder that process data reported by these devices (server-side apps).  The client Pis could then be connected to the server Pi over an IPsec VPN, enabling the Bluetooth devices to be remoted over a WAN, while still exchanging data in real time with server-side apps.

This repository contains instructions & associated config files to connect a client-side Pi to a server-side Pi over an IPsec VPN using Strongswan.  Server-side authentication is achieved using X.509 certificates issued though https://letsencrypt.org/ using https://certbot.eff.org/ utilities, while client-side authentication uses EAP & MSCHAP2.  I also provide instructions for how to tether the Pi to an iPhone over USB in order to obtain cellular Internet access in environments where local WiFi is not avilable.
