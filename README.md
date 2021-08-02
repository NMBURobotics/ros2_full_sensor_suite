# ros2_full_sensor_suite

THis project aims to provide easy integration for simulation of essential sensors for mobile robots. 

The sensor suite consists of following components; 

| Sensor type | Topic Name(s) | Message Type | Update Rate |
| :---: | :---: | :---: | :---: |
| LIDAR | /velodyne_points | sensor_msgs::msg::PointCloud2 | 30 |
| RealSense D435 COLOR CAMERA | /camera/color/image_raw | sensor_msgs::msg::Image | 30 |
| RealSense D435 DEPTH CAMERA | /camera/aligned_depth_to_color/image_raw | sensor_msgs::msg::Image | 30 |
| RealSense D435 IR1 CAMERA | /camera/infra1/image_raw | sensor_msgs::msg::Image | 1 |
| RealSense D435 IR2 CAMERA | /camera/infra2/image_raw | sensor_msgs::msg::Image | 1 |
| GPS | /gps/fix | sensor_msgs::msg::NavSatFix | 30 |
| IMU | /imu | sensor_msgs::msg::Imu | 30 |


All this sensors are composed to one gazebo model named `full_sensor_suite`. Refer to models folder to find the related files. 

There is also a Gazebo World plugin, which is used to attach the sensor suite model to any robot model. See the provided `test.world` for example usage.

# Installation requirements

This code was developed under Ubuntu 20.04 with Foxy version of ROS2. 
Make sure to have a similar setup. 

clone this repo to your workspace/src and let the rosdep resolve dependencies with ; 

>  rosdep install -y -r -q --from-paths src --ignore-src --rosdistro foxy 

finally build with colcon;

> colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release --parallel-workers 4

# Example usage 
 
Replace `botanbot` with you robot name and change the fileds in sensor_base_attacher_plugin accordingly. 

 ```xml
<?xml version="1.0"?>
    <sdf version="1.6">
        .
        .
        .
        <world name="test_world">   
            <include>
                <pose>0.0 0 0.5 0.0 0.0 0.0</pose>
                <uri>model://botanbot</uri>
            </include>

            <include>
                <pose>0.0 0 1.0 0.0 0.0 0.0</pose>
                <uri>model://full_sensor_suite</uri>
            </include>

            <plugin name="sensor_base_attacher_plugin" filename="libsensor_base_attacher_plugin.so">
                <robot_model_name>botanbot</robot_model_name>
                <robot_base_link_name>base_link</robot_base_link_name>
                <sensor_suite_model_name>full_sensor_suite</sensor_suite_model_name>
                <sensor_suite_base_link_name>base_sensor_link</sensor_suite_base_link_name>
            </plugin>
            .
            .
            .
        </world>
    </sdf>            
```

Launch the world file with; 

> ros2 launch ros2_full_sensor_suite test.launch.py

Open RVIZ set the global frame to `base_link`. You can view various sensor data such as ; 

![.](images/teaser.png)


Note that some sensor data cannot be visualized through RVIZ but you can just subscribe to a topic to echo the data. 

e.g 

> ros2 topic echo /imu/data