# Autoware Demo
### Nomenclature:
* Pi       - Ubiquity Raspberry Pi
* TX2      - NVIDIA Jetson TX2
* Minotaur - Lenovo ThinkPad Laptop

### IP Addresses:
* TX2 - 192.168.1.198
* Pi - 192.168.1.111
* Minotaur - 192.168.1.133

### Step-by-step Instructions
#### 1. On Minotaur
* Connect to Archer-5.0
* Open ~/.bashrc
* Find the comments "Autoware IP's" and uncomment the 2 lines under it
* Comment out the 3 lines under "SAKE Robotics IP's" if it is not already commented out

#### 2. On Pi (via SSH)
```bash
sudo /etc/init.d/ntp stop
sudo ntpdate 192.168.1.198
```

#### 3. On TX2 (via SSH X Forwarding: ssh -X nvidia@192.168.1.198)
* Copy ROS_IP and ROS_MASTER_URI from ~/.bashrc
* Run Autoware container
  ```bash
  cd ~/tx2docker/
  ./run
  ```
* Open ~/.bashrc in container and paste the ROS_IP and ROS_MASTER_URI that was copied earlier.
* Source ~/.bashrc
* Run the following:
  ```bash
  cd ~/shared_dir
  ./copyParamToRuntime.sh
  cd ~/Autoware/ros
  source devel/setup.bash
  rosrun runtime_manager runtime_manager_dialog.py
  ``` 
* This will open the Autoware GUI on the laptop

#### 4. On TX2 (via SSH)
* Terminal 1
  ```bash
  roslaunch velodyne_pointcloud VLP16_points.launch
  ```
* Terminal 2
  ```bash
  rosrun topic_tools relay /velodyne_points /points_raw
  ```

#### 5. On Autoware GUI
* On Map tab:
  - In the PointCloud section:
    * Click Ref
    * Select "0.2_june28_car_short_flat.pcd"
    * Click PointCloud
  - In the Vector Map section:
    * Click Vector Map
  - In the TF section:
    * Click Ref
    * Select "tfvelodyne.launch"
    * Click TF
* On Sensing tab:
  - voxel_grid_filter
    * Click box to start voxel_grid_filter
* On Computing tab:
  - way_planner
    * Click box to start way_planner

#### 6. On Minotaur
* Open RViz
* Once RViz is open, File -> Open Config -> icuro_autoware_tx2.rviz (Ctrl + O -> icuro_autoware_tx2.rviz)

#### 7. On RViz
* Use 2D Pose Estimate and 2D Nav Goal to create waypoints on the lane
* Select the topic /global_waypoints_rviz topic to visualize blue line (default to not showing)

#### 8. On Autoware GUI
* On Computing tab:
  - vel_pose_connect
    * Click box to start vel_pose_connect
  - ndt_matching
    * Click box to start ndt_matching
  - dp_planner
    * Click box to start dp_planner
  - pure_pursuit
    * Click "app"
    * Click Dialogue and set velocity to 7
    * Click box to start pure_pursuit
  - twist_filter
    * Click box to start twist_filter
  - lidar_euclidean_cluster_detect
    * Click "app"
    * Click downsample_cloud
    * Change cluster size maximum to 300
    * Change output_frame to velodyne
    * Click box to start lidar_euclidean_cluster_detect

#### 9. On TX2 (via SSH)
* Run the following:
  ```bash
  cd ~/ros_ws
  ./vlp_outside_relay.py
  ```
* The robot should start moving

### Notes
* To check if velocity commands are being produced:
  ```bash
  rostopic echo /twist_raw
  ```
* To move robot manually:
  ```bash
  rosrun teleop_twist_keyboard teleop_twist_keyboard.py
  ```
* The origin of the map (0,0,0,0,0,0) is located at the middle of the crosswalk line in front of ICURO
