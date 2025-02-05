# Troubleshooting Checklists

!!! info ""
    You are welcome to ask for help in our [community chat](https://gitter.im/browseyourlife/community).
    [Sponsors](../../funding.md) receive direct [technical support](https://photoprism.app/contact) via email.
    Before reporting a bug, try to determine the cause of your problem.

### Connection Fails ###

If [your browser](browsers.md) cannot connect to the Web UI even after waiting a few minutes, run this command to display
the last 100 log messages (omit `--tail=100` to see all):

```bash
docker-compose logs --tail=100
```

Before reporting a bug:

- [ ] Check the logs for messages like *disk full*, *wrong permissions*, *no route to host*, *connection failed*, and *killed*; if a service has been killed or otherwise automatically terminated, this points to a [memory problem](docker.md#adding-swap)
- [ ] Make sure you are using the correct protocol (http or https), port (default is 2342), and hostname or IP address
(default is localhost)
- [ ] Note that HTTP security headers will prevent the app from loading in a frame (override them or wait for additional config options)
- [ ] Verify your computer meets the [system requirements](../index.md#system-requirements)
- [ ] Go through the [checklist for fatal server errors](#fatal-server-errors)

Should MariaDB get stuck in a restart loop and PhotoPrism can't connect to it, this indicates a [memory](docker.md#adding-swap),
[filesystem](docker.md#file-permissions), or other [permission issue](docker.md#kernel-security):

```
mariadb: mysqld: ready for connections.
mariadb: mysqld (initiated by: unknown): Normal shutdown
photoprism: dial tcp 172.18.0.2:3306: connect: no route to host
mariadb: mysqld: Shutdown complete
```

!!! tldr ""
    The default [Docker Compose](https://docs.docker.com/compose/) config filename is `docker-compose.yml`. For simplicity, it doesn't need to be specified when running the `docker-compose` command in the same directory. Config files for other apps or instances should be placed in separate folders.

!!! note ""
    If you see no errors or no logs at all, you may have started the server on a different host
    and/or port. There could also be an [issue with your browser](browsers.md), ad blocker, or firewall settings.

You can also try (re-)starting the app and database without `-d`. This keeps their containers running
in the foreground and shows log messages for troubleshooting:

```bash
docker-compose stop
docker-compose up 
```

To enable [debug mode](../config-options.md), add this to the environment variables of the `photoprism`
service in your `docker-compose.yml` and restart for changes to take effect:

```
PHOTOPRISM_DEBUG: "true"
```

### Docker Doesn't Work ###

↪ [Getting Docker Up and Running](docker.md)

### MariaDB Issues ###

↪ [Troubleshooting MariaDB Problems](mariadb.md)

### Bad Performance ###

↪ [Performance Tips](performance.md)

### Fatal Server Errors ###

Fatal errors are often caused by one of the following conditions:

- [ ] Your (virtual) server disk is full
- [ ] There is disk space left, but the [inode limit](https://serverfault.com/questions/104986/what-is-the-maximum-number-of-files-a-file-system-can-contain) has been reached
- [ ] The storage folder is not writable or there are other filesystem permission issues
- [ ] You have accidentally mounted the wrong folders
- [ ] The server is low on memory
- [ ] You didn't configure [at least 4 GB of swap](docker.md#adding-swap)
- [ ] The server CPU is overheating
- [ ] The server has an outdated operating system that is not fully compatible
- [ ] The database server is not available, [incompatible](../index.md#databases), or incorrectly configured
- [ ] You've upgraded the MariaDB server version without running `mariadb-upgrade`
- [ ] Files are stored on an unreliable device such as a USB flash drive or a shared network folder
- [ ] There are network problems caused by a proxy, firewall, or unstable connection
- [ ] Kernel security modules such as [AppArmor](https://wiki.ubuntu.com/AppArmor) and [SELinux](https://en.wikipedia.org/wiki/Security-Enhanced_Linux) are blocking permissions
- [ ] Your Raspberry Pi has not been configured according to our [recommendations](../raspberry-pi.md#system-requirements)

We recommend checking your [Docker logs](docker.md#viewing-logs) for messages like *disk full*, *wrong permissions*, *no route to host*,
*connection failed*, and *killed* as described above. If a service has been killed or otherwise automatically terminated,
this points to a memory problem.

### App Not Loading ###

If you only see the logo when you navigate to the server URL and nothing else happens, even if you wait a moment:

- [ ] The browser cannot communicate properly with your server, e.g. because a proxy or CDN is configured incorrectly (check its configuration and try without)
- [ ] HTTP security headers prevent the app from loading in a frame (override them or wait for additional config options)
- [ ] JavaScript is disabled in your browser settings (enable it)
- [ ] JavaScript was disabled by a browser plugin (disable it or add an exception)
- [ ] An ad blocker blocks requests (disable it or add an exception)
- [ ] You are using an [incompatible browser](browsers.md) (try another browser)
- [ ] There is a problem with your network connection (test if other sites work)

We also recommend that you check your [browser logs](browsers.md) for errors and warnings.

### Missing Pictures ###

If you have indexed your library and some images or videos are missing, first check *Library > Errors*
for errors and warnings. In case the application logs don't contain anything helpful:

- [ ] The pictures are in [Review](../../user-guide/organize/review.md) due to low quality or incomplete metadata
- [ ] The [file type](../faq.md#what-media-file-types-are-supported) is generally unsupported
- [ ] The [file type](../faq.md#what-media-file-types-are-supported) is generally supported, but a specific feature or codec is not
- [ ] The files have bad filesystem permissions, so they can't be opened by the indexer
- [ ] The indexer has skipped the files because they are exact duplicates
- [ ] They are in *Library > Hidden* because thumbnails could not be created:
    - [ ] *Convert to JPEG* is disabled in *Settings > Library*
    - [ ] FFmpeg and/or RAW converters are disabled in *Settings > Advanced*
    - [ ] The file is broken and cannot be opened
    - [ ] The sidecar and/or cache folders are not writable
- [ ] Multiple files were [stacked](../../user-guide/organize/stacks.md#for-what-reasons-can-files-be-stacked) based on their metadata or file names
- [ ] The [private](../../user-guide/organize/private.md) or [archived](../../user-guide/organize/archive.md) status was restored from a backup
- [ ] The NSFW filter is enabled, so they were marked as [private](../../user-guide/organize/private.md)
- [ ] You are not signed in as admin, so you can't see everything
- [ ] You try to index a shared drive on a remote server, but the server is offline
- [ ] The indexer has crashed because you didn't configure [at least 4 GB of swap](docker.md#adding-swap)
- [ ] Somebody has deleted files without telling you
- [ ] You are connected to the wrong server or CDN, or a DNS record has not been updated yet

### Broken Thumbnails ###

If some pictures have broken or missing thumbnails, first check *Library > Errors* for errors and warnings.
In case the application logs don't contain anything helpful:

- [ ] The issue can be resolved by reloading the page or clearing the browser cache
- [ ] You browse [non-JPEG](../faq.md#what-media-file-types-are-supported) files in *Library > Originals* which have an icon but no preview
- [ ] *Dynamic Previews* are enabled in *Settings > Advanced*, but the server is not powerful enough
- [ ] The sizes in *Settings > Advanced* have been changed so the request can't be fulfilled
- [ ] FFmpeg and/or RAW converters are disabled in *Settings > Advanced*
- [ ] *Convert to JPEG* is disabled in *Settings > Library*
- [ ] Your (virtual) server disk is full
- [ ] Your sidecar and/or cache folders are not writable (anymore)
- [ ] Files were deleted manually, for example to free up disk space
- [ ] Files can't be opened, e.g. because the file system permissions have been changed
- [ ] Files are stored on an unreliable device such as a USB flash drive or a shared network folder
- [ ] Some thumbnails could not be created because you didn't configure [at least 4 GB of swap](docker.md#adding-swap)
- [ ] The browser cannot communicate properly with your server, e.g. because a proxy or CDN is configured incorrectly (check its configuration and try without)
- [ ] Your proxy, router, or firewall has a request rate limit, so some requests fail
- [ ] There are other network problems caused by a firewall, router, or unstable connection
- [ ] An ad blocker blocks requests (disable it or add an exception)
- [ ] You are connected to the wrong server or CDN, or a DNS record has not been updated yet

We also recommend checking your [Docker logs](docker.md#viewing-logs) for messages like *disk full*, *wrong permissions*, and *killed* as
described above. If a service has been killed or otherwise automatically terminated, this points to a memory problem.

### Videos Don't Play ###

If videos do not play and/or you only see a white/black area when you open a video:

- [ ] You are using an [incompatible browser](browsers.md), e.g. without AVC support (try another browser)
- [ ] AVC support or related JavaScript features have been disabled in your browser (check the settings and try another browser)
- [ ] It is a large non-AVC video that needs to be transcoded first (wait or pre-transcode with the [convert command](http://localhost:8000/getting-started/docker-compose/#command-line-interface))
- [ ] An ad blocker blocks requests (disable it or add an exception)
- [ ] Files are stored on an unreliable device such as a USB flash drive or a shared network folder (check if the files are accessible)
- [ ] There are network problems caused by a proxy, firewall, or unstable connection (try a direct connection)
- [ ] You are connected to the wrong server or CDN, or a DNS record has not been updated yet

We also recommend that you check your [Docker](docker.md#viewing-logs) and [browser logs](browsers.md)
for messages related to *HTTP requests*, *permissions*, *security*, *FFmpeg*, *videos*, and *file conversion*.

*[AVC]: MPEG-4 / H.264
*[CDN]: Content Delivery Network
*[CPU]: Central Processing Unit
*[DNS]: Domain Name System
*[HTTP]: Hypertext Transfer Protocol
*[USB]: Universal Serial Bus
*[SSD]: Solid-State Drive
*[RAW]: image format that contains unprocessed sensor data
*[UI]: User Interface
*[URL]: Web Address
*[proxy]: HTTPS reverse proxy
*[firewall]: network security system that controls traffic
*[FFmpeg]: transcodes video files
*[NSFW]: Not Safe For Work
*[swap]: substitute for physical memory
*[host]: physical computer, cloud server, or virtual machine that runs Docker