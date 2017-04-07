How to install Caffe on Ubuntu 16.04 with GPU (Cuda 8.0.61, CuDNN 6.0)

(Tested on GeForce GT 635M and GeForce GTX 660)



install nvidia drivers (go to software&updates - choose last proprietary tested) - not needed if use cuda.run file 

download cuda 8.0 https://developer.nvidia.com/cuda-release-candidate-download

download cudnn 6.0 Library for Linux https://developer.nvidia.com/rdp/cudnn-download


```shell

sudo apt-get install -y build-essential cmake git pkg-config

sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler

sudo apt-get install -y libatlas-base-dev

sudo apt-get install -y --no-install-recommends libboost-all-dev

sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

sudo apt-get install -y python-pip

sudo apt-get install -y python-dev

sudo apt-get install -y python-numpy python-scipy

sudo apt-get install -y libopencv-dev
```

CUDA

```shell

#for cuda.deb (I got Error Unmet dependencies for last vesrion of cuda 8.0.61 so better download .run file):
#sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
#sudo apt-get update
#sudo apt-get install cuda
#sudo apt-get install cuda-drivers

#for cuda.run file (follow the on-screen prompts):

sudo sh cuda_8.0.61_375.26_linux.run

#next for both

echo 'export PATH=/usr/local/cuda-8.0/bin:$PATH' » ~/.bashrc

echo 'export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

#restart terminal

cd /usr/local/cuda-8.0/samples/

sudo make all -j8
```

CUDNN

```shell

#create folder in home directory. for example

mkdir ~/code

cd ~/code

tar -xzvf cudnn-8.0-linux-x64-v5.1.tgz #or extract cudnn file

cd cuda/lib64/

sudo cp lib* /usr/local/cuda-8.0/lib64/

cd cuda/include/

sudo cp cudnn.h /usr/local/cuda-8.0/include/
```

CAFFE

```shell

cd code

git clone https://github.com/BVLC/caffe.git

cd caffe/python/

for req in $(cat requirements.txt); do sudo -H pip install $req --upgrade; done

sudo apt-get update

#export PYTHONPATH to your python folder in caffe folder (don't forget to write your user name and folder correctly)
#for example:

echo 'export PYTHONPATH=/home/user/code/caffe/python:$PYTHONPATH' >> ~/.bashrc

cd ..

cp Makefile.config.example Makefile.config


#check make.config file in caffe folder:
```
uncomment: 

>USE_CUDNN := 1 

>WITH_PYTHON_LAYER := 1

change: 

>CUDA_DIR := /usr/local/cuda-8.0

>PYTHON_INCLUDE := /usr/include/python2.7 /usr/lib/python2.7/dist-packages/numpy/core/include 

>INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial

>LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial

```shell
cd caffe/build

cmake ..

cd code/caffe

make all -j8

make test

sudo reboot

make runtest

cd code/caffe

make pycaffe

make distribute

python

import caffe
```
CONGRATS!
