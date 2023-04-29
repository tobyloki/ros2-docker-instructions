# ros2-docker-instructions

- List of available docker images found at https://github.com/osrf/docker_images in the Architectures section.

- The docker image I used was located at https://hub.docker.com/r/arm64v8/ros/tags?page=1&name=humble

# Instructions

1. Pull Docker image
```bash
docker pull arm64v8/ros:humble-ros-base
```

2. Run Docker container while allowing GUIS to show (e.g. rqt_graph)
- First install XQuartz (https://www.xquartz.org/)
```bash
DOCKER_COMMON_ARGS="--env=DISPLAY --env=QT_X11_NO_MITSHM=1 -v /tmp/.X11-unix:/tmp/.X11-unix:rw"

sudo docker run -it -d --net=host --privileged $DOCKER_COMMON_ARGS --name ros --restart unless-stopped arm64v8/ros:humble-ros-base
```

3. Add source script to ~/.bashrc
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

4. Source (to get access to ros2 command)
```bash
source ~/.bashrc
```

5. Make ros workspace
```bash
mkdir -p ros2_ws/src
cd ~/ros2_ws
```

6. Make pub-sub package
```bash
ros2 pkg create --build-type ament_python py_pubsub
```

7. Add pub-sub files
```bash
cd ~/ros2_ws/src/py_pubsub/py_pubsub

apt-get update -y

apt-get install wget

wget https://raw.githubusercontent.com/ros2/examples/humble/rclpy/topics/minimal_publisher/examples_rclpy_minimal_publisher/publisher_member_function.py

wget https://raw.githubusercontent.com/ros2/examples/humble/rclpy/topics/minimal_subscriber/examples_rclpy_minimal_subscriber/subscriber_member_function.py
```

8. Add dependencies in package.xml under `<license>`
```xml
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
<exec_depend>ros2launch</exec_depend>
```

Add entry points to setup.py
```python
entry_points={
    'console_scripts': [
        'talker = py_pubsub.publisher_member_function:main',
        'listener = py_pubsub.subscriber_member_function:main',
    ],
},
```

9. Add launch file
```bash
mkdir ~/ros2_ws/src/py_pubsub/launch

cd  ~/ros2_ws/src/py_pubsub/launch

touch pubsub.launch.py
```

10. Add code to pubsub.launch.py
```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='py_pubsub',
            executable='talker',
            name='talker',
            output='screen'
        ),
        Node(
            package='py_pubsub',
            executable='listener',
            name='listener',
            output='screen'
        )
    ])
```

11. Install dependencies
```bash
cd ~/ros2_ws

rosdep install -i --from-path src --rosdistro humble -y
```

12. Build
```bash
colcon build
```

13. Add ros workspace source script to ~/.bashrc
```bash
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

14. Source 
```bash
source ~/.bashrc
```

15. Run
- Run via ros2 run commands
```bash
ros2 run py_pubsub talker

ros2 run py_pubsub listener
```

- Run via ros2 launch command
```bash
ros2 launch py_pubsub pubsub.launch.py
```

# Notes
- when editing python files, don't need to re-run source, but still need to re-build