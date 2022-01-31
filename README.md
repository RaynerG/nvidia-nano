# nvidia-nano
Setup for the NVIDIA Nano Developer's board, to enable use with RPi HQ Camera, OpenCV and YOLOv5.

# Disk imaging

Follow the documentation: https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-2gb-devkit#prepare

Ensure to update to most recent software versions if prompted.

# Install utility programs

Update gedit by running it on the command line:
```
gedit
```
Then Go to preferences and turn on line numbers, highlight current line and matching brackets, and set a colour mode.  Tab width can be set to 4.  Plugins including External Tools and Snippets can also be chosen.

Check the python version currently installed:
``` 
python --version
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
