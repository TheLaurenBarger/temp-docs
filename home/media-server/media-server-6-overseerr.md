As great as Plex is, it has a slight weakness around requesting media. In comes Overseerr which gives us a way to request media and receive notifications when that media is available.

<!-- Table of Contents -->

Overseerr Docker compose entry.
===============================

Add the following configuration to your docker-compose.yaml file.

```java
  overseerr:
    image: lscr.io/linuxserver/overseerr:latest
    container_name: overseerr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Africa/Johannesburg
    volumes:
      - /opt/overseerr/config:/config
    ports:
      - 5055:5055
    restart: unless-stopped
```

Bring up the container with the following command:

```java
docker-compose up -d
```

And viola! A whole new service!

Configure Overseerr.
====================

Navigate to your server IP at port 5055 and follow the installation steps presented by Overseerr.

Conclusion.
===========

With that covered, our first step is Deluge!

← [Home Media Server, Part 5: Plex](16744730.html)

**→**

References.
===========

1. [https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/](https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/)