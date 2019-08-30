# Reference

https://github.com/opencv/gst-video-analytics
https://github.com/opencv/gst-video-analytics/wiki
https://github.com/opencv/gst-video-analytics/wiki/Getting-Started-Guide-%5BR2%5D#install-in-docker-image
https://github.com/opencv/gst-video-analytics/wiki/Getting-Started-Guide-%5BR2%5D#run-samples
https://github.com/opencv/gst-video-analytics/tree/master/samples/shell

# Pull docker image from docker hub

https://cloud.docker.com/repository/docker/mintaka33/gst-va-plugin

```bash
docker pull mintaka33/gst-va-plugin:ver1
```


# Run docker image

```bash
sudo docker run -it --privileged --net=host \
\
-v ~/.Xauthority:/root/.Xauthority \
-v /tmp/.X11-unix:/tmp/.X11-unix \
-e DISPLAY=$DISPLAY \
-e HTTP_PROXY=$HTTP_PROXY \
-e HTTPS_PROXY=$HTTPS_PROXY \
-e http_proxy=$http_proxy \
-e https_proxy=$https_proxy \
\
-v ~/data/work/gst_va_plugin/gva/data/models/intel:/root/intel_models:ro \
-v ~/data/work/gst_va_plugin/gva/data/models/common:/root/common_models:ro \
-e MODELS_PATH=/root/intel_models:/root/common_models \
\
-v ~/data/work/gst_va_plugin/gva/data/video:/root/video-examples:ro \
-e VIDEO_EXAMPLES_DIR=/root/video-examples \
\
gstreamer-plugins:latest
```

# Run gstreamer plugin

### basic test

```bash
gst-inspect-1.0 | grep vaapi

gst-inspect-1.0 | grep gva

# h264 decode
gst-launch-1.0 filesrc location=./video-examples/test.mp4 ! qtdemux ! h264parse ! vaapih264dec ! fakesink

# h264 decode + display
gst-launch-1.0 filesrc location=./video-examples/test.mp4 ! qtdemux ! h264parse ! vaapih264dec !\
fpsdisplaysink video-sink=xvimagesink sync=false
```

### object detection with display

```bash
gst-launch-1.0 --gst-plugin-path ${GST_PLUGIN_PATH} filesrc location=./video-examples/test.mp4 ! \
decodebin ! video/x-raw ! videoconvert ! \
gvadetect model=./intel_models/mobilenet-ssd.xml device=CPU ! queue ! \
gvawatermark ! videoconvert ! fpsdisplaysink video-sink=xvimagesink sync=false

gst-launch-1.0 --gst-plugin-path ${GST_PLUGIN_PATH} filesrc location=./video-examples/test.mp4 ! \
qtdemux ! h264parse ! vaapih264dec ! video/x-raw ! videoconvert ! \
gvadetect model=./intel_models/mobilenet-ssd.xml device=GPU ! queue ! \
gvawatermark ! videoconvert ! fpsdisplaysink video-sink=xvimagesink sync=false
```

### object detection without display

```bash
gst-launch-1.0 --gst-plugin-path ${GST_PLUGIN_PATH} -v filesrc location=./video-examples/test.mp4 ! \
qtdemux ! h264parse ! vaapih264dec ! vaapipostproc ! video/x-raw,format=BGRA,width=300,height=300 ! \
gvadetect model=./intel_models/mobilenet-ssd.xml device=GPU every-nth-frame=1 batch-size=1 ! \
fpsdisplaysink video-sink=fakesink silent=true sync=false

gst-launch-1.0 --gst-plugin-path ${GST_PLUGIN_PATH} -v filesrc location=./video-examples/test.mp4 ! \
decodebin ! vaapipostproc ! video/x-raw,format=BGRA,width=300,height=300 ! \
gvadetect model=./intel_models/mobilenet-ssd.xml device=GPU every-nth-frame=2 batch-size=1 ! \
fpsdisplaysink video-sink=fakesink silent=true sync=false
```

### object detection + transcode

```bash
gst-launch-1.0 --gst-plugin-path ${GST_PLUGIN_PATH} filesrc location=./video-examples/test.mp4 ! \
decodebin ! video/x-raw ! videoconvert ! \
gvadetect model=./intel_models/mobilenet-ssd.xml device=GPU every-nth-frame=1 batch-size=1 ! queue ! \
gvawatermark ! videoconvert ! vaapih264enc ! filesink location=out.h264

# copy output file out of container
fresh@freshpc2:~$ sudo docker cp 2d880f2c3ac5:/root/out.h264 ~/
```
