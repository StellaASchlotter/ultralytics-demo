# Demo for running Yolov5 and Yolov8 Networks with GPU support
This was developed in the context of the Robocup team at Humboldt University Berlin (https://github.com/BerlinUnited/NaoTH)

## Setup

### Ubuntu Setup on lab PC
The lab PC has an RTX A4000
I installed the nvidia graphics driver from the graphical software updater that comes with ubuntu and followed the installation instructions from
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/

To make sure that docker can connect to the GPU I installed container according to the guide from
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

### Ubuntu Setup on ball server
This server has
- Quadro K6000
- nvidia driver 418.87.00
- Cuda 10.1
apt purge *nvidia*, apt autoremove, apt autoclean
https://help.ubuntu.com/community/NvidiaDriversInstallation
Pytorch 2.0 need cuda 11.7 and that needs a minimum driver version of nvidia driver >= 450.80.02*#

mainboard dell 0CRH6C
https://www.game-debate.com/motherboard/index.php?mot_id=4413&motherboard=Dell%200CRH6C


### WSL Setup
I did this at home where I have a Geforce RTX 3060.
https://docs.nvidia.com/cuda/wsl-user-guide/index.html


## Dataset creation
For training we need to create a dataset in the ultralytics yolo format first. I prepared a demo for that at https://github.com/StellaASchlotter/datumaro-demo

## Train a detection model
This assumes your dataset is in the current folder under `my_ultralytics_dataset`.

Start the docker container and mount the current folder
```bash
docker run -it --privileged -v ${PWD}:/work -v ${PWD}:/usr/src/ultralytics/runs/ --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash
```
for training on cpu run:
```bash
yolo detect train model=yolov8n.pt data=coco128.yaml device=cpu
```
or train on gpu with
```bash
yolo detect train model=yolov8n.pt data=/work/my_ultralytics_dataset/data.yaml
```

## Run inference on a model on yolov8
docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash

yolo predict model=yolov8n.pt source='test.jpg' save=True

## Run inference on a model on yolov5
B-Human trained a network capable of detecting balls and robots in RoboCup SPL GoPro Data. They used an older version of ultralytics yolo for that.

Download the bhuman model:
```bash
wget -O best.pt https://github.com/bhuman/VideoAnalysis/raw/4efd1f399dd4cdc40a619ba25d97ea6b2e30411f/weights/best.pt
```

Start the docker container. The current folder is mounted in work. This is done so t hat the model and the inference data can be found. Additionally we also mount the current folder to `/usr/src/app/runs/` so that we can see the result.
```bash
docker run -it --privileged -v ${PWD}:/work -v ${PWD}:/usr/src/app/runs/ --gpus all --ipc host ultralytics/yolov5:latest /bin/bash
```
Inside the container run the inference like this:
```bash
python detect.py --weights /work/best.pt --source /work/input_images/test.jpg 
python detect.py --help
```

B-Human example output:
```
[[       1439         347        1504         432     0.95434           2]
 [        272         337         338         407     0.95189           1]
 [       1429         300        1484         375     0.94525           2]
 [       1500         303        1559         375     0.93351           2]
 [        744         300         795         387     0.86272           6]
 [       1005         235        1053         322     0.82992           2]
 [        892         292         932         384     0.81476           2]
 [        946         209         980         268     0.80729           6]
 [        900         272         941         359     0.78042           2]
 [        879         355         895         372     0.76559           0]
 [       1142         266        1181         342     0.72849           3]]
```