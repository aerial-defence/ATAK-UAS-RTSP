# ATAK-UAS-RTSP

# Download the latest rtsp-simple-server (0.17.13)

    wget https://github.com/aler9/rtsp-simple-server/releases/download/v0.17.13/rtsp-simple-server_v0.17.13_linux_amd64.tar.gz

### Extract the binary and a yaml config file

    tar -zxvf rtsp-simple-server_v0.17.13_linux_amd64.tar.gz

### Copy the binary to /usr/local/bin/

    sudo cp rtsp-simple-server /usr/local/bin/rtsp-simple-server

### Copy the configuration file to use with ATAK to /usr/local/etc

    sudo curl -K https://gist.github.com/curtishall/77b9dd3660511b7e173fbc4647ccfcb3#file-rtsp-simple-server-yml -o /usr/local/etc/rtsp-simple-config.yml

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
