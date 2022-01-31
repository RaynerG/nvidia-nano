# nvidia-nano
Setup for the NVIDIA Nano Developer's board, to enable use with RPi HQ Camera, OpenCV and YOLOv5.

# Disk imaging

Follow the documentation: https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-2gb-devkit#prepare

Ensure to update to most recent software versions if prompted.

# Install utility programs

Customise gedit by running it on the command line:
```
gedit
```
Then Go to preferences and turn on line numbers, highlight current line and matching brackets, and set a colour mode.  Tab width can be set to 4.  Plugins including External Tools and Snippets can also be chosen.


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
If required, complete and installation:
``` 
sudo apt install python 3.8-dev
```
This build may take a while.

# Install recent version of OpenCV

Use the script file provided, or go to the original repo: https://github.com/mdegans/nano_build_opencv

Create the file called ```build_opencv.sh``` and copy the contents into it.  Make it executable by:
``` 
chmod +x build_opencv.sh
```
Then run it:
```
./build_opencv.sh
```
This should build version 4.1.0 of OpenCV, with CUDA optimisations.  Will take quite some time, and requires some user installation confirmation to finalise.

Another resource that may prove useful can be found at this link: https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html

# Install PyTorch and torchvision

TODO for installation of YOLO
