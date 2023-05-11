# Graphics Card
Lab PC has RTX A4000



installed the nvidia graphics driver from the graphical software updater that comes with ubuntu

followed the installation instructions from
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/

container toolkit installation
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

# train detection
docker run -it --privileged -v ${PWD}:/work --gpus all --ipc host ultralytics/ultralytics:latest /bin/bash
yolo detect train model=yolov8n.pt data=coco128.yaml device=cpu
