# Demo for running Yolov5 and Yolov8 Networks with GPU support
This was developed as part of the robocup team at Humboldt University Berlin.

## Setup

### Ubuntu Setup
The lab PC has an RTX A4000
installed the nvidia graphics driver from the graphical software updater that comes with ubuntu

followed the installation instructions from
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/

container toolkit installation
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

### WSL Setup
I did this at home where I have ...
https://docs.nvidia.com/cuda/wsl-user-guide/index.html


## Dataset creation
For training we need to create a dataset in the ultralytics yolo format first. I prepared a demo for that at https://github.com/StellaASchlotter/Datumaro-Demo



## Train a detection model

docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash

yolo detect train model=yolov8n.pt data=coco128.yaml device=0
yolo detect train model=yolov8n.pt data=coco128.yaml device=cpu

## Run inference on a model on yolov8
docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash

yolo predict model=yolov8n.pt source='test.jpg' save=True

## Run inference on a model on yolov5
Download the bhuman model:
```
wget -O best.pt https://github.com/bhuman/VideoAnalysis/raw/4efd1f399dd4cdc40a619ba25d97ea6b2e30411f/weights/best.pt
```

docker run -it --privileged -v ${PWD}:/work -v ${PWD}:/usr/src/app/runs/ --gpus all --ipc host ultralytics/yolov5:latest /bin/bash
python detect.py --weights /work/best.pt --source /work/test.jpg 