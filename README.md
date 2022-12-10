# ATAK-UAS-RTSP

<p align="center">
    <img src="/images/logo.png" alt="rtsp-simple-server">
</p>

_rtsp-simple-server_ is a ready-to-use and zero-dependency server and proxy that allows users to publish, read and proxy live video and audio streams through various protocols:

|protocol|description|publish|read|proxy|
|--------|-----------|-------|----|-----|
|RTSP|fastest way to publish and read streams|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:|
|RTMP|allows to interact with legacy software|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:|
|HLS|allows to embed streams into a web page|:x:|:heavy_check_mark:|:heavy_check_mark:|

Features:

* Publish live streams to the server
* Read live streams from the server
* Act as a proxy and serve streams from other servers or cameras, always or on-demand
* Each stream can have multiple video and audio tracks, encoded with any codec, including H264, H265, VP8, VP9, MPEG2, MP3, AAC, Opus, PCM, JPEG
* Streams are automatically converted from a protocol to another. For instance, it's possible to publish a stream with RTSP and read it with HLS
* Serve multiple streams at once in separate paths
* Authenticate users; use internal or external authentication
* Query and control the server through an HTTP API
* Read Prometheus-compatible metrics
* Redirect readers to other RTSP servers (load balancing)
* Run external commands when clients connect, disconnect, read or publish streams
* Reload the configuration without disconnecting existing clients (hot reloading)
* Compatible with Linux, Windows and macOS, does not require any dependency or interpreter, it's a single executable

[![Release](https://img.shields.io/github/v/release/aler9/rtsp-simple-server)](https://github.com/aler9/rtsp-simple-server/releases)
[![Docker Hub](https://img.shields.io/badge/docker-aler9/rtsp--simple--server-blue)](https://hub.docker.com/r/aler9/rtsp-simple-server)
[![API Documentation](https://img.shields.io/badge/api-documentation-blue)](https://aler9.github.io/rtsp-simple-server)

# Download the latest rtsp-simple-server

For AWS LightSail use Linux_AMD64

    wget https://github.com/aler9/rtsp-simple-server/releases/download/v0.20.3/rtsp-simple-server_v0.20.3_linux_amd64.tar.gz

### Extract the binary and a yaml config file

    tar -zxvf rtsp-simple-server_v0.20.3_linux_amd64.tar.gz

### Give Root access to folder /usr/local/etc/

    sudo chmod a+rwx /usr/local/etc/
    
### Copy the rtsp-simple-server binary to /usr/local/bin/

    sudo cp rtsp-simple-server /usr/local/bin/rtsp-simple-server
    
### Copy the rtsp-simple-server.yml binary to /usr/local/etc/

    sudo cp rtsp-simple-server.yml /usr/local/etc/rtsp-simple-server.yml

### Copy the configuration file to use with ATAK to /usr/local/etc

    sudo curl -K https://github.com/aerial-defence/ATAK-UAS-RTSP/blob/main/rtsp-simple-server.yml -o /usr/local/etc/rtsp-simple-config.yml

### Create a server file

    sudo tee /etc/systemd/system/rtsp-simple-server.service >/dev/null << EOF
    [Unit]
    After=network.target
    [Service]
    ExecStart=/usr/local/bin/rtsp-simple-server /usr/local/etc/rtsp-simple-server.yml
    [Install]
    WantedBy=multi-user.target
    EOF

### Enable the newly created rtsp-simple-server service

    sudo systemctl enable rtsp-simple-server

Start rtsp-simple-server and tail syslog to see how things look

    sudo systemctl start rtsp-simple-server && tail -f /var/log/syslog

Check that the service is running

    systemctl status rtsp-simple-server.service

### In ATAK UAS Tool use the following Network Preferences:

 - Video Broadcast Type: Wowza Video  
 - Destination IP Address: <ip address of the server> i.e. - 192.168.86.232  
 - Video Destination Port: 554 
 - Use SSL: No  
 - Video Broadcast Identifier: live/ATAK

(The live/ATAK can be changed, but make sure to not put an ending slash)

### Then try to broadcast video, you should see this in /var/log/syslog:

> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [conn 192.168.86.60:52250] opened 
> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [session 343057601] opened by 192.168.86.60:52250
> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [session 343057601] is publishing to path 'live/ATAK', 1 track with TCP

### Then test with VLC -> Open Network Stream, adjust path name according:

rtsp://ipaddress:554/live/ATAK

### HLS instructions coming soon.
