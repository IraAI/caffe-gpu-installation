TESTED ON GeForce GT 635M AND GeForce GTX 660



#install nvidia drivers (go to software&updates - choose last proprietary tested)
#download cuda8 and Patch1 https://developer.nvidia.com/cuda-release-candidate-download
#download cudnn5.1 Library for Linux https://developer.nvidia.com/rdp/cudnn-download


sudo apt-get install -y build-essential cmake git pkg-config

sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler

sudo apt-get install -y libatlas-base-dev 

sudo apt-get install -y --no-install-recommends libboost-all-dev

sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

sudo apt-get install -y python-pip

sudo apt-get install -y python-dev

sudo apt-get install -y python-numpy python-scipy

sudo apt-get install -y libopencv-dev


#CUDA

sudo dpkg -i cuda-repo-ubuntu1604-8-0-rc_8.0.27-1_amd64.deb

sudo apt-get update

sudo apt-get install cuda

sudo apt-get install cuda-drivers

echo 'export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

#restart terminal

cd /usr/local/cuda-8.0/samples/

#open with software install 'cuda-misc-headers-8-0_8.0.27.1-1_amd64.deb' (Patch 1 from cuda toolkit page)

sudo make all -j8



#CUDNN

#create folder  in home directory. for example

mkdir ~/code 

cd ~/code

tar -xzvf cudnn-8.0-linux-x64-v5.1.tgz

cd cuda/lib64/

sudo cp lib* /usr/local/cuda-8.0/lib64/

cd cuda/include/

sudo cp cudnn.h /usr/local/cuda-8.0/include/



#CAFFE

mkdir ~/code 

cd code

git clone https://github.com/BVLC/caffe.git

cd caffe/python/

for req in $(cat requirements.txt); do sudo -H pip install $req --upgrade; done

sudo apt-get update

#export PYTHONPATH to your python folder in caffe folder. for example:

export PYTHONPATH=/home/null/code/caffe/python:$PYTHONPATH'

cd .. 

cp Makefile.config.example Makefile.config

#check make.config file in caffe folder:
'''
  uncomment:
    USE_CUDNN := 1
    WITH_PYTHON_LAYER := 1

  change:
    CUDA_DIR := /usr/local/cuda-8.0

    PYTHON_INCLUDE := /usr/include/python2.7 /usr/lib/python2.7/dist-packages/numpy/core/include 

    INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial

    LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
'''

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


CONGRATS!
