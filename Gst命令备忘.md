# Gst命令备忘

## 安装插件

```bash
sudo apt-get install gstreamer-plugins-*
```





### man gst-luanch-1.0

文档有官方例子


## pipeline\bin 可视化工具
```bash
https://gstreamer.freedesktop.org/documentation/tutorials/basic/debugging-tools.html?gi-language=c#getting-pipeline-graphs

https://dreampuf.github.io/GraphvizOnline

#  设置环境变量： GST_DEBUG_DUMP_DOT_DIR ： 视图文件存放目录

```



## 环境变量的设置

```GST_DEBUG_FILE``` : 重定向日志输出的文件；

```GST_DEBUG``` : 设置日志输出的级别；



******

******


### fpsdisplaysink
```bash
gst-launch-1.0 rtspsrc location=rtsp://x.x.x.x:xxx/xxx ! decodebin ! fpsdisplaysink
```

### rtmpsrc保存到本地

```bash
gst-launch-1.0 rtmpsrc location=rtmp://192.168.6.220:1935/stream/123  ! "video/x-h264, stream-format=byte-stream" ! filesink location=/Users/suxiaohan/Desktop/Pic/test.flv
```

### rtmpsink

```bash
gst-launch-1.0 -v videotestsrc ! queue  ! x264enc ! flvmux !  rtmpsink location='rtmp://127.0.0.1/stream/123 live=1' 
```
### shm 的使用
```bash
gst-launch-1.0 -v videotestsrc is-live=true ! x264enc key-int-max=10 ! shmsink socket-path=/tmp/foo sync=true wait-for-connection=false

 
 
 gst-launch-1.0 funnel name=f shmsrc socket-path=/tmp/foo !  watchdog timeout=10000 ! f. appsrc ! watchdog timeout=3600000 ! f. f. ! h264parse disable-passthrough=true ! matroskamux ! watchdog timeout=300000 ! filesink location=test.mk ## Invalid buffer timestamp; dropping buffer 会失败，未解决

 gst-launch-1.0 funnel name=f shmsrc socket-path=/tmp/foo !  watchdog timeout=10000 ! f. appsrc ! watchdog timeout=3600000 ! f. f. ! h264parse disable-passthrough=true ! flvmux ! watchdog timeout=300000 ! filesink location=test.flv
 
 ## is-live=true 必须
 
 gst-launch-1.0 -v /tmp/foo ! h264parse disable-passthrough=true ! flvmux streamable=true ! watchdog timeout=10000 ! rtmpsink max-lateness=1000000000 location="rtmp://192.168.6.122:1935/stream/test live=1 timeout=1"
 
```


### rtsp server

* 创建一个rtsp server
在mac系统下，不能使用host模式
```bash
# tcp
docker run --rm -it --network host -p 8554:8554/tcp dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

# udp
docker run --rm -it  --network host -p 8554:8554/udp dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

# 
docker run -d  -p 8554:8554 dyhexl/gst-rtsp-server:v1.16 ./test-launch "( videotestsrc is-live=true ! video/x-raw, width=1280, height=720, framerate=24/1 ! x264enc bitrate=4096 tune=zerolatency ! video/x-h264, profile=constrained-baseline ! rtph264pay name=pay0 pt=96 )"

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



* 使用docker创建一个rtmp server

```bash
docker pull alfg/nginx-rtmp

docker run -it -p 1935:1935 -p 8080:80 --rm alfg/nginx-rtmp

```



* 推送模拟视频流到rtmp服务器

```bash
sudo gst-launch-1.0 --gst-debug='2,rtmpsink:5tmp:5'  -v videotestsrc ! queue ! x264enc ! flvmux ! rtmpsink max-lateness=1000 location='rtmp://127.0.0.1:1935/stream/123 live=1'1> rtmp_info.log 2>rtmp_error.log
```





## get 在mac的命令记录
转载：https://gist.github.com/nebgnahz/26a60cd28f671a8b7f522e80e75a9aa5

Most GStreamer examples found online are either for Linux or for gstreamer 0.10.

This particular [release note](https://wiki.ubuntu.com/Novacut/GStreamer1.0) seems to have covered important changes, such as:

- ffmpegcolorspace => videoconvert
- ffmpeg => libav


Applying `-v` will print out useful information. And most importantly the negotiation results.

Before anything, you will need to install it:
On Mac OS, `brew` will work for the most of the times. To install `x264` support, simply type `brew options gst-plugins-ugly` and you will see `--with-x264` as an options. Sometimes you might need to `brew reinstall` to get some plugins (not sure why? it just seems reinstallation helps).

# From Swarmbox to Mac OS X

sender:
```
gst-launch-1.0 -v v4l2src device=/dev/video1 ! 'video/x-h264,width=800,height=448,framerate=30/1' ! h264parse ! rtph264pay config-interval=10 pt=96 ! udpsink host=192.168.0.100 port=9000
```

receiver:
```
gst-launch-1.0 -v udpsrc port=9000 ! 'application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264, sprop-parameter-sets=(string)"Z0JAKLtAZBy/gKJAAAADAEAAAA8YEAALcbAAtx73vheEQjU\=\,aM44gA\=\=", payload=(int)96, ssrc=(uint)3725838184, timestamp-offset=(uint)2716743768, seqnum-offset=(uint)769' ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! videoconvert ! facedetect ! videoconvert ! glimagesink
```

Need to understand what `sprop-parameter-sets` does.

Most of the commands below are mainly tested on Mac OS X

# Simple Commands

```
gst-launch-1.0 videotestsrc ! osxvideosink
```
```
gst-launch-1.0 -v videotestsrc ! glimagesink 
```
```
gst-launch-1.0 playbin uri=file:///<filepath>
```

# H264 encoding and decoding
Start by putting all of them in a single pipeline

```
gst-launch-1.0 videotestsrc is-live=true ! x264enc ! h264parse ! avdec_h264 ! videoconvert ! osxvideosink
```
Another success I had is to use `VideoToolbox`. The caps filter are somewhat needed so that negotiation will succeed. Not sure how this approach compares to the `x264` one.

```
gst-launch-1.0 -v videotestsrc ! vtenc_h264 ! video/x-h264,width=640,height=480,framerate=30/1 ! vtdec_hw ! 'video/x-raw,format=NV12' ! videoconvert ! osxvideosink
```
You can save the stream as a file and play it with `playbin`:

```
gst-launch-1.0 videotestsrc ! x264enc ! mpegtsmux ! filesink location=testfile.ts
```

# H264 encoding/decoding with RTP payload

```
gst-launch-1.0 videotestsrc is-live=true ! x264enc ! h264parse ! rtph264pay ! rtph264depay ! avdec_h264 ! videoconvert ! osxvideosink
```

# H264 encoding/decoding with UDP-RTP

Sender:

```
gst-launch-1.0 videotestsrc is-live=true ! video/x-raw,framerate=25/1 ! videoconvert ! x264enc ! h264parse ! rtph264pay pt=96 ! udpsink host=127.0.0.1 port=5000
```

Receiver:
```
gst-launch-1.0 -v udpsrc port=5000 ! application/x-rtp,clock-rate=90000,payload=96 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! osxvideosink
```

This kindof works, except the receiving side is telling me things are too slow.
```
There may be a timestamping problem, or this computer is too slow.
```

# H264 encoding/decoding with TCP
```
gst-launch-1.0 videotestsrc horizontal-speed=5 ! x264enc tune="zerolatency" threads=1 ! tcpserversink port=4444
```
```
gst-launch-1.0 tcpclientsrc port=4444 host=localhost ! h264parse ! avdec_h264 ! glimagesink
```

This works fine, I did wait for a couple of seconds before seeing the rolling test source.

# To stream to VLC:

## GStreamer sender
```
gst-launch-1.0 videotestsrc ! vtenc_h264 ! rtph264pay config-interval=10 pt=96 ! udpsink host=127.0.0.1 port=5000
```

## VLC Receiver
```
$ cat test.sdp
v=0
m=video 5000 RTP/AVP 96
c=IN IP4 127.0.0.1
a=rtpmap:96 H264/90000
```

# With OpenCV
You can install opencv plugins in `gst-plugins-bad` by `brew install gst-plugins-bad --with-opencv`.

Then you can live test face detection in your pipeline.
```
gst-launch-1.0 avfvideosrc ! videoconvert ! facedetect ! videoconvert ! osxvideosink
```

# Resolution

It seems that OS X `avfvideosrc` only supports the following resolution: `320x240`, `352x288`, `640x480`, `960x540`, `1280x720`. Otherwise, it will report `ERROR: from element /GstPipeline:pipeline0/GstAVFVideoSrc:avfvideosrc0: Internal data flow error.`

### Using Rust to develop GStreamer plugins

- [blog](https://coaxion.net/blog/2016/05/writing-gstreamer-plugins-and-elements-in-rust/)
- [repository](https://github.com/sdroege/rsplugin)

### GStreamer Rust Bindings

- [repository](https://github.com/arturoc/gstreamer1.0-rs)

### GObject INtrospection bindings

[github](https://github.com/gi-rust)

Some typical ways people measuring the performance (use end to end FPS)

Generate N numbers of buffers and get the execution time.

```
gst-launch-1.0 filesrc num-buffers=100 location=/dev/zero blocksize=8294400 ! videoparse format=rgba width=1920 height=1080 ! glimagesink sync=false
```

You would have something like:
```
Setting pipeline to PAUSED ...
Pipeline is PREROLLING ...
Got context from element 'sink': gst.gl.GLDisplay=context, gst.gl.GLDisplay=(GstGLDisplay)"\(GstGLDisplayCocoa\)\ gldisplaycocoa0";
Pipeline is PREROLLED ...
Setting pipeline to PLAYING ...
New clock: GstSystemClock
Got EOS from element "pipeline0".
Execution ended after 0:00:00.921270000
Setting pipeline to PAUSED ...
Setting pipeline to READY ...
Setting pipeline to NULL ...
Freeing pipeline ...
```

So we run 100 1920x1080 frames within 0.92 seconds on Mac OS.

This is pretty impressive if you compare this to [RPi](https://fhackts.wordpress.com/2016/01/09/benchmarking-gstreamer-opengl-performance-on-raspberrypi/). What an unfair comparison...

# Pipeline module processing time

The [marking](https://github.com/mpekar/gstreamer_timestamp_marking) library is helpful to get the execution time with `markin` and `markout`. The [fork](https://github.com/yashi/gstreamer_timestamp_marking/commit/1955b6e9b1011774d848f53b039d5c2111247feb) fixed the autoconf compilation issue.

A typical usage:
```
gst-launch-1.0 -v --gst-debug=markout:5 --gst-plugin-path=$HOME/repos/gstreamer_timestamp_marking/src avfvideosrc num-buffers=300 ! videoconvert ! markin ! faceblur profile=/usr/local/Cellar/opencv/2.4.13/share/OpenCV/haarcascades/haarcascade_frontalface_alt2.xml ! markout ! videoconvert ! osxvideosink sync=true
```

I've been using this to measure the processing time of OpenCV `faceblur`. On Macbook Pro 2015, it's somewhere around 10ms (using resolution: 320x240).


Three terminals:

`A -> B (face detect box) -> C`

- A: `gst-launch-1.0 avfvideosrc ! x264enc tune="zerolatency" threads=1 ! tcpserversink port=4444`
- B: `gst-launch-1.0 tcpclientsrc port=4444 host=localhost ! h264parse ! avdec_h264 ! videoconvert ! facedetect ! videoconvert ! x264enc tune="zerolatency" threads=1 ! tcpserversink port=5555`
- C: `gst-launch-1.0 tcpclientsrc port=5555 host=localhost ! h264parse ! avdec_h264 ! glimagesink`


