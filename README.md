# nvidia-nano
Setup for the NVIDIA Nano Developer's board, to enable use with RPi HQ Camera, OpenCV and YOLOv5.

# Disk imaging

Follow the documentation: https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-2gb-devkit#prepare

Ensure to update to most recent software versions if prompted.  I am running JetPack 4.6

# Configuration

Optionally, remove un-needed LibreOffice, which is not required for deep learning:
``` 
sudo apt-get purge libreoffice*
sudo apt-get clean
```
Now ensure the board is fully updated:
``` 
sudo apt-get update && sudo apt-get upgrade
```

Optionally, customise gedit by running it on the command line:
```
gedit
```
Then Go to preferences and turn on line numbers, highlight current line and matching brackets, and set a colour mode.  Tab width can be set to 4.  Plugins including External Tools and Snippets can also be chosen.

*Importantly*, create a virtual environment by first installing:
```
sudo apt-get install python3-venv
```
Create a project directory and move to it, then create and activate the virtual env:
```
python3 -m venv venv_name
source venv_name/bin/activate
deactivate
```
Later on, a list of requirements for the virtual environment can be generated by:
```
pip3 freeze > requirements.txt
```
From here, ensure all software packages are installed while in this virtual env.

An optional tool to give better information of the status of the NVidia Nano is jtop, which should be installed while not in the virtual environment:
```
sudo -H pip install -U jetson-stats
sudo reboot
# run the app 
jtop
```

#  Configuring and running RPi HQ Camera

Go to the following page to enable the CSI camera port: https://docs.nvidia.com/jetson/archives/l4t-archived/l4t-3261/index.html#page/Tegra%20Linux%20Driver%20Package%20Development%20Guide/hw_setup_jetson_io.html#wwpID0E02D0HA
If required, navigate to the following section:
NVIDIA Jetson Linux Driver Package Software Features > Hardware Setup > Configuring Jetson Expansion Header > Running Jetson-IO > Configuring the CSI connector

Run the following: 
```
sudo /opt/nvidia/jetson-io/jetson-io.py
```
Navigate to Configure Jeston Nano CSI Connector > Configure for Compatible Hardware > Camera IMX477 > Save pin changes > Save and reboot to reconfigure pins

After reboot, test the operation by running:

```
nvgstcapture-1.0
```
This should display a camera window, press ctrl+c to exit.  More details in the docs: https://developer.nvidia.com/embedded/learn/tutorials/first-picture-csi-usb-camera

The following repo has some good basic programs to test the camera and nano working together: https://github.com/JetsonHacksNano/CSI-Camera

# Install recent version of python
Check the python version currently installed:
``` 
python --version
```
It should be fine to use 3.6  If required, complete and installation:
``` 
sudo apt install python 3.8-dev
```
This build may take a while.

Now install pip:
```
sudo apt install python3-pip
pip3 --version
pip3 install --upgrade pip
pip3 --version
```
This installs pip3, and ensures that it is updated to the latest version.

# Install PyTorch and Torchvision into virtual environment

Note that this is based on Jetpack v4.6, and follows the official instructions found at [this link](https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-10-now-available/72048).

These packages were installed in a virtual environment created as outlined above.

I chose to install PyTorch v1.8.0 and Torchvision v0.9.0 in the hope of compatibility.  To choose different versions, modify the links and version numbers in the commands, as outlined in the original instructions.
```
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl
sudo apt-get install python3-pip libopenblas-base libopenmpi-dev 
pip3 install Cython
pip3 install numpy==1.19.4 
pip3 install torch-1.8.0-cp36-cp36m-linux_aarch64.whl
rm torch-1.8.0-cp36-cp36m-linux_aarch64.whl
```
Note that it is important to install Numpy v1.19.4, as 1.19.5 has conflicts that cause a seg fault.

Now for torchvision:
```
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
git clone --branch v0.9.0 https://github.com/pytorch/vision torchvision
cd torchvision
export BUILD_VERSION=0.9.0
python3 setup.py install 
cd ../
```
Now verification can be completed by starting python3 and running the following:
```
import torch
print(torch.__version__)
print('CUDA available: ' + str(torch.cuda.is_available()))
print('cuDNN version: ' + str(torch.backends.cudnn.version()))
a = torch.cuda.FloatTensor(2).zero_()
print('Tensor a = ' + str(a))
b = torch.randn(2).cuda()
print('Tensor b = ' + str(b))
c = a + b
print('Tensor c = ' + str(c))

import torchvision
print(torchvision.__version__)
```
Should be good to go now!


# Install PyTorch (hard version)

This version wasn't working in the virtual environment.  The wheel may be incompatible.

Following the guide found at this link: https://qengineering.eu/install-pytorch-on-jetson-nano.html

First, install the dependencies, and then the pre-made installation wheel:
```
sudo apt-get install python3-pip libjpeg-dev libopenblas-dev libopenmpi-dev libomp-dev
sudo -H pip3 install future
sudo pip3 install -U --user wheel mock pillow
sudo -H pip3 install testresources
# above version 58.3.0 there may be version issues
sudo -H pip3 install setuptools==58.3.0
sudo -H pip3 install Cython
# install gdown to download from google drive
sudo -H pip3 install gdown
gdown https://drive.google.com/uc?id=1TqC6_2cwqiYacjoLhLgrZoap6-sVL2sd
# install torch
sudo -H pip3 install torch-1.10.0a0+git36449ea-cp36-cp36m-linux_aarch64.whl
# clean up
rm torch-1.10.0a0+git36449ea-cp36-cp36m-linux_aarch64.whl
```
You can check successful install by the following:
``` 
python3
import torch as tr
tr.__version__
print(tr.rand(3,4))
print(tr.hypot(tr.tensor([1.]),tr.tensor([1.])))
exit()
```

# Install PyTorch (easy version)

This version may not use the correct compiler, which may cause unstable behaviour and some errors.

Following the instructions from the following two links:
https://github.com/ultralytics/yolov5/issues/2524
https://forums.developer.nvidia.com/t/illegal-instruction-core-dumped/165488/6

```
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl
sudo apt-get install python3-pip libopenblas-base libopenmpi-dev
pip3 install Cython
pip3 install numpy=1.19.4 torch-1.8.0-cp36-cp36m-linux_aarch64.whl
rm torch-1.8.0-cp36-cp36m-linux_aarch64.whl
```
Note that numpy1.19.5 causes a seg fault, so we are using 1.19.4

# Install Torchvision

Now install torchvision:
```
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo pip3 install -U pillow
sudo -H pip3 install gdown
gdown https://drive.google.com/uc?id=1C7y6VSIBkmL2RQnVy8xF9cAnrrpJiJ-K
sudo -H pip3 install torchvision-0.11.0a0+fa347eb-cp36-cp36m-linux_aarch64.whl
rm torchvision-0.11.0a0+fa347eb-cp36-cp36m-linux_aarch64.whl
```
You can verify successful installation by the following:
``` 
python3
import torchvision as tv
tv.__version__
exit()
```

# Install PyTorch and Torchvision (official version)

Follow instructions here:
https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-10-now-available/72048

Now install PyTorch v1.10.0:
```
wget https://nvidia.box.com/shared/static/fjtbno0vpo676a25cgvuqc1wty0fkkg6.whl -O torch-1.10.0-cp36-cp36m-linux_aarch64.whl
sudo apt-get install python3-pip libopenblas-base libopenmpi-dev
pip3 install Cython
pip3 install numpy torch-1.10.0-cp36-cp36m-linux_aarch64.whl
```
Now install Torchvison v0.11.1
```
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
git clone --branch v0.11.1 https://github.com/pytorch/vision torchvision
cd torchvision
export BUILD_VERSION=0.11.1
python3 setup.py install --user
cd ../
```
To verify, start python3 and do:
```
import torch
print(torch.__version__)
print('CUDA available: ' + str(torch.cuda.is_available()))
print('cuDNN version: ' + str(torch.backends.cudnn.version()))
a = torch.cuda.FloatTensor(2).zero_()
print('Tensor a = ' + str(a))
b = torch.randn(2).cuda()
print('Tensor b = ' + str(b))
c = a + b
print('Tensor c = ' + str(c))

import torchvision
print(torchvision.__version__)
```

# Install OpenCV4 into virtual environment

These instructions install OpenCV 4.5.1, based on the following links:
https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html
https://www.pyimagesearch.com/2020/03/25/how-to-configure-your-nvidia-jetson-nano-for-computer-vision-and-deep-learning/
https://forums.developer.nvidia.com/t/install-opencv-for-python3-in-jetson-nano/74042/17

```
sudo apt-get update
sudo apt-get upgrade
# install light text editor
sudo apt-get install nano
sudo apt-get install dphys-swapfile
# increase swapfile size by changing variable CONF_MAXSWAP=4096
sudo nano /sbin/dphys-swapfile
# update this file also
sudo nano /etc/dphys-swapfile
sudo reboot
```
Install pre-requisites:
```
# check memory availabilty, need at least 6.5GB
free -m
# check CUDA location
sudo sh -c "echo '/usr/local/cuda/lib64' >> /etc/ld.so.conf.d/nvidia-tegra.conf"
sudo ldconfig
# install libraries
sudo apt-get install build-essential cmake git unzip pkg-config zlib1g-dev
sudo apt-get install libjpeg-dev libjpeg8-dev libjpeg-turbo8-dev
sudo apt-get install libpng-dev libtiff-dev libglew-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libgtk2.0-dev libgtk-3-dev libcanberra-gtk*
sudo apt-get install python-dev python-numpy python-pip
sudo apt-get install python3-dev python3-numpy python3-pip
sudo apt-get install libxvidcore-dev libx264-dev libgtk-3-dev
sudo apt-get install libtbb2 libtbb-dev libdc1394-22-dev libxine2-dev
sudo apt-get install gstreamer1.0-tools libgstreamer-plugins-base1.0-dev
sudo apt-get install libgstreamer-plugins-good1.0-dev
sudo apt-get install libv4l-dev v4l-utils v4l2ucp qv4l2
sudo apt-get install libtesseract-dev libxine2-dev libpostproc-dev
sudo apt-get install libavresample-dev libvorbis-dev
sudo apt-get install libfaac-dev libmp3lame-dev libtheora-dev
sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt-get install libopenblas-dev libatlas-base-dev libblas-dev
sudo apt-get install liblapack-dev liblapacke-dev libeigen3-dev gfortran
sudo apt-get install libhdf5-dev libprotobuf-dev protobuf-compiler
sudo apt-get install libgoogle-glog-dev libgflags-dev
```
**Now** activate your virtual environment, then move to root and start download / install of opencv.  
```
# example command to activate virtual environment, for my system
source ~/projectdir/venvdir/bin/activate
# move to root
cd ~
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
unzip opencv.zip
unzip opencv_contrib.zip
# give them better names
mv opencv-4.5.1 opencv
mv opencv_contrib-4.5.1 opencv_contrib
# clean up zip files
rm opencv.zip
rm opencv_contrib.zip
```
Now build make.
```
cd ~/opencv
mkdir build
cd build
```
Run cmake with all the following flags, and the `..` at the end.  I actually didn't use the -D CMAKE_INSTALL_PREFIX line, but I think it should be fine to use.
```
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
```
Now, run make.  This will run for quite some time, mine took about 5 hours and seemed to be stuck on 100% build for ages, before finalising.  I think it ran out of RAM.
```
make -j4
```
Now you can remove any pre-installed version of opencv, and install the packages on the system.  Be sure to run this in the `~/opencv/build/` folder.
```
sudo rm -r /usr/include/opencv4/opencv2
sudo make install
sudo ldconfig
# clean up to free a few hundred MB
make clean
sudo apt-get update
```
Now, arguably the most important part to get opencv running in your virtual environment.  Create a symbolic link to the package location:
```
cd /usr/local/lib/python3.6/site-packages/cv2/python-3.6
# give it a good name
mv cv2.cpython-36m-xxx-linux-gnu.so cv2.so
# go to your virtual environment python interpreter location
cd ~/projectdir/venvdir/lib/python3.6/site-packages/
# you could just link it to the native library for system wide visibility (?)
cd ~
# create symlink
ln -s /usr/local/lib/python3.6/site-packages/cv2/python-3.6/cv2.so cv2.so
```
That's about it.  You could follow the optional clean-up in the q-engineering article, but I won't outline that here.  You can confirm that it is working by starting `python3` in the shell and running:
```
import cv2
cv2.__version__
```
Fingers crossed, it will say `4.5.1` :))

# Install other dependencies for YOLOv5

Assuming that the above listed installations of python3, pytorch and torchvision have been successful, the remaining requirements can be installed as follows.

# Finalisation

To capture the dependencies required for the project, do the following in the project directory:
```
pip3 freeze > requirements.txt
```
This will enable restoration if required.

