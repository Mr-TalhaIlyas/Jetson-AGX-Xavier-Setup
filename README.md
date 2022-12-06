# Jetson-AGX-Xavier-PyRealsense2-PyTorch-ONNX-Setup

```cmd
Linux: 20.04
JetPack: 5.0.2
Python: 3.8.10
ONNX: 1.12.1
PyTorch: 1.13
PyRealSense2: Latest
```
## Host machine vs Targeted Jetson board

1. Install Jetson SDK from the [link1](https://docs.nvidia.com/jetson/jetpack/install-jetpack/index.html) or [link2](https://developer.nvidia.com/drive/sdk-manager). 
* Install it on host machine. 
* Run the SDK by `sdkmanager` command.
* Follow the instructions on GUI one by one.
* Put your Jetson board in force recovery mode by presseing power and recovery button simultaneously.
It'll take sometime depending on internet and board speed.

NOTE:
IF you are on Linux 20.04 then you can only install JetPack 5.0 or above.


## Install Pytorch:
* First download the `.whl` file from [link](https://developer.nvidia.com/embedded/downloads#?search=jetpack).
* Follow the step by step instructions on [link](https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform/index.html)
* Becarfull of the file paths.

## Install ONNX
Its better to convert to ONNX framwork before running on Jetson boards. You only have to install `onnxruntime` for that very light weight module. Train, finetune and convert your model using host machine.

Torch -> ONNX conversion [Example](https://pytorch.org/docs/stable/onnx.html)

### ONNX installation

```
pip install onnx
```
### ONNX Runtim Installation
* Go to [lilnk](https://onnxruntime.ai/)
* For Jetson Boards [link](https://elinux.org/Jetson_Zoo#ONNX_Runtime)

1. First downlaod the corresponding `pip` wheel for onnx, I used onnx 1.12.1 and python 3.8 version for jetpack > 5.0.2.
2. then run `pip3 install your_filename_here_onnxruntime_gpu.whl`
3. Type `python3` in terminal to verify installation.
```
import torch
torch.cuda.is_available()
```
If you get path error then follow steps [here](https://javachipd.medium.com/setting-up-pytorch-on-nvidia-jetson-boards-f7c297f56747) or [nano](https://qengineering.eu/install-pytorch-on-jetson-nano.html)


## Connecting Intel's RealSense Depth Camera
1. First install some libraries to avoid any probable errors

```
sudo apt-get update && sudo apt-get upgrade
cd ~
// get repo
git clone https://github.com/IntelRealSense/librealsense.git
cd librealsense && mkdir build && cd build
// I added few extra libs to be installed to abvoid any errors
sudo apt-get -y install \
                python3 \
                python3-dev \
                python3-pip \
                libssl-dev \
                libxinerama-dev \
                libxcursor-dev \
                libcanberra-gtk-module \
                libcanberra-gtk3-module \
                xorg-dev \
                libglu1-mesa-dev

```
First get repo,
```
git clone https://github.com/IntelRealSense/librealsense.git
cd ./librealsense
mkdir build
cd build
```
Set some PATH environment variables to tell the build script where CUDA is located: 
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin
```
Now start build
```
cmake -DFORCE_RSUSB_BACKEND=ON -DBUILD_PYTHON_BINDINGS:bool=true -DPYTHON_EXECUTABLE=/usr/bin/python3 -DCMAKE_BUILD_TYPE=release -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=true -DBUILD_WITH_CUDA:bool=true
```
Then, install
```
make -j4
sudo make install
```
Add `PATH`, make sure correct python version is named.
```
export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.8/pyrealsense2
```
Give Permissions
First `cd` back to main `dir`, than go to `config` dir.
```
sudo cp 99-realsense-libusb.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && udevadm trigger
```

Now you can run 

```
rs-enumerate-devices
```
and you'll see connected devices. For running application,

```
realsense-viewer
```
Python Binding
```
sudo mkdir /usr/local/lib/python3.8/pyrealsense2
sudo ln -s ~/librealsense/build/wrappers/python/pybackend2.cpython-38-aarch64-linux-gnu.so /usr/local/lib/python3.8/pyrealsense2/
sudo ln -s ~/librealsense/build/wrappers/python/pyrealsense2.cpython-38-aarch64-linux-gnu.so /usr/local/lib/python3.8/pyrealsense2/
```
Now you can run `python3` and call `import pyrealsense2 as rs`

```python

import pyrealsense2 as rs
import numpy as np
import cv2

width = 640
height = 360

pipeline = rs.pipeline()
config = rs.config()
config.enable_stream(rs.stream.depth, width, height, rs.format.z16, 30)
config.enable_stream(rs.stream.color, width, height, rs.format.bgr8, 30)

profile = pipeline.start(config)

depth_sensor = profile.get_device().first_depth_sensor()
depth_scale = depth_sensor.get_depth_scale()

print("Depth Scale is: ", depth_scale)

try:
    while True:
        frames = pipeline.wait_for_frames()
        depth_frame = frames.get_depth_frame()
        color_frame = frames.get_color_frame()
        if not depth_frame or not color_frame:
            continue

        # convert images to numpy arrays
        depth_image = np.asanyarray(depth_frame.get_data())
        color_image = np.asanyarray(color_frame.get_data())
        depth_colormap = cv2.applyColorMap(cv2.convertScaleAbs(depth_image, alpha=0.03), cv2.COLORMAP_JET)

        depth = depth_image[320,240].astype(float)*depth_scale

        cv2.imshow('rgb', color_image)
        cv2.imshow('depth', depth_colormap)
        print(f'Depth: {depth} m')

        if cv2.waitKey(1) == ord("q"):
            break
finally:
    pipeline.stop()
```

### References
* [link1](https://cognitivexr.at/blog/2021/07/29/installing-pyrealsense2-nvidia-jetson-xavier-nx.html)
* [link2](https://jstar0525.tistory.com/97)
* [link3](https://github.com/IntelRealSense/librealsense/issues/7722)
* [link4](https://github.com/35selim/RealSense-Jetson)
