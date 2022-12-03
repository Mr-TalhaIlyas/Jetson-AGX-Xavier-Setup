# Jetson-AGX-Xavier-Setup

## Host machine vs Targeted Jetson board

1. Install Jetson SDK from the [link1](https://docs.nvidia.com/jetson/jetpack/install-jetpack/index.html) or [link2](https://developer.nvidia.com/drive/sdk-manager). 
* Install it on host machine. 
* Run the SDK by `sdkmanager` command.
* Follow the instructions on GUI one by one.
* Put your Jetson board in force recovery mode by presseing power and recovery button simultaneously.
It'll take sometime depending on internet and board speed.

2. Install Pytorch:
* First download the `.whl` file from [link](https://developer.nvidia.com/embedded/downloads#?search=jetpack).
* Follow the step by step instructions on [link](https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform/index.html)
* Becarfull of the file paths.

3. Type `python3` in terminal to verify installation.
```
import torch
torch.cuda.is_available()
```
If you get path error then follow steps [here](https://javachipd.medium.com/setting-up-pytorch-on-nvidia-jetson-boards-f7c297f56747) or [nano](https://qengineering.eu/install-pytorch-on-jetson-nano.html)


4. Connecting Intel's RealSense Depth Camera
* [link1](https://cognitivexr.at/blog/2021/07/29/installing-pyrealsense2-nvidia-jetson-xavier-nx.html)
* [link2](https://jstar0525.tistory.com/97)

if you get an error related to `RandR` then install followin packeage first.

```
sudo apt-get install xorg-dev libglu1-mesa-dev
```
