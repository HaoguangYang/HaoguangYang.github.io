---
layout: post
category: SetupLog
title: Setting up Machine Learning and Vision Environments in Ubuntu 16.04.
tagline: by Yang
tags: 
  - OpenCV
  - TensorFlow
  - Torch
  - Linux
  - AgileVehicle
---

This workstation is used as the on-board computer of the Agile Vehicle project, featuring the processing of various vehicle dynamic algorithms and computer vision issues for autopilot. 

<!--more-->

## Hardware Parameters

|Item|Value|
|---|---|
|CPU|Intel® Xeon(R) CPU E3-1245 v5 @ 3.50GHz, 3.90GHz Max × 4 Cores, 8 Threads|
|RAM|32.0 GiB DDR4 Dual channel|
|GPU|NVidia GeForce GTX TITAN X /PCIe/SSE2; Intel HD Graphics P530|
|OS|Ubuntu 16.04.1 LTS amd64|
|HDD|256 GiB for OS, 2 TB for massive data storage|

## Install NVidia Drivers

```sh
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
```

Press Enter to Continue

(RECOMMENDED!) Use "extra drivers" tab on the software update panel of the system GUI to switch to latest NVIDIA drivers.

Or:

```sh
sudo apt-get install nvidia-378
```

Install extra softwares that improves performance and compatibility and reboot: 

```sh
sudo apt-get install mesa-common-dev
sudo apt-get install freeglut3-dev
sudo reboot
```

## Download and Install CUDA 8.0

```sh
sudo sh cuda_your_version_linux.run –tmpdir=/tmp/
```

Do NOT install default drivers
When finished, add these lines to `~/.bashrc` :

```sh
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

To check if CUDA and NVidia driver are running correctly, run:

```sh
nvidia-smi
```

## Download and Install CUDNN

```sh
tar xvzf cudnn-your_version-prod.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

## Download and Install TensorFlow

### Install Bazel

```sh
sudo apt-get install python-numpy swig python-dev python-wheel​​
```

refer to http://www.bazel.io/docs/install.html
Check that this line is added to `~/.bashrc` :
```sh
export PATH="$PATH:$HOME/bin"​
```

### Download TensorFlow source

```sh
​​git clone https://github.com/tensorflow/tensorflow​
cd tensorflow
./configure
```

### Making tests

```sh
bazel build -c opt --config=cuda //tensorflow/cc:tutorials_example_trainer
```

> If error occurs, run alternatively:

> ```sh
> bazel build -c opt --config=cuda //tensorflow/cc:tutorials_example_trainer​ --verbose_failures
> ```

and then:

```sh
bazel-bin/tensorflow/cc/tutorials_example_trainer –use_gpu​
```

This should output the eigen values of a 2x2 matrix and it should converge.

### Build pip installer and install

```sh
bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg​
sudo pip install /tmp/tensorflow_pkg/tensorflow-0.9.0-py2-none-any.whl
```

## Install Torch

This will automatically install OpenBLAS at `/opt/OpenBLAS` .

```sh
git clone https://github.com/torch/distro.git ~/torch --recursive
cd ~/torch; bash install-deps;
./install.sh
```

Add these lines to `~/.bashrc` :

```sh
export OPENBLAS_NUM_THREADS=8
export OMP_NUM_THREADS=8
export LD_LIBRARY_PATH=/opt/OpenBLAS/lib${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

to test Torch, run

```
$ th

  ______             __   |  Torch7                                   
 /_  __/__  ________/ /   |  Scientific computing for Lua.         
  / / / _ \/ __/ __/ _ \  |                                           
 /_/  \___/_/  \__/_//_/  |  https://github.com/torch   
                          |  http://torch.ch            

th> torch.Tensor{1,2,3}
 1
 2
 3
[torch.DoubleTensor of dimension 3]

th>
```

## Download and Install OpenCV3.2.0

```sh
sudo apt-get install qt5-default cmake-qt-gui build-essential libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
git clone https://github.com/opencv/opencv.git
cd opencv; mkdir build; cd build 
```

Notice that `ffmpeg` and `libacvcodec-dev` must enable shared libraries to ensure the correct build of OpenCV from source.
Generate OpenCV makefile with using Cmake GUI, enable `CUDA`, `CuDNN`, `TBB`, `Qt`, `OpenCL`, `OpenGL`, `OpenBLAS`, and other necessary dependencies.
or use the terminal:

```sh
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D ENABLE_FAST_MATH=1 -D CUDA_FAST_MATH=1 -D WITH_CUBLAS=1 ..
```

then,

```sh
make -j16
sudo make install
```

> (Optional) Configure the library search path by creating the file `/etc/ld.so.conf.d/opencv.conf` and adding the following line.
> ```
> /usr/local/lib
> ```
> Add the following line to the `~/.bashrc` file
> ```
>  PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
> ```
> Reload the bashrc file for the current session
> ```
> source ~/.bashrc
> ```

## Install Caffe

```sh
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler
```

> For Debian and Ubuntu users, The lib names of `hdf5` on debian have a postfix "serial", therefore `-lhdf5` and `-lhdf5_hl` cannot be found.
> To solve it, I created two symbolics:
> ```sh
> cd /usr/lib/x86_64-linux-gnu
> sudo ln -s libhdf5_serial.so.8.0.2 libhdf5.so
> sudo ln -s libhdf5_serial_hl.so.8.0.2 libhdf5_hl.so
> ```

then download the NVIDIA-flavored Caffe:

```sh
git clone https://github.com/NVIDIA/caffe.git $CAFFE_ROOT
```

Modify certain lines of the `Makefile.config` as stated below:

```
6		USE_CUDNN := 1
27		OPENCV_VERSION := 3
54		BLAS := open
58		BLAS_INCLUDE := /opt/OpenBLAS/include
59		BLAS_LIB := /opt/OpenBLAS/lib
68		MATLAB_DIR := /opt/MATLAB/R2016a
87		PYTHON_LIB := /usr/lib
95		WITH_PYTHON_LAYER := 1
98		INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
99		LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```

run:

```sh
make clean
protoc src/caffe/proto/caffe.proto –cpp_out=.
mkdir include/caffe/proto
mv src/caffe/proto/caffe.pb.h include/caffe/proto
sudo pip install -r $CAFFE_ROOT/python/requirements.txt
make -j16; make test -j16; make runtest; make pycaffe; make distribute
```

Add the following lines to `~/.bashrc` :

```sh
export LD_LIBRARY_PATH=/usr/local/caffe/lib${LD_LIBRARY_PATH:export PYTHONPATH=/usr/local/caffe/python:$PYTHONPATH
export CAFFE_HOME=/usr/local/caffe
```

## Install Digits

Add these line to ~/.bashrc

```sh
export DIGITS_ROOT=/usr/local/digits
alias digits_server_start=/usr/local/digits/digits-devserver
```

then run:

```sh
source ~/.bashrc
sudo mkdir /usr/local/digits; sudo chmod 777 /usr/local/digits
sudo apt-get install --no-install-recommends git graphviz python-dev python-flask python-flaskext.wtf python-gevent python-h5py python-numpy python-pil python-pip python-protobuf python-scipy
git clone https://github.com/NVIDIA/DIGITS.git $DIGITS_ROOT
sudo pip install -r $DIGITS_ROOT/requirements.txt
sudo pip install -e $DIGITS_ROOT
digits_server_start
```

if successful, it should appeare like this:

```
  ___ ___ ___ ___ _____ ___
 |   \_ _/ __|_ _|_   _/ __|
 | |) | | (_ || |  | | \__ \
 |___/___\___|___| |_| |___/ 5.1-dev
```
 
