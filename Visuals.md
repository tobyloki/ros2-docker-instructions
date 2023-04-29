Install rviz2
```bash
apt-get install -y ros-humble-rviz2

export LD_LIBRARY_PATH=/opt/ros/humble/opt/rviz_ogre_vendor/lib/:$LD_LIBRARY_PATH

ros2 run rviz2 rviz2
```

rqt_graph
```bash
apt-get install -y ros-humble-rqt ros-humble-rqt-graph

apt-get install -y libqt5widgets5 libqt5gui5 libqt5dbus5 qt5-style-plugins

export QT_QPA_PLATFORM_PLUGIN_PATH=/usr/lib/aarch64-linux-gnu/qt5/plugins/platforms/

ros2 run rqt_graph rqt_graph
```