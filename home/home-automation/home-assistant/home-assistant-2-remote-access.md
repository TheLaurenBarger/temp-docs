In order to become the over lord of my house no matter which network I’m connected to, I need to expose my Home Assistant instance remotely.

<!-- Table of Contents -->

Nginx Proxy Manager host entry.
===============================

Add the following Nginx host entry:

![](attachments/13467649/13729793.png)

![](attachments/13467649/13664257.png)

Cloudflare subdomain entry.
===========================

![](attachments/13467649/13828097.png)

Firewall Rule.
--------------

sudo ufw allow 8123

Rule added  
Rule added (v6)

Conclusion.
===========

text

References.
===========

1. [https://community.home-assistant.io/t/docker-network-mode-host-and-container-discovery/218448/11](https://community.home-assistant.io/t/docker-network-mode-host-and-container-discovery/218448/11)
