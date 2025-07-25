## SLAM in Autonomous Driving Book (SAD Book)

This book systematically introduces readers to inertial navigation, integrated navigation, LiDAR mapping, LiDAR localization, LiDAR-inertial odometry, and related knowledge. This repository contains the source code accompanying the book and is publicly available for use.

The English translation is almost done and is open-source, check it out here: https://github.com/gaoxiang12/slam-in-ad-en/blob/main/sad-en.pdf 

<img src="https://github.com/gaoxiang12/slam_in_autonomous_driving/assets/6635511/734af25b-d866-4dcf-a155-773190ba03d8" width="300" />

## Notes

- The book began printing on July 10, 2023, and is expected to be available within two weeks. I will update the links to various platforms at that time.
- August 9, 2023: The book is currently in its second printing, with some content corrections from the first edition (though without signatures). For details, see the code updates.
- Official page from Electronic Industry Publishing House: https://item.jd.com/10080292102089.html
- JD.com self-operated store: https://item.jd.com/13797963.html

## Book Content Structure

- Chapter 1: Overview
- Chapter 2: Review of mathematical fundamentals: geometry, kinematics, KF filter theory, and matrix Lie groups
- Chapter 3: Error-state Kalman filter, inertial navigation, satellite navigation, and integrated navigation
- Chapter 4: Pre-integration, graph optimization, and pre-integration-based integrated navigation
- Chapter 5: Point cloud basics, nearest neighbor structures, and point cloud linear fitting
- Chapter 6: 2D LiDAR mapping: scan matching, likelihood fields, submaps, 2D loop closure detection, and pose graph
- Chapter 7: 3D LiDAR mapping: ICP, ICP variants, NDT, NDT LO, Loam-like LO, and loosely-coupled LIO
- Chapter 8: Tightly-coupled LIO, IESKF, and pre-integration tightly-coupled LIO
- Chapter 9: Offline mapping: frontend, backend, batch loop closure detection, map optimization, and tile export
- Chapter 10: Fusion localization: LiDAR localization, initialization search, tile map loading, and EKF fusion

## Features of This Book

- This book likely offers the simplest mathematical derivations and code implementations among similar materials.
- In this book, you will reproduce many classic algorithms and data structures in LiDAR SLAM:
  - You will derive and implement an Error-State Kalman Filter (ESKF), feed it IMU and GNSS data, and observe how it estimates its state.
  - You will also implement a pre-integration system for the same purpose and compare their performance.
  - Next, you will implement common algorithms in 2D LiDAR SLAM: scan matching, likelihood fields, submaps, occupancy grids, and use loop closure detection to build larger maps—all by yourself.
  - In LiDAR SLAM, you will implement a K-d tree for approximate nearest neighbor searches, then use it for ICP and point-to-plane ICP, discussing potential improvements.
  - You will implement the classic NDT algorithm, test its registration performance, and use it to build a LiDAR odometer—much faster than most existing LOs.
  - You will also implement a point-to-plane ICP LiDAR odometer, which is similarly fast and works similarly to Loam but simpler.
  - You will integrate IMU into the LiDAR odometer, implementing both loosely-coupled and tightly-coupled LIO systems, including derivations for iterative Kalman filters and pre-integration graph optimization.
  - You will adapt the system for offline operation to allow thorough loop closure detection, ultimately creating an offline mapping system.
  - Finally, you will segment the resulting map for real-time localization.
- Most implementations in this book are significantly simpler than those in comparable libraries, making it easier to understand their workings without dealing with complex interfaces.
- The book employs convenient concurrent programming, often resulting in more efficient implementations than existing algorithms (partly due to historical reasons).
- Each chapter includes dynamic demonstrations like these:

![](./doc/lio_demo.gif)
![](./doc/2dmapping_demo.gif)
![](./doc/lo_demo.gif)

We hope you enjoy the minimalist style of this book and discover the joy of algorithms.

## Datasets

- Dataset download links:
  - Baidu Cloud: https://pan.baidu.com/s/1ELOcF1UTKdfiKBAaXnE8sQ?pwd=feky (Extraction code: feky)
  - OneDrive: https://1drv.ms/u/s!AgNFVSzSYXMahcEZejoUwCaHRcactQ?e=YsOYy2

- Includes the following datasets (total 270GB; download selectively based on storage capacity):
  - UrbanLoco (ULHK, 3D LiDAR, urban road scenarios)
  - NCLT (3D LiDAR, RTK, campus scenarios)
  - WXB (3D LiDAR, campus scenarios)
  - 2dmapping (2D LiDAR, mall scenarios)
  - AVIA (DJI solid-state LiDAR)
  - UTBM (3D LiDAR, road scenarios)
- Other built-in data:
  - Chapters 3–4 use text-formatted IMU and RTK data.
  - Chapter 7 uses some EPFL data for point cloud registration.
- Store datasets in `./dataset/sad/` for default paths to work. Alternatively, manually specify paths or create symlinks if storage is limited.

## Compilation

- Recommended environment: Ubuntu 20.04. Older versions require GCC adjustments (C++17 support). For newer Ubuntu, install the corresponding ROS version.
- Prerequisites:
  - ROS Noetic: http://wiki.ros.org/noetic/Installation/Ubuntu
  - Additional libraries:
    ```bash
    sudo apt install -y ros-noetic-pcl-ros ros-noetic-velodyne-msgs libopencv-dev libgoogle-glog-dev libeigen3-dev libsuitesparse-dev libpcl-dev libyaml-cpp-dev libbtbb-dev libgmock-dev
    ```
  - Pangolin: Compile `thirdparty/pangolin.zip` or install from https://github.com/stevenlovegrove/Pangolin
  - g2o: Compile `thirdparty/g2o` or install from https://github.com/RainerKuemmerle/g2o
- Build commands:
  ```bash
  mkdir build
  cd build
  cmake ..
  make -j8
  ```
- Executables are located in the `bin` directory.

### Ubuntu 18.04 Adaptation

For Ubuntu 18.04, install GCC-9 and compatible TBB, or use Docker.

**Install GCC-9:**
```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo update-alternatives --remove-all gcc
sudo update-alternatives --remove-all g++

# Priority values (1 and 10); auto-mode defaults to higher priority.
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 1
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 10

sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 1
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 10
```

**Check version:**
```bash
g++ -v
```

**Build:**
```bash
mkdir build
cd build
cmake .. -DBUILD_WITH_UBUNTU1804=ON
make -j8
```

**Docker setup:**
```bash
docker build -t sad:v1 .
./docker/docker_run.sh
```
Inside the container:
```bash
cd ./thirdparty/g2o
mkdir build
cd build
cmake ..
make -j8
cd /sad
mkdir build
cd build
cmake ..
make -j8
```

## FAQs

1. GUI crashes on certain 2023+ laptop models (GL hardware compatibility): https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/67
2. Chapter 5 `test_nn` GMock errors during compilation: https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/18
3. Compiler version issues: https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/4
4. g2o compilation (`config.h` not found): https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/95

--- 

## SLAM in Autonomous Driving book (SAD book)

本书向读者系统介绍了惯性导航、组合导航、激光建图、激光定位、激光惯导里程计等知识。本仓库是书籍对应的源代码仓库，可以公开使用。

<img src="https://github.com/gaoxiang12/slam_in_autonomous_driving/assets/6635511/734af25b-d866-4dcf-a155-773190ba03d8" width="300" />



## 注意

- 本书已于2023.7.10开始印刷，预计在两周内上架。届时我会更新各平台的链接信息。
- 2023.8.9 本书目前是第二次印刷，在第一次上修正了一部分内容（但没有签名了），详情见代码的推送。
- 电子工业出版社官方：https://item.jd.com/10080292102089.html
- 京东自营： https://item.jd.com/13797963.html

## 本书的内容编排

- 第1章，概述
- 第2章，数学基础知识回顾，几何学、运动学、KF滤波器理论，矩阵李群
- 第3章，误差状态卡尔曼滤波器，惯性导航、卫星导航、组合导航
- 第4章，预积分，图优化，基于预积分的组合导航
- 第5章，点云基础处理，各种最近邻结构，点云线性拟合
- 第6章，2D激光建图，scan matching, 似然场，子地图，2D回环检测，pose graph
- 第7章，3D激光建图，ICP，变种ICP，NDT，NDT LO, Loam-like LO，LIO松耦合
- 第8章，紧耦合LIO，IESKF，预积分紧耦合LIO
- 第9章，离线建图，前端，后端，批量回环检测，地图优化，切片导出
- 第10章，融合定位，激光定位，初始化搜索，切片地图加载，EKF融合

## 本书的特点

- 本书大概是您能找到的类似材料中，数学推导和代码实现最为简单的书籍。
- 在这本书里，您会复现许多激光SLAM中的经典算法和数据结构。
    - 您需要自己推导、实现一个误差状态卡尔曼滤波器(ESKF)，把IMU和GNSS的数据喂给它，看它如何推算自己的状态。
    - 您还会用预积分系统实现一样的功能，然后对比它们的运行方式。
    - 接下来您会实现一遍2D激光SLAM中的常见算法：扫描匹配、似然场、子地图，占据栅格，再用回环检测来构建一个更大的地图。这些都需要您自己来完成。
    - 在激光SLAM中，您也会自己实现一遍Kd树，处理近似最近邻，然后用这个Kd树来实现ICP，点面ICP，讨论它们有什么可以改进的地方。
    - 然后您会实现经典的NDT算法，测试它的配准性能，然后用它来搭建一个激光里程计。它比大部分现有LO快得多。
    - 您也会实现一个点面ICP的激光里程计，它也非常快。它工作的方式类似于Loam，但更简单。
    - 您会想要把IMU系统也放到激光里程计中。我们会实现松耦合和紧耦合的LIO系统。同样地，您需要推导一遍迭代卡尔曼滤波器和预积分图优化。
    - 您需要把上面的系统改成离线运行的，让回环检测运行地充分一些。最后将它做成一个离线的建图系统。
    - 最后，您可以对上述地图进行切分，然后用来做实时定位。
- 本书的大部分实现都要比类似的算法库简单的多。您可以快速地理解它们的工作方式，不需要面对复杂的接口。
- 本书会使用非常方便的并发编程。您会发现，本书的实现往往比现有算法更高效。当然这有一部分是历史原因造成的。
- 本书每章都会配有动态演示，像这样：

![](./doc/lio_demo.gif)
![](./doc/2dmapping_demo.gif)
![](./doc/lo_demo.gif)

希望您能喜欢本书的极简风格，发现算法的乐趣所在。

## 数据集

- 数据集下载链接：
- 百度云链接: https://pan.baidu.com/s/1ELOcF1UTKdfiKBAaXnE8sQ?pwd=feky 提取码: feky
- OneDrive链接：https://1drv.ms/u/s!AgNFVSzSYXMahcEZejoUwCaHRcactQ?e=YsOYy2 

- 包含以下数据集。总量较大(270GB)，请视自己硬盘容量下载。
    - UrbanLoco (ULHK，3D激光，道路场景)
    - NCLT (3D激光，RTK，校园场景)
    - WXB (3D激光，园区场景)
    - 2dmapping (2D激光，商场场景)
    - AVIA (大疆固态激光)
    - UTBM (3D激光，道路场景)
- 其他的内置数据
    - 第3,4章使用文本格式的IMU，RTK数据
    - 第7章使用了一部分EPFL的数据作为配准点云来源
- 您应该将上述数据下载至./dataset/sad/目录下，这样许多默认参数可以正常工作。如果不那么做，您也可以手动指定这些文件路径。如果您硬盘容量不足，可以将其他硬盘的目录软链至此处。

## 编译

- 本书推荐的编译环境是Ubuntu 20.04。更老的Ubuntu版本需要适配gcc编译器，主要是C++17标准。更新的Ubuntu则需要您自己安装对应的ROS版本。
- 在编译本书代码之前，请先安装以下库（如果您机器上没有安装的话）
    - ROS Noetic: http://wiki.ros.org/noetic/Installation/Ubuntu
    - 使用以下指令安装其余的库
    ```bash
    sudo apt install -y ros-noetic-pcl-ros ros-noetic-velodyne-msgs libopencv-dev libgoogle-glog-dev libeigen3-dev libsuitesparse-dev libpcl-dev libyaml-cpp-dev libbtbb-dev libgmock-dev
    ```
    - Pangolin: 编译安装thirdparty/pangolin.zip，或者 https://github.com/stevenlovegrove/Pangolin
    - 编译thirdparty/g2o，或者自行编译安装 https://github.com/RainerKuemmerle/g2o 
    - 通过cmake, make安装本repo下的`thirdparty/g2o`库
- 之后，使用通常的cmake, make方式就可以编译本书所有内容了。例如
```bash
mkdir build
cd build
cmake ..
make -j8
```
- 编译后各章的可执行文件位于`bin`目录下

### 适配Ubuntu18.04

为了在Ubuntu18.04上编译运行，需要安装gcc-9，并且使用对应版本的TBB。或者在docker环境下使用。

**安装gcc-9**
```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo update-alternatives --remove-all gcc
sudo update-alternatives --remove-all g++

#命令最后的1和10是优先级，如果使用auto选择模式，系统将默认使用优先级高的
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 1
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 10

sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 1
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 10
```

**检查版本**
```bash
g++ -v
```

**编译程序**
```bash
mkdir build
cd build
cmake .. -DBUILD_WITH_UBUNTU1804=ON
make -j8
```

**在docker环境下使用**
```bash
docker build -t sad:v1 .
./docker/docker_run.sh
```
进入docker容器后
```bash
cd ./thirdparty/g2o
mkdir build
cd build
cmake ..
make -j8
cd /sad
mkdir build
cd build
cmake ..
make -j8
```

## 常见问题
1. 图形界面在2023年以后特定型号的笔记本端导致桌面卡死（GL硬件兼容性）：https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/67 
2. 第5章test_nn编译时，gtest报gmock错误：https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/18
3. 编译器版本问题：https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/4
4. g2o编译问题（config.h找不到）： https://github.com/gaoxiang12/slam_in_autonomous_driving/issues/95 

## TODO项

- LioPreiteg在某些数据集上不收敛

## NOTES

- [已确认] ULHK的IMU似乎和别家的不一样，已经去了gravity, iekf初期可能有问题
- [已确认] NCLT的IMU在转包的时候转成了Lidar系，于是Lidar与IMU之间没有旋转的外参（本来Lidar是转了90度的），现在Lidar是X左Y后Z下，原车是X前Y右Z下。本书使用的NCLT数据均基于点云系,
  IMU的杆臂被忽略。
- [已确认] NCLT的rtk fix并不是非常稳定，平均误差在米级
