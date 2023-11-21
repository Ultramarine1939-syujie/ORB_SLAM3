# ORB_SLAM3 for Ubuntu20.04.6 with ROS-Noetic

针对ubuntu20.04系统做了验证，gcc/g++版本默认分别为9和11，窗口环境为x11（默认已安装ros环境）

## 安装所需库

```bash
#依赖之依赖
sudo apt-get install libopencv-dev
sudo apt-get install ros-noekit-cv-bridge
sudo apt-get install build-essential libgtk2.0-dev libgtk-3-dev libavcodec-dev libavformat-dev libjpeg-dev libswscale-dev libtiff5-dev
sudo apt install python3-dev python3-numpy
sudo apt install libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
sudo apt install libpng-dev libopenexr-dev libtiff-dev libwebp-dev
sudo apt-get install libeigen3-dev
sudo apt-get install libglew-dev libboost-dev libboost-thread-dev libboost-filesystem-dev
sudo apt-get install ffmpeg libavcodec-dev libavutil-dev libavformat-dev libswscale-dev libpng-dev 
sudo apt install libpython2.7-dev
```

## 安装eigen3

```bash
sudo apt install libeigen3-dev
```

## 安装opencv（4.2.0）

```bash
sudo apt install libopencv-dev libopencv-highgui-dev libopencv-contrib-dev
```

## 安装Pangolin-0.6

复制到浏览器下载并解压缩：https://codeload.github.com/stevenlovegrove/Pangolin/zip/refs/tags/v0.6

```bash
cd Pangolin-0.6
mkdir build
cd build
cmake ..
make -j
sudo make install
```

测试是否安装成功

```bash
cd Pangolin
cd examples/HelloPangolin
mkdir build && cd build
cmake ..
make
./HelloPangolin
```

如果出现包含方块的窗口则安装成功

## 编译ORB-SLAM3

```bash
git clone https://github.com/UZ-SLAMLab/ORB_SLAM3.git
cd ORB_SLAM3
./build.sh
```

### Debug:

#### ORB_SLAM3/CMakeLists.txt

```cmake
#20
set(CMAKE_CXX_STANDARD 14)
#41
find_package(Eigen3 REQUIRED)
```

#### ORB_SLAM3/Examples_old/ROS/ORB_SLAM3/CMakeLists.txt

```cmake
#12 13
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3")
#41
find_package(Eigen3 REQUIRED)
#20
set(CMAKE_CXX_STANDARD 14)
#33
find_package(OpenCV 4.2.0 REQUIRED)
```

#### ORB_SLAM3/Thirdparty/DBoW2 /CMakeLists.txt

```cmake
#8 9
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3")
```

#### ORB_SLAM3/Thirdparty/g2o/CMakeLists.txt

```cmake
#57 58
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3")
#72
find_package(Eigen3 REQUIRED)
```

## ROS环境配置

```bash
sudo vim ~/.bashrc
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:~/ORB_SLAM3/Examples/ROS
source ~/.bashrc
cd ORB_SLAM3
./build_ros.sh
```

### Debug:

1、将Examples_old中的ROS文件夹复制到Examples文件夹中

2、修改/ORB_SLAM3/Examples_old/ROS/ORB_SLAM3/CMakeLists.txt

```cmake
#12 13
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3")
#20
set(CMAKE_CXX_STANDARD 14)
#33
find_package(OpenCV 4.2.0 QUIET)
#43
find_package(Sophus REQUIRED)
#49 add
${PROJECT_SOURCE_DIR}/../../../Thirdparty/Sophus
```

3、修改”ORB_SLAM3/Examples_old/ROS/ORB_SLAM3/src/AR“中的文件

```cmake
cv::Mat Tcw=ORB_SLAM3::Converter::toCvMat(mpSLAM->TrackMonocular(cv_ptr->image,cv_ptr->header.stamp.toSec()).matrix());
#in line 151 of ros_mono_ar.cc

vPoints.push_back(ORB_SLAM3::Converter::toCvMat(pMP->GetWorldPos()));
#in line 405 of ViewerAR.cc

cv::Mat Xw = ORB_SLAM3::Converter::toCvMat(pMP->GetWorldPos());
#in line 530 of ViewerAR.cc
```

