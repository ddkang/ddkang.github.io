---
layout: post
title: Installing CUDA 10.0, CuDNN 7.4.1, TensorRT 5.0.1 on Google Compute Engine
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
sudo apt install gnupg-curl # needed for adding the key

mkdir install ; cd install
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo apt update
time sudo apt install cuda -y
```


### Step 2: Installing CuDNN (~2 minutes)

1. Download CuDNN [here](https://developer.nvidia.com/rdp/cudnn-download) (BOTH the runtime and dev, deb). Use version 7.4.1.
2. scp the deb files to GCP
3.
```
sudo dpkg -i libcudnn7_7.4.1.5-1+cuda10.0_amd64.deb
sudo dpkg -i libcudnn7-dev_7.4.1.5-1+cuda10.0_amd64.deb
```


### Step 3: Installing TensorRT (~2 minutes)

1. Download TensorRT [here](https://developer.nvidia.com/nvidia-tensorrt-5x-download). Use version
   5.0.2.
2. scp the deb files to GCP.
3.
```
sudo dpkg -i nv-tensorrt-repo-ubuntu1604-cuda10.0-trt5.0.2.6-ga-20181009_1-1_amd64.deb
sudo apt install tensorrt libnvinfer5
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


### Step 4: Run the ResNet-50 example

NOTE: In order to achieve close to the benchmark numbers [here](https://developer.nvidia.com/deep-learning-performance-training-inference),
the memcpy needs to be disabled, and the working set size needs to be changed to 16GB.

```
sudo apt-get update
sudo apt install libprotobuf-dev protobuf-compiler cmake -y

# Install ONNX
git clone --recursive https://github.com/onnx/onnx.git
cd onnx
mkdir build ; cd build
cmake ..
make -j8
sudo make install -j8

# Try the ResNet-50 example
cd ../../
git clone https://github.com/parallel-forall/code-samples.git
cd code-samples/posts/TensorRT-introduction
wget https://s3.amazonaws.com/onnx-model-zoo/resnet/resnet50v2/resnet50v2.tar.gz
tar xvf resnet50v2.tar.gz

./simpleOnnx_1 resnet50v2/resnet50v2.onnx resnet50v2/test_data_set_0/input_0.pb

./simpleOnnx_2 resnet50v2/resnet50v2.onnx resnet50v2/test_data_set_*/input_0.pb

BATCH_SIZE=41 ; for x in `seq 1 $BATCH_SIZE`; do echo resnet50v2/test_data_set_0/input_0.pb ; done  | xargs ./simpleOnnx resnet50v2/resnet50v2.onnx
```
