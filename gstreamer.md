
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

### call stack

```
iHD_drv_video.so!do_exec2(mos_linux_bo * bo, int used, mos_linux_context * ctx, drm_clip_rect_t * cliprects, int num_cliprects, int DR4, unsigned int flags) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/os/i915/mos_bufmgr.c:2631)
iHD_drv_video.so!mos_gem_bo_context_exec2(mos_linux_bo * bo, int used, mos_linux_context * ctx, drm_clip_rect_t * cliprects, int num_cliprects, int DR4, unsigned int flags) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/os/i915/mos_bufmgr.c:2758)
iHD_drv_video.so!GpuContextSpecific::SubmitCommandBuffer(GpuContextSpecific * const this, PMOS_INTERFACE osInterface, PMOS_COMMAND_BUFFER cmdBuffer, bool nullRendering) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/os/mos_gpucontext_specific.cpp:902)
iHD_drv_video.so!Mos_Specific_SubmitCommandBuffer(PMOS_INTERFACE pOsInterface, PMOS_COMMAND_BUFFER pCmdBuffer, int32_t bNullRendering) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/os/mos_os_specific.c:3549)
iHD_drv_video.so!CodechalEncodeCscDs::DsKernel(CodechalEncodeCscDs * const this, CodechalEncodeCscDs::KernelParams * params) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encode_csc_ds.cpp:1781)
iHD_drv_video.so!CodechalEncodeCscDs::DsKernel(CodechalEncodeCscDs::KernelParams * params, CodechalEncodeCscDs * const this) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encode_csc_ds.cpp:1540)
iHD_drv_video.so!CodechalEncodeCscDs::KernelFunctions(CodechalEncodeCscDs * const this, CodechalEncodeCscDs::KernelParams * params) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encode_csc_ds.cpp:1239)
iHD_drv_video.so!CodechalEncodeAvcEnc::ExecuteKernelFunctions(CodechalEncodeAvcEnc * const this) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encode_avc.cpp:6252)
iHD_drv_video.so!CodechalEncoderState::ExecuteEnc(CodechalEncoderState * const this, EncoderParams * encodeParams) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encoder_base.cpp:4384)
iHD_drv_video.so!CodechalEncoderState::Execute(CodechalEncoderState * const this, void * params) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/agnostic/common/codec/hal/codechal_encoder_base.cpp:583)
iHD_drv_video.so!DdiEncodeAvc::EncodeInCodecHal(DdiEncodeAvc * const this, uint32_t numSlices) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/codec/ddi/media_ddi_encode_avc.cpp:1143)
iHD_drv_video.so!DdiEncodeBase::EndPicture(DdiEncodeBase * const this, VADriverContextP ctx, VAContextID context) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/codec/ddi/media_ddi_encode_base.cpp:77)
iHD_drv_video.so!DdiEncode_EndPicture(VADriverContextP ctx, VAContextID context) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/codec/ddi/media_libva_encoder.cpp:586)
iHD_drv_video.so!DdiMedia_EndPicture(VADriverContextP ctx, VAContextID context) (/home/fresh/data/work/media_driver/source/media-driver/media_driver/linux/common/ddi/media_libva.cpp:3244)
libva.so.2!vaEndPicture(VADisplay dpy, VAContextID context) (/home/fresh/data/work/media_driver/source/libva/va/va.c:1583)
libgstvaapi.so!gst_vaapi_enc_picture_encode(GstVaapiEncPicture * picture) (/home/fresh/data/work/gst_code/source/gstreamer-vaapi/gst-libs/gst/vaapi/gstvaapiencoder_objects.c:588)
libgstvaapi.so!gst_vaapi_encoder_h264_encode(GstVaapiEncoder * base_encoder, GstVaapiEncPicture * picture, GstVaapiCodedBufferProxy * codedbuf) (/home/fresh/data/work/gst_code/source/gstreamer-vaapi/gst-libs/gst/vaapi/gstvaapiencoder_h264.c:2907)
libgstvaapi.so!gst_vaapi_encoder_encode_and_queue(GstVaapiEncoder * encoder, GstVaapiEncPicture * picture) (/home/fresh/data/work/gst_code/source/gstreamer-vaapi/gst-libs/gst/vaapi/gstvaapiencoder.c:553)
libgstvaapi.so!gst_vaapi_encoder_put_frame(GstVaapiEncoder * encoder, GstVideoCodecFrame * frame) (/home/fresh/data/work/gst_code/source/gstreamer-vaapi/gst-libs/gst/vaapi/gstvaapiencoder.c:604)
libgstvaapi.so!gst_vaapiencode_handle_frame(GstVideoEncoder * venc, GstVideoCodecFrame * frame) (/home/fresh/data/work/gst_code/source/gstreamer-vaapi/gst/vaapi/gstvaapiencode.c:693)
libgstvideo-1.0.so.0!gst_video_encoder_chain(GstPad * pad, GstObject * parent, GstBuffer * buf) (/home/fresh/data/work/gst_code/source/gst-plugins-base/gst-libs/gst/video/gstvideoencoder.c:1557)
libgstreamer-1.0.so.0!gst_pad_chain_data_unchecked(void * data, GstPadProbeType type, GstPad * pad) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4333)
libgstreamer-1.0.so.0!gst_pad_push_data(GstPad * pad, GstPadProbeType type, void * data) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4589)
libgstreamer-1.0.so.0!gst_pad_push(GstPad * pad, GstBuffer * buffer) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4708)
libgstpango.so!gst_base_text_overlay_push_frame(GstBaseTextOverlay * overlay, GstBuffer * video_frame) (/home/fresh/data/work/gst_code/source/gst-plugins-base/ext/pango/gstbasetextoverlay.c:2334)
libgstpango.so!gst_base_text_overlay_video_chain(GstPad * pad, GstObject * parent, GstBuffer * buffer) (/home/fresh/data/work/gst_code/source/gst-plugins-base/ext/pango/gstbasetextoverlay.c:2815)
libgstreamer-1.0.so.0!gst_pad_chain_data_unchecked(void * data, GstPadProbeType type, GstPad * pad) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4333)
libgstreamer-1.0.so.0!gst_pad_push_data(GstPad * pad, GstPadProbeType type, void * data) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4589)
libgstreamer-1.0.so.0!gst_pad_push(GstPad * pad, GstBuffer * buffer) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gstpad.c:4708)
libgstbase-1.0.so.0!gst_base_src_loop(GstPad * pad) (/home/fresh/data/work/gst_code/source/gstreamer/libs/gst/base/gstbasesrc.c:2974)
libgstreamer-1.0.so.0!gst_task_func(GstTask * task) (/home/fresh/data/work/gst_code/source/gstreamer/gst/gsttask.c:328)
libglib-2.0.so.0![Unknown/Just-In-Time compiled code] (Unknown Source:0)
libpthread.so.0!start_thread(void * arg) (/build/glibc-OTsEL5/glibc-2.27/nptl/pthread_create.c:463)
libc.so.6!clone() (/build/glibc-OTsEL5/glibc-2.27/sysdeps/unix/sysv/linux/x86_64/clone.S:95)
```
