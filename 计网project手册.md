# Computer network project笔记
## Windows 平台

**使用FFmpeg推流到rtmp地址**

ffmpeg -re -i "E:\computer network\video.mp4" -vcodec libx264 -vprofile baseline -acodec aac -ar 44100 -strict -2 -ac 1 -f flv -q 10 rtmp://localhost:1935/myapp

**读取rtmp地址转换成mpd文件输出**

ffmpeg –i rtmp://localhost:1930/myapp –vcodec copy -movflags dash out.mpd

**使用FFmpeg对原始视频进行清晰度转换**

前三个是视频，最后一个是音频

ffmpeg -i "E:\CNP\test.mp4" -s 160x90 -c:v libx264 -b:v 250k -g 90 -an input_video_160x90_250k.mp4

ffmpeg -i "E:\CNP\test.mp4" -s 320x180 -c:v libx264 -b:v 500k -g 90 -an input_video_320x180_500k.mp4

ffmpeg -i "E:\CNP\test.mp4" -s 640x360 -c:v libx264 -b:v 500k -g 90 -an input_video_640x360_750k.mp4

ffmpeg -i "E:\CNP\test.mp4" -c:a aac -b:a 128k -vn input_audio_128k.mp4

**使用mp4box推出mpd文件**

manifest.mpd  # file name

mp4box -dash-strict 5000 -rap -profile dashavc264:onDemand -mpd-title BBB -out manifest.mpd -frag 2000 input_audio_128k.mp4 input_video_160x90_250k.mp4 input_video_320x180_500k.mp4 input_video_640x360_750k.mp4



### HTML文件

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx! Video server!</h1>
<p>Running on Song's PC</p>
<p>Team menber 11612001, 11612002, 11611181</p>

<script src="https://cdn.dashjs.org/latest/dash.all.min.js"></script>
<style>
    video {
       width: 640px;
       height: 360px;
    }
</style>
<body>
   <div>
       <video data-dashjs-player autoplay src="http://localhost:10240/dash4/manifest.mpd" controls></video>
   </div>
</body>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```

### Nginx 配置文件

```
#user  nobody;
worker_processes  1;
 
error_log  logs/error.log debug;
 
events {
    worker_connections  1024;
}
 
rtmp {
    server {
        listen 1935;
 
      
        application myapp {
            live on;
            dash on;
            dash_path /tmp/live;
			dash_nested on;
			dash_fragment 4;
			dash_playlist_length 120;
			dash_cleanup on;
        }
    }
}
 
 
http {
    include mime.types;
	default_type application/octet-stream;
	sendfile on;
	keepalive_timeout 65;
    autoindex on;

    server {

        listen 10240;

        location /live {
            root tmp;
            add_header Cache-Control no-cache;
	        add_header 'Access-Control-Allow-Origin' '*';
        }
 
        location /dash2 {
            root tmp;
            add_header Cache-Control no-cache;
	        add_header 'Access-Control-Allow-Origin' '*';
        }

        location /dash {
            root tmp;
            add_header Cache-Control no-cache;
	        add_header 'Access-Control-Allow-Origin' '*';
        }

        location /dash4 {
            root tmp;
            add_header Cache-Control no-cache;
	        add_header 'Access-Control-Allow-Origin' '*';
        }

         location /dash.js {          # //dash.js 安装目录下的baseline.html ，提供播放dash流的例子
           root tmp;
         	   
        }
        error_page 405 =200 $uri;
        }
}

```

