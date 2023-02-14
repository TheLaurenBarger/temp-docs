Now that our home server is accessible via our CloudFlare domain address, we need to address the issue of dynamic external IP addresses configured for our Router.

{{< toc >}}

Dynamic External IP addresses.
==============================

I am sadly not a large enough corporation to warrant my own static external address. My ISP will instead continuously rotate the external IP assigned to my router. Therefore, I need to maintain the IP address on my CloudFlare DNS entries accordingly.

DD Client.
==========

DD Client is a service meant for maintaining the DNS entries on your domain provider when your ISP updates your external IP address.

DD Client container.
--------------------

Add the following image to your docker-compose.yaml.

```java
  ddclient:
    image: lscr.io/linuxserver/ddclient:latest
    container_name: ddclient
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Africa/Johannesburg
    volumes:
      - /opt/ddclient:/config
    restart: unless-stopped
```

Then run the following to bring up the DD Client container:

```bash
docker-compose up -d
```

DD Client configuration.
------------------------

The DD Client needs to be configured to update the external IP address of the CloudFlare DNS entries. In order to connect to CloudFlare and perform updates, we will need the following:

* domain name (eg. badger-quest.com)
* email username
* API global token
    

To retrieve your CloudFlare global API token, navigate to My Profile → API Tokens. Then select View next to your Global API Key:

![](attachments/14942209/14778375.png)

Navigate to the config volume configured in the docker-compose entry, and open the `ddclient.conf` file with your favourite text editor (it's VS code).

```java
## Check for updated IP address every minute on CloudFlare for the badger-quest.com domain.

daemon=600
syslog=yes
verbose=no
pid=/var/run/ddclient/ddclient.pid
ssl=yes                             

protocol=cloudflare
use=web
zone=badger-quest.com
login=lauren.barger@outlook.com
password=************************
badger-quest.com
```

The domain configuration should be populated as follows:

```java
## Template:
protocol=cloudflare
use=web
zone=<domain-name>
login=<email-address>
password=<global-api-token>
<domain-name>
```

Save the file for the changes to take affect.

Testing updates.
----------------

If you had enabled verbose logging, you should see entries in the container logs like the following:

```java
2022-10-18T11:42:52.286666768Z SUCCESS:  badger-quest.com -- Updated Successfully to ***.***.***.***
```

If you navigate to your domain DNS entries on CloudFlare, you should see that the IP address for the DNS entries has been updated to the value of your external IP address.

## Conclusion

My CloudFlare domain is now ensured to always be up to date with the latest dynamic IP assigned to my local router.

**←** [Home Server, Part 5: Remote Access (Cloudflare and NGINX Proxy Manager)](327719.html)

[Home Server, Part 7: Mikrotik Port Forwarding Secure Ports](4292609.html) **→**

References
==========

1. [https://developers.cloudflare.com/dns/manage-dns-records/how-to/managing-dynamic-ip-addresses/](https://developers.cloudflare.com/dns/manage-dns-records/how-to/managing-dynamic-ip-addresses/)
2. [https://docs.linuxserver.io/images/docker-ddclient](https://docs.linuxserver.io/images/docker-ddclient)
3. [https://ddclient.net/usage.html](https://ddclient.net/usage.html)
4. [https://gist.github.com/benscabbia/a6b158974219f96ecb1e87b97890c5fe](https://gist.github.com/benscabbia/a6b158974219f96ecb1e87b97890c5fe)