# Deepstream & TensorRT Beginner Guideline
Both of NVIDIA deepstream and tensorRT have been relatively new tools for computer vision filed, especially in term of video processing in the last 2 years. This git will help reader to clarify how to install deepstream and tensorRT on your local computer with NVIDIA graphic cards installed. There is also an example how to run tools as well.

## Deepstream installation on local GPU
However, on the NVIDIA official website there are instruction detailed. But there is some steps that we need to learn from errors ourselves. So, I summarize the important steps to complete the Deepstream installation.

**Prerequisite**
- [ ] Ubuntu version 18.04
- [ ] NVIDIA graphic driver verion 440 above
- [ ] CUDA driver version 10.2
- [ ] GStreamer version 1.14.1
- [ ] TensorRT version 7.0 above

**1. Remove previous NVIDIA & CUDA drivers**
This can help to avoid conflict when you install mismatch version of each driver.
```
sudo apt purge *nvidia*
sudo apt autoremove
sudo apt autoremove --purge 'cuda*'
```

**2. Install GStreamer**
It is a library for creating media and managing a wide variety of multimedia which it can support many file formats.
```
sudo apt install build-essential git

sudo apt install \
libssl1.0.0 \
libgstreamer1.0-0 \
gstreamer1.0-tools \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly \
gstreamer1.0-libav \
libgstrtspserver-1.0-0 \
libjansson4
```
**3. Install NVIDIA graphic driver version 440 above**
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt upgrade
```
There is some related NVIDIA drivers which are neccessary to install, so run the following command to complete driver instalation
```
sudo ubuntu-drivers autoinstall
```
Reboot computer after finished installation.
```
sudo reboot
```
When Ubuntu boots, please check the drivers were installed correctly

```
lsmod | grep nvidia
```

**4. Install CUDA driver version 10.2 above**
It is bulit for parallel computing and API platform to enable developers to use parallel computing power of GPU efficiency.
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin

sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb

sudo dpkg -i cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb

sudo apt-key add /var/cuda-repo-10-2-local-10.2.89-440.33.01/7fa2af80.pub

sudo apt-get update

sudo apt-get -y install cuda
```
**5. install TensorRT 7.0**
It is a tool for increasing efficiency during the run time for maximum efficiency. It is designed for performance tuning before run with real problems.

Please login with NVIDIA account and download TensorRT 7.0 at https://developer.nvidia.com/nvidia-tensorrt-download (TensorRT 7.0.0.11 for Ubuntu 1804 and CUDA 10.2 DEB local repo packages)

```
sudo dpkg -i ./nv-tensorrt-repo-ubuntu1804-cuda10.2-trt7.0.0.11-ga-20191216_1-1_amd64.deb
sudo apt-key add /var/nv-tensorrt-repo-cuda10.2-trt7.0.0.11-ga-20191216/7fa2af80.pub
sudo apt-get update

sudo apt-get install tensorrt
sudo apt-get install uff-converter-tf
```
Then, chech the installation by grep TensorRT
```
dpkg -l | grep TensorRT
```

**6. Install librdkafka**
What is librdkafka, please see the documentation at https://github.com/edenhill/librdkafka

Download librdkafka repository
```
git clone https://github.com/edenhill/librdkafka.git
```

Configure & build librdkafka
```
cd librdkafka
git reset --hard 7101c2310341ab3f4675fc565f64f0967e135a6a
./configure
make
sudo make install
```

Copy created librdkafka into deepstream folder
```
sudo mkdir -p /opt/nvidia/deepstream/deepstream-5.0/lib
sudo cp /usr/local/lib/librdkafka* /opt/nvidia/deepstream/deepstream-5.0/lib
```

**7. Install Deepstream SDK**
Download Deepstream version 5.0 at https://developer.nvidia.com/deepstream-getting-started (select DeepStream 5.0 for Servers and Workstations)
```
sudo apt-get install ./deepstream_sdk_-5.0_amd64.deb
```

**8. Test Deepstream is running**
```
deepstream-app -c /$PATH_TO_YOUR_DEEPSTREAM_FOLDER/samples/configs/deepstream-app/source4_1080p_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt
```
**Issue found when running deepstream-app**
1. Failed to load plugin ‘/usr/lib/x86_64-linux-gnu/gstreamer-1.0/deepstream/libnvdsgst_inferserver.so’: libtrtserver.so
Please try to clear GStreramer cache by the following commands and re-launch again
```
rm -rf ${HOME}/.cache/gstreamer-1.0
```

Your deepstream-app should run normally, you will get a pop-up video result as shown in figure below.


The majority of content is refer from https://docs.nvidia.com/metropolis/deepstream/dev-guide/index.html
