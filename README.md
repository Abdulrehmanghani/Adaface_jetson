# Adaface_jetson
Face detection and recognition jetson nano

# OpenCV Installation and Camera Testing on Jetson Nano

This guide provides a step-by-step process for installing OpenCV with CUDA support and testing your camera on a Jetson Nano.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Setting up Python and Virtual Environment](#setting-up-python-and-virtual-environment)
3. [Creating a Swap File](#creating-a-swap-file)
4. [Installing Dependencies for OpenCV](#installing-dependencies-for-opencv)
5. [Downloading and Building OpenCV](#downloading-and-building-opencv)
6. [Installing jtop System Monitor](#installing-jtop-system-monitor)
7. [Testing Your Camera](#testing-your-camera)
8. [Verifying OpenCV Installation](#verifying-opencv-installation)

---

## Prerequisites

- A Jetson Nano development kit.
- Connected CSI or USB camera.
- Sufficient disk space and RAM (recommended: use a swap file).

---

## Setting up Python and Virtual Environment

Run the following commands:

```
sudo apt-get install python3-pip
pip3 install virtualenv
python3 -m virtualenv -p python3 env --system-site-packages
source env/bin/activate
python -c 'import cv2; print(cv2.__version__)'
```
## Creating a Swap File
A swap file helps handle memory-intensive operations during OpenCV build:
```
sudo fallocate -l 4G /var/swapfile
sudo chmod 600 /var/swapfile
sudo mkswap /var/swapfile
sudo swapon /var/swapfile
sudo bash -c 'echo "/var/swapfile swap swap defaults 0 0" >> /etc/fstab'
sudo reboot
```
After reboot, verify the swap space:
```
free -h
```
## Installing Dependencies for OpenCV
Install the necessary libraries and tools using the following commands:
```
sudo apt-get install build-essential cmake git unzip pkg-config
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libgtk2.0-dev libcanberra-gtk*
sudo apt-get install python3-dev python3-numpy python3-pip
sudo apt-get install libxvidcore-dev libx264-dev libgtk-3-dev
sudo apt-get install libtbb2 libtbb-dev libdc1394-22-dev
sudo apt-get install libv4l-dev v4l-utils
sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
sudo apt-get install libavresample-dev libvorbis-dev libxine2-dev
sudo apt-get install libfaac-dev libmp3lame-dev libtheora-dev
sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt-get install libopenblas-dev libatlas-base-dev libblas-dev
sudo apt-get install liblapack-dev libeigen3-dev gfortran
sudo apt-get install libhdf5-dev protobuf-compiler
sudo apt-get install libprotobuf-dev libgoogle-glog-dev libgflags-dev
sudo sh -c "echo '/usr/local/cuda/lib64' >> /etc/ld.so.conf.d/nvidia-tegra.conf"
sudo ldconfig
```

## Downloading and Building OpenCV
Step 1: Download OpenCV Source Code
```
cd ~
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
unzip opencv.zip
unzip opencv_contrib.zip
mv opencv-4.5.1 opencv
mv opencv_contrib-4.5.1 opencv_contrib
rm opencv.zip opencv_contrib.zip
```
Step 2: Build OpenCV
```
cd ~/opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
      -D EIGEN_INCLUDE_PATH=/usr/include/eigen3 \
      -D WITH_OPENCL=OFF \
      -D WITH_CUDA=ON \
      -D CUDA_ARCH_BIN=5.3 \
      -D CUDA_ARCH_PTX="" \
      -D WITH_CUDNN=ON \
      -D WITH_CUBLAS=ON \
      -D ENABLE_FAST_MATH=ON \
      -D CUDA_FAST_MATH=ON \
      -D OPENCV_DNN_CUDA=ON \
      -D ENABLE_NEON=ON \
      -D WITH_QT=OFF \
      -D WITH_OPENMP=ON \
      -D WITH_OPENGL=ON \
      -D BUILD_TIFF=ON \
      -D WITH_FFMPEG=ON \
      -D WITH_GSTREAMER=ON \
      -D WITH_TBB=ON \
      -D BUILD_TBB=ON \
      -D BUILD_TESTS=OFF \
      -D WITH_EIGEN=ON \
      -D WITH_V4L=ON \
      -D WITH_LIBV4L=ON \
      -D OPENCV_ENABLE_NONFREE=ON \
      -D INSTALL_C_EXAMPLES=OFF \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D BUILD_NEW_PYTHON_SUPPORT=ON \
      -D BUILD_opencv_python3=TRUE \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D BUILD_EXAMPLES=OFF ..
make -j4
sudo make install
sudo ldconfig
```
## Verifying OpenCV Installation
Open Python3 and verify the installation:
```
python3
import cv2
print(cv2.__version__)
```
