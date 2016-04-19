# sulcata

## Description
본 프로젝트는 우분투14.04, ROS 인디고, 거북이 로봇, 키넥트 v2를 활용하는 한 예를 제시하고자 작성 되었습니다. ([for International](README.md))

본 프로젝트는: 
- ROS 기반 로봇 제작에 있어서 최대한 간단하고 빠른 방법을 제시합니다.  
- SLAM과 네비게이션 프로젝트를 위한 최소한의 구성이 어떤지 확인 할 수 있습니다. 
- ROS 어플들을 실행하기 위한 간단한 명령어들을 제시 합니다. 

특히 키넥트 v1 또는 xtion 등의 비교적 구하기 어려운 RGBD 카메라를 키넥트 v2로 대체하고자 하는 분들께 도움이 될 수 있습니다.

(설카타는 육지 거북의 한 종류 입니다. 이 프로젝트가 다양한 거북이 로봇의 한 구현 예인 것에 착안하여 채택하였습니다. (<a href="https://en.wikipedia.org/wiki/African_spurred_tortoise" target="_blank">see also</a>))

## Maintainer
- [김성준](http://bus710.net)<<bus710@gmail.com>>

## Table of contents
- [Description](#description)
- [Hardware setting](#hardware-setting)
- [Software setting](#software-setting)
- [Launch](#launch)
- [Launch (alternative)] (#launch-alternative)
- [Todo list](#todo-list)
- [Reference](#reference)

## Hardware setting
인두기와 납땜하는 과정을 최소한으로 줄이도록 노력 했습니다. 공구와 간단한 관련 지식이 있다면 30분 안에 모든 과정을 마칠 수 있습니다.

![images/1.jpg](images/1.jpg)
![images/2.jpg](images/2.jpg)
![images/3.jpg](images/3.jpg)
![images/4.jpg](images/4.jpg)    

- 첫번째 이미지는 완성 되었을 때의 모습 입니다. 거북이를 구입할 때 옵션으로써 나무 패널과 알루미늄 지지대를 함께 구입할 수 있습니다. 
- 두번째 이미지는 키넥트와 거북이, 컴퓨터 간의 결선을 보여줍니다. 키넥트v1에 비해 v2는 더 많은 전력을 요구하는 것으로 알려져 있기에 여기서는 거북이의 12V/5A 출력을 키넥트의 전원으로 사용하기로 했습니다. v2의 전원선을 잘라보면 예상과는 다르게, 둘로 나뉘어진 그물 케이블만으로 구성 된 것을 확인할 수 있습니다. 그것이 각각 + 전압과 GND 선인 것을 테스터기로 확인할 수 있습니다. 따라서 그 부분을 납땜을 통해 준비된 커넥터 (Molex PN : 5566-02B2)에 연결함으로써 쉽게 거북이에 연결할 수 있습니다. 납땜은 이것으로 끝!  
- 세번째 이미지는 컴퓨터와 거북이, 키넥트가 각각 USB 케이블로 연결 된 것을 보여 줍니다. 주의할 점은, 키넥트v2의 특성 상, USB 3.0 단자를 사용해 주어야 한다는 점 입니다.
- 네번째 이미지는 키넥트를 패널에 고정하는 방법을 보여 줍니다 ([구입처](http://www.amazon.com/Smallrig%C2%AE-Screw-Adapter-Quick-Release/dp/B006GB5MDW)). 키넥트v2는 하단에 너트 소켓이 있는데, 일반적인 카메라 삼각대와 호환되는 사이즈 입니다. 따라서, 패널에 적절한 직경(10 mm)의 구멍을 내주고, 카메라 마운트용 볼트로 쉽게 고정해 줄 수 있습니다.

추가로, 본 예제에 사용된 컴퓨터는 인텔의 3세대 I5 모바일 CPU, 내장 그래픽 HD4000, 8GB의 램으로 구성 되었습니다. 요즘에 있어서는 크게 고사양은 아니지만, 어쨋거나 반드시 우분투 14.04와 호환이 잘 되는 시스템을 사용하시길 권장 합니다.  

## Software setting
본 프로젝트는 우분투 14.04에 의존하고 있습니다. 따라서 독자들께서 이미 우분투 14.04를 설치/사용하고 있는 것을 가정하도록 하겠습니다. 

아래에서는 주요 소프트웨어를 설치하는 방법을 최대한 간단히 안내 합니다만, 가급적이면 한번쯤은 관련 웹페이지를 확인하시길 추천 드립니다.

- Install ROS Indigo desktop full version. ([see also](http://wiki.ros.org/indigo/Installation/Ubuntu))
일단 ROS를 설치하는 방법 입니다. 설치에 큰 어려움은 없겠지만, 저장소의 키 값이 종종 바뀌거나 모종의 이유로 다운로드가 불가능할 경우가 있습니다. 그럴 때는 원래의 가이드에서 키값을 다시 확인하거나, 나중에 설치함으로써 해결할 수 있습니다.
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net --recv-key 0xB01FA116
sudo apt-get update
sudo apt-get install ros-indigo-desktop-full

sudo rosdep init
rosdep update
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt-get install python-rosinstall
```

- Set up ROS environment.
ROS의 설치를 마치면, ROS 앱을 다운 받고 컴파일하는 장소를 마련해야 합니다. 또한 단축키를 추가하는 방법도 적어 두었습니다.
```
cd ~
mkdir catkin_ws
cd catkin_ws
catkin_make  

echo "source $HOME/catkin_ws/devel/setup.bash" >> ~/.bashrc
echo "alias cw='cd ~/catkin_ws'"
echo "alias cs='cd ~/catkin_ws/src'"
echo "alias cm='cw && catkin_make'"
source ~/.bashrc
```

- Install Kobuki packages ([see also](http://wiki.ros.org/turtlebot))
거북이 패키지는 실제 제품 또는 시뮬레이터 상의 거북이를 제어하기 위한 드라이버와 어플리케이션을 포함하고 있습니다. 
```
sudo apt-get install ros-indigo-kobuki* 
```

- Install urg_node package ([see also](http://wiki.ros.org/urg_node))
Although this package is designed for 2D LRF sensor, you can leverage for your Konect2
```
sudo apt-get install ros-indigo-urg-node 
```

- Install depthimage-to-laserscan package ([see also](http://wiki.ros.org/depthimage_to_laserscan))
This package is the key to convert Kinect's 3D data to 2D data to make map around your robot.
```
sudo apt-get install ros-indigo-depthimage-to-laserscan
```

- Install rosbook_kobuki repository ([see also](https://github.com/oroca/rosbook_kobuki.git))
This package, which is developed by Dr.Pyo, is the core of various Kobuki based SLAM and navigation projects. 
```
git clone https://github.com/oroca/rosbook_kobuki.git
cm
```

- Modify kobuki_slam.launch ([see also](http://cafe.naver.com/openrt/11728))  
Since 'kobuki_slam' was originally designed for 2D LRF data, you should modify a launch file, whici is 'kobuki_slam.launch' in the repository. The lines, which is related to "urg_node", should be commentted.
```
<launch>
#<node pkg="urg_node" type="urg_node" name="kobuki_urg_node" output="screen">
	#<param name="frame_id" value="base_scan" />
#</node>
<node pkg="kobuki_tf" type="kobuki_tf" name="kobuki_tf" output="screen">
</node>
``` 

- Install libfreenect2 package ([see also](https://github.com/OpenKinect/libfreenect2))
This package provides a driver and a test application for Kinect v2. In order to take advantage of GPGPU, this package's installation steps explain for several GPUs from different vendors such as Nvidia-GTX/Tegra, AMD-Radeon, Intel-HD, ARM-Mali. However, below lines only show for Intel-HD GPUs. 
```
# download & install
cd
git clone https://github.com/OpenKinect/libfreenect2.git
cd libfreenect2
cd depends; ./download_debs_trusty.sh
sudo apt-get install build-essential cmake pkg-config
sudo dpkg -i debs/libusb*deb
sudo apt-get install libturbojpeg libjpeg-turbo8-dev
sudo dpkg -i debs/libglfw3*deb; sudo apt-get install -f; sudo apt-get install libgl1-mesa-dri-lts-vivid
sudo apt-add-repository ppa:floe/beignet; sudo apt-get update; sudo apt-get install beignet-dev; sudo dpkg -i debs/ocl-icd*deb
sudo dpkg -i debs/{libva,i965}*deb; sudo apt-get install -f
sudo apt-get install libopenni2-dev

# build
cd ..
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/freenect2
make
make install

# test (unplug Kinect v2 USB code and plug it again.) 
sudo cp ../platform/linux/udev/90-kinect2.rules /etc/udev/rules.d/
./bin/Protonect
```

- Install iai_kinect2 package ([see also](https://github.com/code-iai/iai_kinect2))
In order to connect freenect2 driver and ROS, you should install this package. 
```
cd ~/catkin_ws/src/
git clone https://github.com/code-iai/iai_kinect2.git
cd iai_kinect2
rosdep install -r --from-paths .
cd ~/catkin_ws
catkin_make -DCMAKE_BUILD_TYPE="Release"
```

## Launch
Now, the stack is ready to run 'kobuki_slam'. If the hardware and software are well installed, you can see the image attahced below. Each command should be run in new terminals (or tmux). If you use a better GPU than mine (HD5200 or later), you can change 'sd' to 'hd' or 'qhd' from the line for 'depthimage_to_laserscan'.
```
# launch apps in target system 
roscore
roslaunch kobuki_node minimal.launch --screen
roslaunch kinect2_bridge kinect2_bridge.launch publish_tf:=true
rosrun depthimage_to_laserscan depthimage_to_laserscan image:=/kinect2/sd/image_depth_rect _output_frame_id:=/base_scan
roslaunch kobuki_slam kobuki_slam.launch
rosrun rviz rviz -d `rospack find kobuki_slam`/rviz/kobuki_slam.rviz 

# launch apps in remote system
roslaunch kobuki_keyop keyop.launch
```

## Launch (alternative)
Since the launch commands are pretty complex, this repository provides simple scripts in the scripts directory.
```
# install this repository in target system
cd ~/Download
git clone https://github.com/bus710/sulcata
cd scripts

# launch apps in target system
roscore
./01_kobuki
./02_kinect_bridge
./03_depth_laserscan
./04_kobuki_slam
./05_rviz

# launch apps in remote system
./09_keyop
```

![images/run_1.png](images/run_1.png)

## Todo list
Now you have the working sample for your project. What will you try after this? I might try these goals in the future. If you are interested or want to suggest something, please do PR or leave issues.  
- Actual SLAM and navigation usage will be written.
- Modify for Ubuntu 16.04.
- Trouble shooting for issues.  

## Reference
[1] http://wiki.ros.org/Books/ROS_Robot_Programing   
[2] https://github.com/oroca/rosbook_kobuki  
[3] https://github.com/OpenKinect/libfreenect2  
[4] https://github.com/code-iai/iai_kinect2  
[5] http://wiki.ros.org/turtlebot  


