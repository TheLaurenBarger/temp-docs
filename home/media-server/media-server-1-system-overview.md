Before we dig into the how-to, let’s take a look at where we’re heading.

This section assumes the following has been completed:

[Home Server, Part 1: Hardware](2031617.html)

[Home Server, Part 2: Ubuntu OS](14024705.html)

[Home Server, Part 3: Docker](1900552.html)

<!-- Table of Contents -->

Home media server structure.
============================

In order of installation, the components of the media server setup that we will cover the installation and configuration for is as follows:

### Deluge

Deluge is a torrent client. There are many torrent client options available and each have their pros and cons, but we’re using Deluge for this build. Deluge is also going to allow us to set things like active downloading hours and download options specific to the source of the downloading request.

### Jackett

Jacket is a proxy application that will translate incoming download requests into torrent magnet links by maintaining its own list of indexers and trackers on various torrent sights.

### Sonarr, Radarr, etc

Sonarr and Radarr are both examples of a series of applications which handle the management of your media libraries. Each fork is specific to a paricular media type, for example, Sonarr is specific to tv shows and Radarr is specific to movies. There are many many instances of these. These apps will drive the process of monitoring RSS feeds to schedule downloads for media you’ve flagged when it becomes available. The apps will also handle the interaction with Jackett indexers to find media and Deluge for downloading the torrents.

### Plex

Plex is a “client-server media system”. It will give us a platform for watching our media and maintaining it’s meta data.

### Overseerr

Overseerr is a web application that we can use to add requests to our media server and alert us when those requests are fulfilled.

For a hysterical and actually extremely accurate technology selection guide, have a look at this fantastic flow chart for Home Media Server setup: [https://imgur.com/chNkIx6](https://imgur.com/chNkIx6)

Conclusion.
===========

And that’s our road map for the moment! We’ll continue on with our first step: Deluge!

[Home Media Server, Part 2: Deluge](16744449.html) **→**

References.
===========

1. [https://github.com/sebgl/htpc-download-box](https://github.com/sebgl/htpc-download-box)
2. [https://blog.harveydelaney.com/installing-radarr-sonar-and-deluge-on-your-unraid-setup/](https://blog.harveydelaney.com/installing-radarr-sonar-and-deluge-on-your-unraid-setup/)
3. [https://gist.github.com/tschant/ecf1d4c06cd84a0711df0fe015024331](https://gist.github.com/tschant/ecf1d4c06cd84a0711df0fe015024331)
4. [https://shownotes.opensourceisawesome.com/setting-up-sonarr-radarr-jackett-and-qbittorrent-on-docker-using-portainer/](https://shownotes.opensourceisawesome.com/setting-up-sonarr-radarr-jackett-and-qbittorrent-on-docker-using-portainer/)