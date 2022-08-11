---
title: "Jetson Xavier NX(JetPack 5.01)にROS2とRealSense SDKをインストールする"
date: 2022-08-11T23:54:19+09:00
draft: false
categories: ["Jetspm"]
tags: ["ROS2", "Jetson Xavier NX","RealSense"]
---
# 開発環境
* PC:Jetson Xavier NX
* OS:JetPack 5.01

# JetPack 5.01のダウンロード・OSイメージの書き込み
適当なPC上での作業  
JetPack 5.01を以下のリンクからダウンロード   https://developer.nvidia.com/embedded/jetpack-sdk-501dp
https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit#write ここのインストールガイドに従ってOSを書き込む。
SDカードを[SD Memory Card Formatter](https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/)でクイックフォーマットでフォーマットする。
フォーマットしたのち[Etcher](https://www.balena.io/etcher/)で先ほどダウンロードしたOSイメージを書き込む。

完了したらSDカードをJetsonに刺して準備完了。Jetsonの電源をいれるとUbuntuが起動するようになる。

# RealSense SDK 2.0のインストール
[RealSense SDK 2.0](https://github.com/IntelRealSense/librealsense)の[インストールガイド](https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md)に従おうとしたけけどx86/ x64ではaptインストールできるが、Armアーキテクチャではできないらしいのでソースからビルドする必要がある。(Jetson Xavier NXはARMv8)
↓  
https://hackmd.io/FxWqPYAeRpK2Jv70leGXhghttps://github.com/IntelRealSense/librealsense/issues/10197#issuecomment-1042997148

## ビルド方法
```
git clone https://github.com/IntelRealSense/librealsense.git
cd librealsense/scripts/
.libuvc_installation.sh
```
これでビルドが開始される。結構時間がかかる。
終了したら
```rs-depth```とか```rs-multicam```
を実行できるようになる。
![](../img/rs-depth.png)
![](../img/rs-multicam.jpg)

# ROS2(Foxy)のインストール
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html#
に従って、
```
locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```

```
sudo apt update && sudo apt install curl gnupg2 lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg
```
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

```
sudo apt update
sudo apt upgrade
sudo apt install ros-foxy-desktop
```

これでインストール完了
```
source /opt/ros/foxy/setup.bash
```
を.bachrcに追加しておく

# RealSenseのROS2 Wrapperのインストール
```
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src/
```
```
sudo apt-get install python3-rosdep -y
sudo rosdep init # "sudo rosdep init --include-eol-distros" for Dashing
rosdep update
rosdep install -i --from-path src --rosdistro $ROS_DISTRO --skip-keys=librealsense2 -y
```

ここはReadmeでは特に書かれていなかったけれどもcolconが最初インストールされてないのでインストールしてからビルドする。
```
sudo apt install python3-colcon-common-extensions
colcon build
```

これで完了。

# 使い方
RealSenseノードの起動
```
cd ~/ros2_ws
. install/local_setup.bash
ros2 launch realsense2_camera rs_launch.py
```
別のターミナルで
```
rviz2
```
でrvizを起動
Fixed Frameを```camera_color_frame```にする。
AddでImageを追加してImage>Topicに```/camera/color/image_raw```
同様にもう一つImageを追加してImage>Topicに```/camera/depth/image_rect_raw```を追加する。
![](../img/ros2_realsense.png)
これでROS2でRealSenseを使うことができた。
