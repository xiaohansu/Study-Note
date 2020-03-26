# Gst命令备忘

## 安装插件

```bash
sudo apt-get install gstreamer-plugins-*
```





### man gst-luanch-1.0

文档有官方例子







## 环境变量的设置

```GST_DEBUG_FILE``` : 重定向日志输出的文件；

```GST_DEBUG``` : 设置日志输出的级别；



******

******



### rtmpsrc保存到本地

```bash
gst-launch-1.0 rtmpsrc location=rtmp://192.168.6.220:1935/stream/123  ! "video/x-h264, stream-format=byte-stream" ! filesink location=/Users/suxiaohan/Desktop/Pic/test.flv
```

###rtmpsink

```bash
gst-launch-1.0 -v videotestsrc ! queue  ! x264enc ! flvmux !  rtmpsink location='rtmp://127.0.0.1/stream/123 live=1' 
```



### rtsp server

* 创建一个rtsp server

```bash
# tcp
docker run --rm -it -p 8554:8554/tcp dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

# udp
docker run --rm -it -p 8554:8554/udp dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

# 
docker run --rm -it -p 8554:8554 dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

####
# 端口绑定时指定传输协议tcp/udp，但是
# 当指定rtsp服务端tcp，客户端（rtspsrc）指定udp， 好像 本机可以获取rtsp流，局域网内的另一台主机则无法获取，提示被防火墙挡住---待验证
# 引出问题，当端口指定了协议（tcp/udp），用非此协议的方法去访问，会怎样？


```



* rtspsrc -> rtmpsink

```bash
gst-launch-1.0 rtspsrc location= rtsp://192.168.6.136:8554/test \
                    protocols=udp-mcast+udp latency=50 ! rtph264depay ! \
                    avdec_h264 ! vaapipostproc width=1280 \
                    height=720 ! videorate max-rate=24 ! \
                    clockoverlay time-format="%Y-%m-%d_%H:%M:%S" ! vaapipostproc ! \
                     vaapih264enc bitrate=2048 \
                    keyframe-period=30 quality-level=7 rate-control=cbr ! \
                    video/x-h264, profile=baseline ! flvmux streamable=true ! \
                    watchdog timeout=10000 ! rtmpsink max-lateness=-1 \
                    location="rtmp://192.168.6.136:1935/stream/test live=1"

...
```







### rtmp server



* 创建一个rtmp server

```bash
docker dd
```



* 推送模拟视频流到rtmp服务器

```bash
sudo gst-launch-1.0 --gst-debug='2,rtmpsink:5tmp:5'  -v videotestsrc ! queue ! x264enc ! flvmux ! rtmpsink max-lateness=1000 location='rtmp://127.0.0.1:1935/stream/123 live=1'1> rtmp_info.log 2>rtmp_error.log
```

