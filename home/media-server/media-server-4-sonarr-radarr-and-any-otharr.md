The real magic and brains of the setup will sit in these media management apps. Sonarr and Radarr are both examples of a series of applications which handle the management of your media libraries. Each fork is specific to a paricular media type, for example, Sonarr is specific to tv shows and Radarr is specific to movies. There are many many instances of these. These apps will drive the process of monitoring RSS feeds to schedule downloads for media you’ve flagged when it becomes available. The apps will also handle the interaction with Jackett indexers to find media and Deluge for downloading the torrents.

There are a range of them, but the setup for each of them is extremely similar. For the purposes of this tutorial, we’ll focus on the installation of Sonarr.

<!-- Table of Contents -->

Sonarr Docker container.
========================

Add the following container details to your docker-compose.yaml [https://hub.docker.com/r/linuxserver/sonarr](https://hub.docker.com/r/linuxserver/sonarr):

```java
  sonarr:
    image: linuxserver/sonarr
    container_name: sonarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Africa/Johannesburg
      - UMASK_SET=022 #optional
    volumes:
      - /opt/sonarr/config:/config
      - /mnt/media:/mnt/media
    ports:
      - 8989:8989
    restart: unless-stopped
```

Then run the following to bring up the container:

```java
docker-compose up -d
```

Notice how we have mounted our external hard drive media mount against the media directory of sonarr.

Sonarr will be handling the downloads of two of my media libraries: anime and series. Therefore I’m going to create folders called anime and series in my `/mnt/media` directory for both libraries.

Configuring Sonarr.
===================

Navigate to your server IP at port 8989: [http://192.168.0.10:8989](http://192.168.0.10:8989) to reach the Sonarr web UI. Once you’re here, navigate to the **Settings** tab on the right. Select the Show Advanced option for more swag points and freedom.

Configure download indexers.
----------------------------

We need to connect Sonarr to Jackett as an indexer proxy. Navigate to **Settings** → **Indexers** and select the + icon to add a new indexer. The idea here is that you will add an indexer to each of the Jackett indexers you configured in the previous step which support the media that the -arr app supports. In this case, Sonarr will be downloading anime and series. So I will link the following Jackett profiles:

*   1337x
    
*   LimeTorrents
    
*   Nyaa
    

The configuration should be the same for each of them. For each you will need to give them a name and then navigate to Jackett to locate the following information:

*   **Jackett API Key**: Shown in the top right of the Jackett web UI
    
*   **Jackett Indexer Torsnab Feed**: Select the **Copy Torznab Feed** button for each indexer that you want to configure.
    

These entries will be entered into the Sonarr configuration as follows:

*   **URL**: Jackett Indexer Torsnab Feed
    
*   **API Key**: Jackett API Key
    

Also configure the correct media categories which correspond with the categores listed for the indexer on Jackett.

After entering the information for each index, hit the **Test** button to make sure that they’re working from Sonarr!

My final Indexers are as follows:

![](attachments/16744589/17465442.png)

Configure download client.
--------------------------

Our download client is Deluge! Navigate to **Settings** → **Download Clients**. Select the + icon to add a client to Sonarr.

Enter in the information for our Deluge client:

*   **Name**: Deluge
    
*   **Enable**: yes
    
*   **Host**: 192.168.0.10
    
*   **Port**: 8112
    
*   **Password**: <the password you configured for deluge>
    
*   **Category**: sonarr
    

The Category here is the same as the Label name in Deluge!

Hit **Test** to make sure the connection is working before moving on!

Our last step is to create a remote path mapping to connect Sonarr to the completed downloads folder that Deluge will move items labelled “sonarr” to. Scroll down to the bottom of the configuration and select the + icon beneath **Remote Path Mappings**.

Enter in the server IP for Deluge and enter in the directory information that Deluge uses for the Sonarr label:

*   **Host:** 192.168.0.10
    
*   **Remote Path**: `/downloads/complete/sonarr`
    
*   **Local Path**: `/mnt/media/downloads/complete/sonarr`
    

Remember, the remote path is the path as Deluge understands it, while the local path is the mapping from the point of view of the Sonarr container.

Configure download profiles.
----------------------------

Navigate to **Settings** → **Profiles**. Here you can configure requirements on any files that will be downloaded. These requirements are passed along to Jackett when it searches it’s indexers for torrents. There is only one thing I want to configure here, and that is adding some torrent uploader restrictions when downloading anime from nyaa.

Scroll to the bottom of the page and select the + icon beneath the **Release Profiles** section. Create a new release profile called **Anime - Subbed**. The restrictions we will define are for the torrent name to contain at least one of the following terms:

*   HorribleSubs
    
*   horriblesubs
    
*   subsplease
    

![](attachments/16744589/17334369.png)

Finally we need to specify that this restriction only applies to our Anime indexer: **Jackett -** **Nyaa**:

![](attachments/16744589/17334375.png)

Hit Save and the restriction is created.

Adding media.
=============

Media can be added to Sonarr in many different ways a few of which we’ll touch on here. Your added shows will appear in your **Series** tab. Any media that gets added which has upcoming shows being released will appear in the **Calendar** tab. Anything being downloaded at the moment can be viewed in the **Activity** → **Queue** tab.

Adding new shows.
-----------------

Navigate to **Series** → **Add New** and search for the show you want to monitor. Here you can specify the folder name that will be created for the show, which episodes you want Sonarr to download and at what quality profile. What is important to configure is the **Series Type**. If the series is an anime, you want the downloads to move to specific indexers with specific criteria which are not going to work for normal series. Once you’ve finished the edit, select the **Add** button.

Import existing media.
----------------------

To add any existing files to Sonarr, copy them into the `/mnt/media/anime` or `/mnt/media/series` folders. Then navigate to **Series** → **Library Import**. Here you can select the folder your media is located in and then map it to a particular show.

Conclusion.
===========

There you go! If you can configure Sonarr, you should have almost no issue configuring media apps also ending in -arr. They are a fantastic collection of apps and they’ve bought me much joy. With everything we’ve set up so far, we have managed to achieve an almost complete loop for maintaining and downloading our media. What comes next is how to watch it.

← [Home Media Server, Part 3: Jackett](16809985.html)

[Home Media Server, Part 5: Plex](16744730.html) **→**

References.
===========

1. [https://hub.docker.com/r/linuxserver/sonarr](https://hub.docker.com/r/linuxserver/sonarr)
2. [https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/](https://zacholland.net/a-complete-guide-to-setting-up-a-plex-home-media-server-with-automated-requests-downloads/)
3. [https://blog.harveydelaney.com/installing-radarr-sonar-and-deluge-on-your-unraid-setup/](https://blog.harveydelaney.com/installing-radarr-sonar-and-deluge-on-your-unraid-setup/)