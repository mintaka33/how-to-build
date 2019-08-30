# Reference
https://github.com/OpenVisualCloud/Dockerfiles
https://github.com/OpenVisualCloud/Dockerfiles/blob/master/XeonE3/ubuntu-18.04/analytics/ffmpeg/Dockerfile
https://github.com/OpenVisualCloud/Dockerfiles/blob/master/doc/ffmpeg.md

# Docker image on dockerhub
https://cloud.docker.com/u/mintaka33/repository/docker/mintaka33/ffmpeg-va-filter
```bash 
docker pull mintaka33/ffmpeg-va-filter:ver1
```

# Run docker image
```bash
docker run --privileged -it --device=/dev/dri \
-v ~/data/work/gst_va_plugin/gva/data/models/intel:/home/intel_models:ro \
-v ~/data/work/gst_va_plugin/gva/data/models/common:/home/common_models:ro \
-v ~/data/work/gst_va_plugin/gva/data/video:/home/video-examples:ro \
-e MODELS_PATH=/home/intel_models:/home/common_models \
-e VIDEO_EXAMPLES_DIR=/home/video-examples \
xeone3-ubuntu1804-analytics-ffmpeg:latest
```

# workloads on CPU
```bash
# ffmpeg decode VAAPI acceleration
ffmpeg -v verbose -hwaccel vaapi -hwaccel_output_format vaapi -i video-examples/test.mp4 -f null -


# decode+inference run on CPU
ffmpeg -v verbose -i video-examples/test.mp4 -vframes 200 \
-vf detect=model=./intel_models/r1/mobilenet-ssd.xml:model_proc=./intel_models/r1/mobilenet-ssd.json \
-an -f null /dev/null

ffmpeg -y -v verbose -i video-examples/test.mp4 \
-vf detect=model=./intel_models/r1/mobilenet-ssd.xml:model_proc=./intel_models/r1/mobilenet-ssd.json \
-an -f iemetadata out_meta.json

ffmpeg -y -v verbose -i video-examples/test.mp4 \
-vf classify=model=./intel_models/resnet50/resnet-50-128.xml:model_proc=./intel_models/resnet50/resnet50.json \
-an -f iemetadata out_meta.json

ffmpeg -y -v verbose -i video-examples/test.mp4 \
-vf "detect=model=./intel_models/r1/mobilenet-ssd.xml:model_proc=./intel_models/r1/mobilenet-ssd.json,\
classify=model=./intel_models/resnet50/resnet-50-128.xml:model_proc=./intel_models/resnet50/resnet50.json" \
-an -f iemetadata out_meta.json
```

# workloads on GPU
```bash
# decode+inference run on GPU

ffmpeg -v verbose -hwaccel vaapi -hwaccel_output_format vaapi -i video-examples/test.mp4 -vframes 20000 \
-vf detect=model=./intel_models/r1/mobilenet-ssd.xml:model_proc=./intel_models/r1/mobilenet-ssd.json:device=3 \
-an -f null /dev/null

ffmpeg -y -v verbose -i video-examples/test.mp4 \
-vf "detect=model=./intel_models/r1/mobilenet-ssd.xml:model_proc=./intel_models/r1/mobilenet-ssd.json:device=3,\
classify=model=./intel_models/resnet50/resnet-50-128.xml:model_proc=./intel_models/resnet50/resnet50.json:device=3" \
-an -f iemetadata out_meta.json
```
