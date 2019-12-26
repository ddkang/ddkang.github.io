---
layout: post
title: Installing CUDA 10.2, CuDNN 7.6.5, TensorRT 7.0 on AWS, Ubuntu 18.04
comments: true
author: Daniel Kang
---

How to install CUDA 10.2, CuDNN 7.6.5, TensorRT 7.0 in the AWS T4 instance.



### Step 0: AWS setup (~1 minute)

Create a `g4dn.xlarge` AWS instance. Attach at least 30 GB of HDD space with Ubuntu 18.04.


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
sudo apt-get -y install cuda-10-2
```


### Step 2: Installing CuDNN (~2 minutes)

1. Download CuDNN [here](https://developer.nvidia.com/rdp/cudnn-download) (BOTH the runtime and dev, deb). Use version 7.6.5.
2. scp the deb files to AWS
3.
```
sudo dpkg -i libcudnn7_7.6.5.32-1+cuda10.2_amd64.deb
sudo dpkg -i libcudnn7-dev_7.6.5.32-1+cuda10.2_amd64.deb
```


### Step 3: Installing TensorRT (~2 minutes)

1. Download TensorRT [here](https://developer.nvidia.com/nvidia-tensorrt-7x-download). Use version
   7.0.
2. scp the deb files to AWS.
3.
```
sudo dpkg -i nv-tensorrt-repo-ubuntu1804-cuda10.2-trt7.0.0.11-ga-20191216_1-1_amd64.deb
sudo apt update
sudo apt install tensorrt libnvinfer7
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
