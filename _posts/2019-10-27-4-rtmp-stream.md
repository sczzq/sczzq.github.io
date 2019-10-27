---
title: rtmp stream using ffmpeg+nginx
date: 2019-04-08
categories:
- Tech
- Methodology
tags:
- blog
- post
---

> Use FFmpeg and nginx to push rtmp stream in local network.

## first

Install requirement packages
```
sudo apt install nginx nginx-full libnginx-mod-rtmp
sudo apt install ffmpeg
```

## second

Edit configure file of nginx `/etc/nginx/nginx.conf`.
Add this content in the end of that file.
```
rtmp {
	server {
		listen 2019;
		application live {
			live on;
		}
		application hls_alic{
			live on;
			hls on;
			hls_path /tmp/hls;
		}
	}
}
```

Then restart nginx
```
sudo systemctl restart nginx
```

## third

Push rtmp stream using FFmpeg.
```
ffmpeg -re -i *filename* -acodec copy -vcodec copy -f flv rtmp://192.168.1.104:2019/live
```

Note, the `port number` in ffmpeg command should same to `listen port number` of rtmp in nginx.conf file.

## Fourth

Pull rtmp stream using VLC

- Click `Media`
- Click `Open Network Stream`
- Click `Network`
- Enter the url, such as `rtmp://192.168.1.104:2019/live`
- Click `Play` button


