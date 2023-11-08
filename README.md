# Help

All in separate windows. Note `./activate.sh` has to be used in each shell first.

~`ros-humble-image-proc` (used for resize) has a bug, update:~ nvm added to layer.

```sh
# Ignore this, alr fixed.
sudo apt install ros-humble-image-proc
# Note: With everything else as constant variables, this is the only factor between it working & not.
```

Camera node:

```sh
ros2 run v4l2_camera v4l2_camera_node --ros-args -r /image_raw:=/image_cam
```

Container for nodelets:

```sh
ros2 run rclcpp_components component_container
```

Load ResizeNode into above container:

```sh
ros2 component load /ComponentManager image_proc image_proc::ResizeNode -r /image:=/image_cam -r /resize:=/image -p use_scale:=false -p height:=640 -p width:=640
```

Build packages:

```sh
# --symlink-install is bad cause it breaks when the container is rebuilt.
colcon build
```

Start YOLOv8 node:

```sh
ros2 launch isaac_ros_yolov8 isaac_ros_yolov8_visualize.launch.py model_file_path:=/workspaces/isaac_ros-dev/models/yolov8s.onnx engine_file_path:=/workspaces/isaac_ros-dev/models/yolov8s.plan input_binding_names:=['images'] output_binding_names:=['output0'] network_image_width:=640 network_image_height:=640 force_engine_update:=False image_mean:=[0.0,0.0,0.0] image_stddev:=[1.0,1.0,1.0] input_image_width:=640 input_image_height:=640 confidence_threshold:=0.25 nms_threshold:=0.45
```

## Realsense Attempts

### Approach A

Follow <https://nvidia-isaac-ros.github.io/getting_started/hardware_setup/sensors/realsense_setup.html>.

- Add in additional `.realsense` layer
  - See <https://nvidia-isaac-ros.github.io/repositories_and_packages/isaac_ros_common/index.html> for more info regarding adding layers.
- Requires the camera to be plugged in during building: builds for exact hardware?
- Setups some udev rules and builds librealsense from source.
- Also compiles a specific old version of the Realsense ROS2 packages from source, is this still needed?

### Approach B

Follow <https://github.com/IntelRealSense/realsense-ros>.

- Uses prebuilt librealsense and Realsense ROS2 packages.
- If this works correctly, it saves time & hassle.
