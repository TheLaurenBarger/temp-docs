Plex is a media streaming app which gives us a GUI to access our downloaded media and provides a series of organizational functions as well.

<!-- Table of Contents -->

Deluge Docker container.
========================

Add the following container details to your docker-compose.yaml [https://hub.docker.com/r/linuxserver/deluge](https://hub.docker.com/r/linuxserver/deluge):

```java
  deluge:
    image: lscr.io/linuxserver/deluge:latest
    container_name: deluge
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Africa/Johannesburg
      - DELUGE_LOGLEVEL=error #optional
    volumes:
      - /opt/deluge/config:/config
      - /mnt/media/downloads:/downloads
    ports:
      - 8112:8112
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
```

Then run the following to bring up the container:

```java
docker-compose up -d
```

Notice how we have mounted our external hard drive media mount against the downloads directory of deluge.

In this docker compose entry we have specified a volume for the container called downloads. This folder will store all our deluge torrents and downloads while they’re seeding. For this setup, I have created a directory on an external hdd called media which will store all my media and a sub folder to handle downloads.

Conclusion.
===========

With that covered, our first step is Deluge!

← [Home Media Server, Part 4: Sonarr, Radarr and any otharr](16744589.html)

[Home Media Server, Part 6: Overseerr](16973825.html) **→**

References.
===========

1. [https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/](https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/)