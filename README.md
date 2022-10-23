

### 使用教程

##### 1、前提条件：本文使用环境为Ubuntu18.04+ROS-melodic ，需要自己建立一个工作空间，src功能包如下
<div align="center">
  <img src="https://github.com/Hahalim2022y/slam_test/blob/main/%E5%9B%BE%E7%89%87/%E5%B7%A5%E4%BD%9C%E7%A9%BA%E9%97%B4.png">
</div>


<img src="[https://github.com/Hahalim2022y/slam_test/图片/工作空间.png](https://github.com/Hahalim2022y/slam_test/blob/main/%E5%9B%BE%E7%89%87/%E5%B7%A5%E4%BD%9C%E7%A9%BA%E9%97%B4.png)" alt="工作空间" style="zoom: 80%;" />

##### 2、下载turtlebot3机器人模型  

安装turtlebot3所需要的依赖

```c++
sudo apt install ros-melodic-desktop-full ros-melodic-joy ros-melodic-teleop-twist-joy ros-melodic-teleop-twist-keyboard ros-melodic-laser-proc ros-melodic-rgbd-launch ros-melodic-depthimage-to-laserscan ros-melodic-rosserial-arduino ros-melodic-rosserial-python ros-melodic-rosserial-server ros-melodic-rosserial-client ros-melodic-rosserial-msgs ros-melodic-amcl ros-melodic-map-server ros-melodic-move-base ros-melodic-urdf ros-melodic-xacro ros-melodic-compressed-image-transport ros-melodic-rqt-image-view ros-melodic-gmapping ros-melodic-navigation ros-melodic-interactive-markers ros-melodic-turtlebot3-gazebo
```

在slam_test（你的工作空间）/src中下载turtlebot3源码

```c++
git clone https://gitee.com/qianlong_workspace/turtlebot3.git
git clone https://gitee.com/qianlong_workspace/turtlebot3_msgs.git
git clone https://gitee.com/qianlong_workspace/turtlebot3_simulations.git
```

catkin_make编译工作空间

在.bashrc最后一行加上

```c++
export TURTLEBOT3_MODEL=burger
```

##### 3、下载gmapping

安装gmapping依赖

```c++
sudo apt-get install libsdl1.2-dev
sudo apt install libsdl-image1.2-dev
```

在slam_test（你的工作空间）/src中下载gmapping源码

```c++
git clone https://github.com/ros-perception/openslam_gmapping.git
git clone https://github.com/ros-perception/slam_gmapping.git
git clone https://github.com/ros-planning/navigation.git
git clone https://github.com/ros/geometry2.git
```

```
sudo apt-get install ros-melodic-gmapping
```

##### 4、下载cartographer

cartographer的安装比较麻烦，可以参考网上教程

##### 5、下载navigation

安装依赖

```
sudo apt-get install ros-melodic-map-server
sudo apt-get install ros-melodic-tf2-geometry-msgs
sudo apt-get install ros-melodic-tf2-sensor-msgs
sudo apt-get install ros-melodic-move-base-msgs
```

在slam_test（你的工作空间）/src中下载navigation源码

```c++
#二进制安装
sudo apt install ros-melodic-navigation
#源码安装
git clone https://gitee.com/liwuhaoooo/navigation.git  
```

catkin_make编译工作空间

##### 6、下载gazebo_tutorials（正篇开始）

在slam_test（你的工作空间）/src中下载gazebo_tutorials源码

```
git clone https://github.com/Hahalim2022y/slam_test.git
```

catkin_make编译工作空间

###### 6.1、启动仿真环境

```c++
roslaunch gazebo_tutorials create_world.launch
```

###### 6.2、放置机器人

```
roslaunch gazebo_tutorials place_robot.launch
```

<img src="C:\Users\庞昀懿\Desktop\robomaster\图片\仿真1.png" alt="仿真1" style="zoom:80%;" />

###### 6.3、使用gmapping

```
roslaunch slam_gmapping gmapping.launch
```

###### 6.4、使用键盘

```
ROS_NAMESPACE=tb3_0 rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```

###### 6.5、打开rviz

```
rviz
```

在终端运行`rviz`，点击左下角add，在by topic中找到map添加进去，即可看到构建的地图，rviz也可以保存配置，这里先不保存，等后面多台机器人的时候再详细讲，包括坐标的显示、坐标系的选择等。
fixed_frame选择为tb3_0/map，即将世界坐标系设置为地图坐标

<img src="C:\Users\庞昀懿\Desktop\robomaster\图片\仿真2.png" alt="仿真2" style="zoom:80%;" />

###### 6.6、保存地图

```
ROS_NAMESPACE=tb3_0 rosrun map_server map_saver -f ~/slam_test/src/gazebo_tutorials/map/map
```

###### 6.7、使用amcl和navigation

当获取了地图后，把之前打开的终端全部关掉，统一用一个launch文件打开，避免繁琐

```
roslaunch gazebo_tutorials navigate_with_map.launch
```

2d pos estimate在这里不需要使用，因为我们的初始位置是我们自己设定的，基本是准确的。但是如果在使用中，我们可能会把机器人摆歪，那么激光雷达的扫描结果就和地图边缘不匹配，这时候就要使用这个工具调整位姿。发布位姿的时候要使用右上角的tool properties，这个在吗默认的rviz中是不会显示的，需要手动添加。然后目前的topic是没有命名空间的情况下，所以要在前面加上**`tb3_0/ `**代表是第一台机器人位姿估计。

2d Nav Goal就是发布希望机器人的目标地点，同样需要修改右上角topic来选定导航的机器人。


<img src="C:\Users\庞昀懿\Desktop\robomaster\图片\仿真3.png" alt="仿真3" style="zoom:80%;" />

###### 6.8、rqt_graph和tf

我们可以看到，是gazebo这个环境直接给gmapping节点提供了scan这个雷达数据的topic

![节点1](C:\Users\庞昀懿\Desktop\robomaster\图片\节点1.png)

tf变化树

<img src="C:\Users\庞昀懿\Desktop\robomaster\图片\tf1.png" alt="tf1"  />

