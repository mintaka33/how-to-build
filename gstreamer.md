
# notes

## 0. reference

https://gstreamer.freedesktop.org/documentation/frequently-asked-questions/git.html

https://blogs.igalia.com/vjaquez/2018/04/17/how-to-setup-a-gst-build-environment-with-intels-va-api-stack/

## 1. get source code

code repository

https://gitlab.freedesktop.org/gstreamer

https://github.com/GStreamer

```bash
for module in gstreamer gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gstreamer-vaapi; do
  git clone https://gitlab.freedesktop.org/gstreamer/$module ;
done
```

## 2. build instructions

```bash
#install prerequisites
sudo apt install autopoint bison flex gtk-doc-tools

# build gstreamer
cd PROJECT_DIR
mkdir build
cd build && mkdir gst && cd gst
../../gstreamer/autogen.sh
../../gstreamer/configure
make -j8
sudo make install

# build gst-plugin-base
cd PROJECT_DIR
cd build && mkdir gst-base && cd gst-base
../../gst-plugins-base/autogen.sh
../../gst-plugins-base/configure
make -j8
sudo make install

# build gst-plugin-good
cd PROJECT_DIR
cd build && mkdir gst-good && cd gst-good
../../gst-plugins-good/autogen.sh
../../gst-plugins-good/configure
make -j8
sudo make install

# build gst-plugin-bad
cd PROJECT_DIR
cd build && mkdir gst-bad && cd gst-bad
../../gst-plugins-bad/autogen.sh
../../gst-plugins-bad/configure
make -j8
sudo make install

# build gst-plugin-ugly
cd PROJECT_DIR
cd build && mkdir gst-ugly && cd gst-ugly
../../gst-plugins-ugly/autogen.sh
../../gst-plugins-ugly/configure
make -j8
sudo make install

# build gstreamer-vaapi
cd PROJECT_DIR
cd build && mkdir gst-vaapi && cd gst-vaapi
../../gstreamer-vaapi/autogen.sh
../../gstreamer-vaapi/configure
make -j8
sudo make install

```

## 3. run gstreamer

### **set environment variable to whitelist gst-vaapi**

```bash
export GST_VAAPI_ALL_DRIVERS=1
gst-inspect-1.0 vaapih264enc
```
### check vaapi plugins

```bash
gst-inspect-1.0 | grep vaapi

vaapi:  vaapijpegdec: VA-API JPEG decoder
vaapi:  vaapimpeg2dec: VA-API MPEG2 decoder
vaapi:  vaapih264dec: VA-API H264 decoder
vaapi:  vaapivc1dec: VA-API VC1 decoder
vaapi:  vaapivp8dec: VA-API VP8 decoder
vaapi:  vaapivp9dec: VA-API VP9 decoder
vaapi:  vaapih265dec: VA-API H265 decoder
vaapi:  vaapipostproc: VA-API video postprocessing
vaapi:  vaapidecodebin: VA-API Decode Bin
vaapi:  vaapisink: VA-API sink
vaapi:  vaapimpeg2enc: VA-API MPEG-2 encoder
vaapi:  vaapih265enc: VA-API H265 encoder
vaapi:  vaapijpegenc: VA-API JPEG encoder
vaapi:  vaapih264enc: VA-API H264 encoder
vaapi:  vaapih264feienc: VA-API H264 FEI Advanced encoder (Experimental)

```

### test gst-vaapi plugins

```bash
# h264 encode
gst-launch-1.0 -ev videotestsrc num-buffers=60 ! timeoverlay ! vaapih264enc ! mp4mux ! filesink location=test.mp4

# h264 decode
gst-launch-1.0 filesrc location=./test.mp4  ! qtdemux ! h264parse  ! vaapih264dec ! fakesink

```



























