---
title: "Maixsense A010をROS2で使ってみる"
date: 2022-12-20T01:07:52+09:00
draft: false
categories: ["ROS2"]
tags: ["ROS2","Maixsense A010"]
---

Maixsense A010はSipeedが出している5000円程度のROS対応RGBDカメラです。  
今回はAliExpressで購入しました。
https://ja.aliexpress.com/item/1005004842571816.html?spm=a2g0o.order_list.order_list_main.5.6242585afJOqTn&gatewayAdapt=glo2jpn
![](../img/Maixsense_A010.jpg)

サイズは35x24x10mm程度でデプスの範囲は0.15~1.5mで100x100,70°x60°の範囲を取得可能。フレームレートは20FPSです。  
通信はUSB2.0とUART出力ができます。

# 開発環境
* Ubuntu22.04
* ROS2 Humble

# 必要ソフトのダウンロード
https://wiki.sipeed.com/hardware/en/maixsense/maixsense-a010/maixsense-a010.html#Secondary-development%3AAccess-ROS

が公式wikiでこちらに従います。

https://dl.sipeed.com/shareURL/MaixSense/MaixSense_A010/software_pack/SDK
より、sipeed_tof_ms_a010_ros_v0.3.zipをダウンロード、展開する。

```
cd sipeed_tof_ms_a010_ros/ros2
source /opt/ros/humble/setup.sh 
colcon build
```
ビルドすると警告がでたけど多分OK

ノードの起動
```
source install/setup.sh
ros2 run sipeed_tof_ms_a010 publisher --ros-args -p device:="/dev/ttyUSB0"
```

rvizを起動して、AddからBy topicの中の/depth/imageと/cloud/PointCloud2を選択   
Global Options->Fixed Frameの値をmapからtofにする

![](../img/Maixsense_A010_at_rviz.png)

見えた!(小並感)
