---
layout: post
title: Installing CUDA 10.1, CuDNN 7.6.3, TensorRT 5.0.1 on AWS, Ubuntu 18.04
comments: true
author: Daniel Kang
---

How to install CUDA 9.2, CuDNN 7.2.1, PyTorch nightly on Google Compute Engine. I expect this to be
outdated when PyTorch 1.0 is released (built with CUDA 10.0).

This uses Conda, but pip should ideally be as easy.


### Step 0: GCP setup (~1 minute)

Create a GCP instance with 8 CPUs, 1 P100, 30 GB of HDD space with Ubuntu 16.04. Turn off host
migration (GPU jobs can't be resumed).


### Step 1: Installing CUDA (~5.5 minutes)

You can also install CUDA directly from the offline installer, but this is a little easier.

```
sudo apt update
sudo apt upgrade -y

mkdir install ; cd install
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda-10-1
```


### Step 2: Installing CuDNN (~2 minutes)

1. Download CuDNN [here](https://developer.nvidia.com/rdp/cudnn-download) (BOTH the runtime and dev, deb). Use version 7.6.3.
2. scp the deb files to GCP
3.
```
sudo dpkg -i libcudnn7_7.6.3.30-1+cuda10.1_amd64.deb
sudo dpkg -i libcudnn7-dev_7.6.3.30-1+cuda10.1_amd64.deb
```


### Step 3: Installing TensorRT (~2 minutes)

1. Download TensorRT [here](https://developer.nvidia.com/nvidia-tensorrt-6x-download). Use version
   6.0.1.
2. scp the deb files to AWS.
3.
```
sudo dpkg -i nv-tensorrt-repo-ubuntu1804-cuda10.1-trt6.0.1.5-ga-20190913_1-1_amd64.deb
sudo apt update
sudo apt install tensorrt libnvinfer6
```

### Step 3.5: Add to .bashrc

I don't actually know if this step is required, but it might be helpful for other things.
```
export CUDA_HOME=/usr/local/cuda
export DYLD_LIBRARY_PATH=$CUDA_HOME/lib64:$DYLD_LIBRARY_PATH
export PATH=$CUDA_HOME/bin:$PATH
export C_INCLUDE_PATH=$CUDA_HOME/include:$C_INCLUDE_PATH
export CPLUS_INCLUDE_PATH=$CUDA_HOME/include:$CPLUS_INCLUDE_PATH
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
export LD_RUN_PATH=$CUDA_HOME/lib64:$LD_RUN_PATH
```
