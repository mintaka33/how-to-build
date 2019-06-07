# how-to-build

## build ffmpeg
https://github.com/mintaka33/ffva/blob/master/notes.md

https://github.com/neptune46/ffmpeg-vscode/blob/master/README.md


## build MSDK
https://github.com/Intel-Media-SDK/MediaSDK#how-to-build

```bash
# vpp scaling + csc
./sample_vpp -lib hw -vaapi -sw 320 -sh 240 -dw 640 -dh 480 -scc nv12 -dcc rgbp -i test.yuv -o out.yuv
# ffmpeg doesn't support rgbp but gbrp
ffplay -s 640x480 -pix_fmt gbrp -i out.rgb 
```

## build opencl
https://github.com/mintaka33/vaapi-opencl-interop/blob/master/README.md


## build opencv
https://github.com/mintaka33/deeplearning-encode/blob/master/build_opencv.md

https://github.com/mintaka33/opencv-caffe-cpp/blob/master/build_opencv.md

https://github.com/mintaka33/deeplearning-notes/blob/master/build-opencv.md


## build openvino
https://github.com/mintaka33/intel-openvino-setup/blob/master/linux-setup.md


## build intel-gpu-tools
https://github.com/mintaka33/i915-notes/blob/master/intel-gpu-tools.md


## build linux kernel
https://github.com/mintaka33/build-linux-kernel/blob/master/README.md


## build mesa 3d driver
https://github.com/mintaka33/build-mesa/blob/master/README.md


## build caffe
https://github.com/neptune46/caffe-notes/blob/master/README.md

