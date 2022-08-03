---
title: ROS Tools
category: ROS
layout: 2017/sheet
tags: [Featured]
updated: 2022-08-03
weight: -10
intro: ros_tools
---

<style type="text/css">
video {
    width: 100%;
}
</style>

---------------

{: .-three-column}

### PlotJuggler

強力なプロットツール

```bash
sudo apt install ros-$ROS_DISTRO-plotjuggler
```

[facontidavide/PlotJuggler](https://github.com/facontidavide/PlotJuggler)

![PlotJuggler](https://raw.githubusercontent.com/facontidavide/PlotJuggler/main/docs/plotjuggler3.gif)

### multi_data_monitor

Rviz2の上の部分にデータを表示するRviz2プラグイン

[tier4/multi_data_monitor](https://github.com/tier4/multi_data_monitor)

### rqt_embed_window

任意のウインドウをrqtで表示するプラグイン

[awesomebytes/rqt_embed_window](https://github.com/awesomebytes/rqt_embed_window)

![Screenshot of SimpleScreenRecorder, Rviz, Plotjuggler, rosbag_editor and a normal rqt_console](https://raw.githubusercontent.com/awesomebytes/rqt_embed_window/main/screenshot1.png)

### dear_ros_node_viewer

DearImGuiライブラリを使ったrqt_graphの軽量版  
[takeshi-iwanari/dear_ros_node_viewer](https://github.com/takeshi-iwanari/dear_ros_node_viewer)

<video controls autoplay muted>
  <source src="https://user-images.githubusercontent.com/105265012/177068238-eaf4fed9-12c0-4c5b-ac7f-9597483c4c3c.mp4" type="video/mp4">
</video>

![](https://user-images.githubusercontent.com/105265012/177068238-eaf4fed9-12c0-4c5b-ac7f-9597483c4c3c.mp4)

### ament_cmake_auto

ROS2のCMakeを楽に書くためのプラグイン  
デフォルトでインストールされている  
詳細は[ブログ](https://hans-robo.hatenablog.com/entry/2020/12/15/153503)を参照

[amnet_cmake_auto](https://github.com/ament/ament_cmake/tree/rolling/ament_cmake_auto)

```CMake
cmake_minimum_required(VERSION 3.5)
project(example)

find_package(ament_cmake_auto REQUIRED)
ament_auto_find_build_dependencies()

ament_auto_add_library(${PROJECT_NAME} SHARED
 src/example.cpp include/example.hpp)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_auto_package()
```
