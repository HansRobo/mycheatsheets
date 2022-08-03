---
title: ROS Packages
category: ROS
layout: 2017/sheet
tags: [Featured]
updated: 2022-08-03
weight: -10
intro: ros_packages
---

Getting started
---------------
{: .-three-column}

### ros2_logging_fmt

https://github.com/facontidavide/ros2_logging_fmt

fmtlibを使ってログを出力する軽量かつシンプルなロギングツール
```c++
ros2_logging_fmt::Logger logger(node.get_logger());

logger.info("Hello {} number {}", world, 42);
logger.error("We have {} errors", 99);
logger.warn("Warning: {} > {}", 30.1, 30.0);
logger.debug("DEBUG MESSAGE");
```

### grid_map

グリッドマップを扱う強力なライブラリ
https://github.com/ANYbotics/grid_map

ROS2対応はゆっくりなので，TIERIVフォークの方が良い
https://github.com/tier4/grid_map/tree/prepare/humble