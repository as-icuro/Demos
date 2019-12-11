# Demos
Instructions to run ICURO Demos

## 1. Picker Demo

### Nomenclature:
* Pi       - Ubiquity Raspberry Pi ($pi)
* AMD      - AMD V1000 (V1807B) ($amd)
* Minotaur - Lenovo ThinkPad Laptop

### IP Addresses:
* AMD - 192.168.7.222
* Pi  - 192.168.7.210

### Notes:
* The rsa keys for logging in to the Pi and AMD are already saved in Minotaur.
* To log into Pi, run the following on Minotaur
```bash
ssh $pi
```
* To log into AMD, run the following on Minotaur
```bash
ssh $amd
```

### Step-by-step Instructions:
#### 1. On Minotaur:
* Connect to TP-Link_E32E
* Open ~/.bashrc
* Find the comments "SAKE Robotics IP's" and uncomment the 3 lines under it
* Comment out Autoware IP's if it is not already commented out

#### 2. On Pi (via SSH):
```bash
cd ~
sudo /etc/init.d/ntp stop
sudo ntpdate 192.168.7.222
./calibrate-gripper.py
roscd fastpick/nodes
./grbl_client.py h
```
OR
Simply run the following BASH script:
```bash
cd ~
./timesyncandcalibrate.sh
```

#### 3. On AMD (via SSH):
* Terminal 1
```bash
cd ~
sudo chmod 777 /dev/ttyUSB0
roslaunch go navigation_with_laset_cut.launch
```
* Terminal 2
```bash
rosrun pick_decision pick_decision_node
```
* Terminal 3
```bash
cd ~
./mux_videostream.sh
```
* Terminal 4
```bash
cd ~
./feedmux.sh 1
```

#### 4. On Pi (via SSH)
```bash
roscd fastpick/nodes
./icuro_drive.py
```

#### 5. On Minotaur:
* Terminal 1
```bash
roscd amdmsgs/../scripts
python3 tkintertest.py
```
* Terminal 2
```bash
roscd amdmsgs/../scripts
./admin.py
```
* Terminal 3
  - Open RViz
  - Once RViz is open, File -> Open Config -> navigation.rviz (Ctrl + O -> navigation.rviz)
  

#### 6. On AMD (Physically, not via SSH)
* Terminal 1
  - Open RViz
  - Once RViz is open, File -> Open Config -> navigation.rviz (Ctrl + O -> navigation.rviz)

* Terminal 2
```bash
cd ~
./runClassifierROS.sh
```
**NOTE:** The ROS Classifier program has a memory leak issue. So it will crash if you run continuously for more than 15 minutes (estimated). Best practice is to stop this script and restart it just before the demo. Everything else will be unaffected.

#### 7. On Minotaur:
* Click the "Interactive" button on admin.py twice 
* Click "Macaroni Bowl" or any option on the tkinter window twice
* After the first pick, then click the option only once
* Navigate back to pick_decision to see that the array is > 0

### To run Picker along with Fiducial robot:
**IMPORTANT**: Turn the Fiducial robot on only after the Picker robot is running and the TP-Link_E32E access point is up.
* Log on to Fiducial robot Raspberry Pi - ubuntu@192.168.7.102
* Terminal 1 (On Fiducal Pi)
```bash
roslaunch magni_demos simple_navigation.launch
```
* Terminal 2 (On Fiducial Pi)
```bash
cd ~/magni_goal_sender/scripts
./goal_sender.py
```
* The robot will wait for 2 items to be dropped into the bin before starting to move

## 2. Autoware Demo
### Nomenclature:
* Pi       - Ubiquity Raspberry Pi
* TX2      - NVIDIA Jetson TX2
* Minotaur - Lenovo ThinkPad Laptop

### IP Addresses:
* TX2 - 192.168.1.198
* Pi - 192.168.1.111
* Minotaur - 192.168.1.133


