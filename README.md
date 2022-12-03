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


# Connecting Intel's RealSense Depth Camera
```
$ git clone https://github.com/IntelRealSense/librealsense.git
$ cd ./librealsense
$ mkdir build
$ cd build
$ make -DFORCE_RSUSB_BACKEND=ON -DBUILD_PYTHON_BINDINGS:bool=true -DPYTHON_EXECUTABLE=/usr/bin/python3 -DCMAKE_BUILD_TYPE=release -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=true -DBUILD_WITH_CUDA:bool=true ..
```
Then
```
make -j4
sudo make install
```
Add `PATH`, make sure correct python version is named.
```
export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.8/pyrealsense2
export PYTHONPATH=/usr/local/lib/python3.8/pyrealsense2
export PYTHONPATH=$PYTHONPATH:/usr/local/lib
```

* [link1](https://cognitivexr.at/blog/2021/07/29/installing-pyrealsense2-nvidia-jetson-xavier-nx.html)
* [link2](https://jstar0525.tistory.com/97)
* [link3](https://github.com/IntelRealSense/librealsense/issues/7722)
* [link4](https://github.com/35selim/RealSense-Jetson)

## Possible Errors
Could NOT find `OpenSSL`, try to set the path to OpenSSL root folder in the system variable OPENSSL_ROOT_DIR (missing: OPENSSL_CRYPTO_LIBRARY OPENSSL_INCLUDE_DIR)
```
$ sudo apt-get install libssl-dev
```
The `Xinerama` headers were not found
```
$ sudo apt-get install xorg-dev libglu1-mesa-dev
```
if you get an error related to `RandR` then install followin packeage first.

```
sudo apt-get install xorg-dev libglu1-mesa-dev
```
