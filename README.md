# Demo for running Yolov5 and Yolov8 Networks with GPU support
This was developed in the context of the Robocup team at Humboldt University Berlin (https://github.com/BerlinUnited/NaoTH)

## Setup

### Ubuntu Setup
The lab PC has an RTX A4000
I installed the nvidia graphics driver from the graphical software updater that comes with ubuntu and followed the installation instructions from
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/

To make sure that docker can connect to the GPU I installed container according to the guide from
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

### WSL Setup
I did this at home where I have a Geforce RTX 3060.
https://docs.nvidia.com/cuda/wsl-user-guide/index.html


## Dataset creation
For training we need to create a dataset in the ultralytics yolo format first. I prepared a demo for that at https://github.com/StellaASchlotter/datumaro-demo

## Train a detection model

docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash

yolo detect train model=yolov8n.pt data=coco128.yaml device=0
yolo detect train model=yolov8n.pt data=coco128.yaml device=cpu

## Run inference on a model on yolov8
docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash

yolo predict model=yolov8n.pt source='test.jpg' save=True

## Run inference on a model on yolov5
B-Human trained a network capable of detecting balls and robots in RoboCup SPL GoPro Data. They used an older version of ultralytics yolo for that.

Download the bhuman model:
```
wget -O best.pt https://github.com/bhuman/VideoAnalysis/raw/4efd1f399dd4cdc40a619ba25d97ea6b2e30411f/weights/best.pt
```

Start the docker container. The current folder is mounted in work. This is done so t hat the model and the inference data can be found. Additionally we also mount the current folder to `/usr/src/app/runs/` so that we can see the result.
```
docker run -it --privileged -v ${PWD}:/work -v ${PWD}:/usr/src/app/runs/ --gpus all --ipc host ultralytics/yolov5:latest /bin/bash
```
Inside the container run the inference like this:
```
python detect.py --weights /work/best.pt --source /work/test.jpg 
```