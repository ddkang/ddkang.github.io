---
layout: post
title: Installing CUDA 9.2, CuDNN TODO, PyTorch nightly on Google Compute Engine
comments: true
author: Daniel Kang
---

How to install CUDA 9.2, CuDNN TODO, PyTorch nightly on Google Compute Engine. I expect this to be
outdated when PyTorch 1.0 is released (built with CUDA 10.0).

This uses Conda, but pip should ideally be as easy.


### Step 0: GCP setup (~1 minute)

Create a GCP instance with 8 CPUs, 1 P100, 30 GB of HDD space with Ubuntu 16.04. Turn off host
migration (GPU jobs can't be resumed).


### Step 1: Installing CUDA (~5.5 minutes)

You can also install CUDA directly from the offline installer, but this is a little easier.

```
mkdir install ; cd install
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.2.148-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_9.2.148-1_amd64.deb
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo apt update
time sudo apt install cuda
```


### Step 2: Installing CuDNN (~2 minutes)

1. Download CuDNN [here](https://developer.nvidia.com/rdp/cudnn-download) (BOTH the runtime and dev, deb). Use version 7.2.1.
2. scp the deb files to GCP
3.
```
sudo dpkg -i libcudnn7_7.2.1.38-1+cuda9.2_amd64.deb
sudo dpkg -i libcudnn7-dev_7.2.1.38-1+cuda9.2_amd64.deb
```


### Step 3: Installing Conda / PyTorch nightly (~9 minutes)

Installing Conda (~3.5 minutes):
```
wget https://repo.anaconda.com/archive/Anaconda3-5.3.0-Linux-x86_64.sh
time bash Anaconda3-5.3.0-Linux-x86_64.sh
```

Making a new virtual environment, with Python 3.5 (~3.5 minutes):
```
conda create -n pytorch-default python=3.5 anaconda
```

Installing PyTorch nightly (~2 minutes):
```
conda install pytorch-nightly cuda92 -c pytorch
```
