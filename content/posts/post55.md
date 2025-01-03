---
title: "RealSense T265 Jazzy環境構築"
date: 2025-01-03T14:29:42+09:00
draft: false
categories: ["ROS2"]
tags: ["RealSense"]
---
[T265](https://www.intelrealsense.com/visual-inertial-tracking-case-study/)は2025年現在生産終了されており、最新のソフトウェアでもサポートされていません。
ただ、どうしても使いたい場面が出てきたので環境構築方法を書いておきます。  

利用した環境は次の通りです。
|環境|バージョン|補足|
|-|-|-|
|Ubuntu|24.04||
|ROS 2|Jazzy|||
|librealsense|v2.53.1||
|realsense-ros|v5.51.1|Jazzy用に[修正した](https://github.com/CoRE-MA-KING/realsense-ros/tree/5.51.1_Jazzy)|

## librealsenseのインストール
T265に対応しているlibrealsenseをインストールしないといけません。  
v2.53.1より後のバージョンではT265が完全にサポートされなくなっているのでこれをインストールします。
[https://github.com/IntelRealSense/librealsense/releases/tag/v2.53.1](https://github.com/IntelRealSense/librealsense/releases/tag/v2.53.1)
libusbがあとで必要と言われるのでインストールしていなければこちらのインストールする。
```
sudo apt install libusb-1.0-0-dev
```

インストール、ビルドは以下の通り。
```
sudo apt install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev
git clone -b v2.53.1 --depth 1 https://github.com/IntelRealSense/librealsense.git

cd librealsense
mkdir build && cd build
cmake .. -DFORCE_RSUSB_BACKEND=true -DCMAKE_BUILD_TYPE=release
make -j2
sudo make install

cd ..
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && sudo udevadm trigger

```
ここまで実行できると```realsense-viewer```が実行できて、T265が認識されます。

## Realsense ROSのインストール
realsense-rosのv5.51.1まではT265対応しているのでこれをJazzy対応した
[https://github.com/CoRE-MA-KING/realsense-ros/tree/5.51.1_Jazzy](https://github.com/CoRE-MA-KING/realsense-ros/tree/5.51.1_Jazzy)
を使います。こちらはオリジナルのv5.51.1をJazzyでも実行できるように修正したものです。中身はほぼ変えていませんが、ビルドを通せるようにする修正のみしています。

ここではros_wsというワークスペースにインストールする方法を書いています。
```
sudo apt update
sudo apt install ros-${ROS_DISTRO}-diagnostic-updater

cd ~/ros_ws/src
git clone -b 5.51.1_Jazzy --depth 1 git@github.com:CoRE-MA-KING/realsense-ros.git

cd ~/ros_ws
colcon build
source install/setup.bash
```
インストールが出来たら以下のコマンドで実行します。

```
ros2 launch realsense2_camera rs_launch.py
```
[https://github.com/CoRE-MA-KING/realsense-ros/blob/5.51.1_Jazzy/realsense2_camera/launch/rs_launch.py](https://github.com/CoRE-MA-KING/realsense-ros/blob/5.51.1_Jazzy/realsense2_camera/launch/rs_launch.py)にパラメータがあるので、こちらをいじればトピックを変えたりなどできます。
[README](https://github.com/CoRE-MA-KING/realsense-ros/tree/5.51.1_Jazzy?tab=readme-ov-file#available-parameters)にも同様の記載があるので適宜修正してください。