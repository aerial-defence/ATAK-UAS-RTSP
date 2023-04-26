# ATAK-UAS-RTSP

<p align="center">
    <img src="/images/logo.png" alt="mediamtx">
</p>

_mediamtx_ is a ready-to-use and zero-dependency server and proxy that allows users to publish, read and proxy live video and audio streams through various protocols:

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

[![Release](https://img.shields.io/github/v/release/aler9/mediamtx)](https://github.com/aler9/mediamtx/releases)
[![Docker Hub](https://img.shields.io/badge/docker-aler9/rtsp--simple--server-blue)](https://hub.docker.com/r/aler9/mediamtx)
[![API Documentation](https://img.shields.io/badge/api-documentation-blue)](https://aler9.github.io/mediamtx)

# Update the server instance
    sudo apt-get update
    sudo apt-get upgrade
    sudo reboot -h now

# Download the latest rtsp-simple-server

For AWS E2 use Linux 22.04 LTS t3.large (2v CPU and 8GB RAM)
For AWS LightSail use Linux_AMD64

    wget https://github.com/aler9/mediamtx/releases/download/v0.22.2/mediamtx_v0.22.2_linux_amd64.tar.gz

### Extract the binary and a yaml config file

    tar -zxvf mediamtx_v0.22.2_linux_amd64.tar.gz

### Give Root access to folder /usr/local/etc/

    sudo chmod a+rwx /usr/local/etc/
    
### Copy the mediamtx binary to /usr/local/bin/

    sudo cp mediamtx /usr/local/bin/mediamtx
    
### Copy the mediamtx.yml binary to /usr/local/etc/

    sudo cp mediamtx.yml /usr/local/etc/mediamtx.yml

### Copy the configuration file to use with ATAK to /usr/local/etc

    sudo curl -K https://raw.githubusercontent.com/aerial-defence/ATAK-UAS-RTSP/main/mediamtx.yml -o /usr/local/etc/mediamtx.yml

### Create a server file

    sudo tee /etc/systemd/system/mediamtx.service >/dev/null << EOF
    [Unit]
    After=network.target
    [Service]
    ExecStart=/usr/local/bin/mediamtx /usr/local/etc/mediamtx.yml
    [Install]
    WantedBy=multi-user.target
    EOF

### Enable the newly created mediamtx service

    sudo systemctl enable mediamtx

Start mediamtx and tail syslog to see how things look

    sudo systemctl start mediamtx && tail -f /var/log/syslog

Check that the service is running

    systemctl status mediamtx.service

### Then try to broadcast video, you should see this in /var/log/syslog:

> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [conn 192.168.86.60:52250] opened 
> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [session 343057601] opened by 192.168.86.60:52250
> Jan 12 23:55:41 rtsp-atak rtsp-simple-server[17053]: 2022/01/12 23:55:41 INF [RTSP] [session 343057601] is publishing to path 'live/ATAK', 1 track with TCP

### Then test with VLC -> Open Network Stream, adjust path name according:

rtsp://ipaddress:8554/live/ATAK

### HLS instructions coming soon.

# AWS Port Forwarding

For AWS, I have the following ports forwarded

<p align="center">
    <img src="https://user-images.githubusercontent.com/1116396/206856817-6b1abec0-60fd-4335-a2ee-46f81a36fc4b.jpg" alt="AWS Security Group">
</p>

# ATAK UAS Plugin Settings (current for UASTool 12.0)
### In ATAK UAS Tool use the following Network Preferences (Video Broadcast Preferences):

 - Video Broadcast Destination: RTSP-Push (VMS systems)
 - Video Destination IP Address: <ip address of the server> i.e. - 192.168.86.232  
 - Video Destination Port: 8554
 - Use SSL: No  
 - Video Broadcast Identifier: live/ATAK
 - Reliable P2P Connection (TCP): Yes

(The live/ATAK can be changed, but make sure to not put an ending slash)
