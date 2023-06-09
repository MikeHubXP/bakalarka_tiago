
# Bachelor Thesis Repository: Gesture Operation of Humanoid Robot Tiago 

This is a repository to bachelor thesis on working with humanoid robot TIAGo++ using human pose recognition solution from MediaPipe, processing image data with neural networks [AlphaPose](https://github.com/MVIG-SJTU/AlphaPose) and [MotionBERT](https://github.com/Walter0807/MotionBERT) with goal to find human in a wokspace around the robot, recognize the direction in which a human is pointing and moving to said point.


## Description of folders in this repository

- [tiago_dual_python_controller](tiago_dual_python_controller): ROS package containing main script [main.py](tiago_dual_python_controller/scripts/main.py) alongside with script to process image by MediaPipe solution [mediapipe_image_server.py](tiago_dual_python_controller/scripts/mediapipe_image_server.py). Also contains images created during testing in simulation


- [alphapose_changed](alphapose_changed): Folder with files from original [AlphaPose](https://github.com/MVIG-SJTU/AlphaPose) repository, which were changed to make the data processing repetable.

- [motionbert_changed](motionbert_changed): Folder with files from original [MotionBERT](https://github.com/Walter0807/MotionBERT) repository, which were changed to make the data processing repetable.

- [image_share_service](alphapose_changed/scripts/image_share_service): Folder containing classes used for comunicating accros running scripts via LAN, used for data transfer
- [full_visualization_results](full_visualization_results): Folder vith results of image processing from simulation and a real world


## Requirements

The requirements to run this project are

- Both Python 2.7 and Python 3.6.9

- [Anaconda](https://www.anaconda.com/) platform for creating virtual environments

- Ubuntu 18.04 with ROS Melodic and TIAGo++ workspace (for example named "tiago_ws") installed by [this](http://wiki.ros.org/Robots/TIAGo%2B%2B/Tutorials/Installation/InstallUbuntuAndROS) guide

- Downloaded [AlphaPose](https://github.com/MVIG-SJTU/AlphaPose) repository and its Fast Pose model trained on Halpe (26 keypoints) dataset.

- Downloaded [MotionBERT](https://github.com/Walter0807/MotionBERT) repository and everything else required for 3D Pose estimation described in this repo

- few additional packages for comunication between running scripts: [zmq](https://pypi.org/project/zmq/), [cloudpickle](https://pypi.org/project/cloudpickle/) and [mediapipe](https://pypi.org/project/mediapipe/0.8.2/) (version 0.8.2 for Python 3.6.9, not the latest version), installed for both Python 2 and 3 (mediapipe only for Python 3)

## Installation guide

- Firstly, setup your environments for using AlphaPose with [environment_alphapose.yml](environment_alphapose.yml) and MotionBERT with [environment_motionbert.yml](environment_motionbert.yml). For AlphaPose environment, use this command:

```shell
conda env create -f environment_alphapose.yml
```
For MotionBERT environment, use:

```shell
conda env create -f environment_motionbert.yml
```
To make sure environments are created, you can run the following command, which shows all installed conda environments:

```shell
conda env list
```

After the steps above are complete, do following:

- from [alphapose_changed](alphapose_changed) folder, copy everything to original AlphaPose repository

- from [motionbert_changed](motionbert_changed) folder, copy everything to original MotionBERT repository

- add the package [tiago_dual_python_controller](tiago_dual_python_controller) to the `/src` folder in created "tiago_ws" workspace and rebuild it to register this new package with ROS via command:

```shell
catkin build
```
Now you should be ready to launch the project!

## Starting up the programs

Fot the entire project to work, you need 4 (5 with simulation) simultaneously running terminal windows, which I will describe below:

1. First window you can launch is a new terminal window to run [alphapose_image_server.py](alphapose_changed/scripts/alphapose_image_server.py) script to start up the image recognition with AlphaPose. To run it, simply move to AlphaPose main repository folder and use the following command:
```shell
python scripts/alphapose_image_server.py
```
If you want to save the visualization results of image recognition, use optional argument `--debug` like so:
```shell
python scripts/alphapose_image_server.py --debug True
```
Folder containing results will appear in your home folder.

2. Secondly, similarly as the script before, you can launch the [motionbert_image_server.py](motionbert_changed/motionbert_image_server.py) script for processing the image data from the AlphaPose results in a new terminal window. To run it, move to MotionBERT main downloaded repository and use the following command:
```shell
python motionbert_image_server.py
```
If you want to save the visualization results of image recognition, use optional argument `--debug` like so:
```shell
python motionbert_image_server.py --debug True
```
3. Now, if you want to try this project in simulation, open a new terminal window, move to created "tiago_ws" workspace, and use the commands below:
```shell
source ./devel/setup.bash
roslaunch tiago_dual_gazebo tiago_dual_gazebo.launch public_sim:=true world:=empty
```
This launches [Gazeboo](https://gazebosim.org/) simulator with the robot in an empty world and moves the robot to home position.
If you want to try this project only in simulation, skip the additional requirements in this step and move to step 4 

Otherwise, while testing this on a real robot, you need to specify few things in **each** new terminal launched after this step like so:
- Firstly, make sure your PC is connected to the same network as the robot is.
- You need to specify the `ROS_MASTER_URI` variable depending on your robot's IP address like so:
```shell
export ROS_MASTER_URI=http://<your_robot_ip>:11311
```
In my case, i used this exact command:
```shell
export ROS_MASTER_URI=http://192.168.130.114:11311
```
- Next, you need to specify the IP address of your PC , where all the other programs are running, in `ROS_IP` varible. You do this via the command below:
```shell
export ROS_IP=<your_pc_ip>
```
- You also should have specified the name of your robot in `/etc/hosts` file, where you add the following line:
```shell
<your_robot_ip> <your_robot_name>
```
In my case, i added:
```shell
192.168.130.114 tiago-114c
```
To make sure the communication is established correctly, you can try to get the list of all ROS topics provided by the robot by entering the following command to terminal:
```shell
rostopic list
```
If the list of topics appears, you should also check wether you are able to read the contents of messages transfered on those topics. You can check this for example by listening to the `/tf` topic with transforms between coordinate frames of the robot by entering the command below:
```shell
rostopic echo /tf
```
If a stream of messages containg transformation data appears in your terminal, the communication with robot was establishd succesfully.

4. Now you can launch the first ROS node, named [mediapipe_image_server.py](tiago_dual_python_controller/scripts/mediapipe_image_server.py), for processing the images with MediaPipe (using installed mediapipe package).
To run it, open a new terminal window, move to "tiago_ws" and enter the following commands:
```shell
source ./devel/setup.bash
rosrun python_control_testing_funcons mediapipe_image_server.py
```
You can also save the visualization from MediaPipe by running the script with the optional argument `--debug` like so:
```shell
rosrun python_control_testing_funcons mediapipe_image_server.py --debug True
```

5. Lastly, you can run the [main.py](tiago_dual_python_controller/scripts/main.py) script to run the main cycle of finding human, watching him move around the robot and while standing still, moving to specified point.
You can run this script by entering these commands to new terminal window while being in the "tiago_ws" folder:
```shell
source ./devel/setup.bash
rosrun python_control_testing_functions main.py
```
You can also enable saving the raw images from robot's camera by running this script with the optional argument `--debug` like so:
```shell
rosrun python_control_testing_functions main.py --debug True
```

## Demos

Before launching the entire final loop, you can try running a few demo programs defined in the [main.py](tiago_dual_python_controller/scripts/main.py) script accesed via optional argument `--demo`:
```shell
rosrun python_control_testing_functions main.py --demo <demo_name>
```
### Demo `camera`:
This demo is for verification of the robot's camera functions and processing of the image taken by MediaPipe, AlphaPose and MotionBERT. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo camera
```

### Demo `robot_info`:
This demo is for printing of the robot's odometry data to terminal, including its speed and position in odom coordinate system. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo robot_info
```

### Demo `move_head`:
This demo is for checking the correct functionality of the robot's head movement. The head rotates to 30 degrees right, left, up and down. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo move_head
```

### Demo `rotate_base`:
This demo is for checking the correct functionality of the robot's base angular movement. Base rotates to 45 degrees left and right from the current direction. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo rotate_base
```

### Demo `move_forward_and_back`:
This demo is for checking the correct functionality of the robot's base linear movement. Base moves 1 meter forward and then returns to the initial position. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo move_forward_and_back
```

### Demo `watch_human`:
This demo is for checking the correct functionality of keeping the human in the robot's field of view. Robot only moves its head and rotates base to keep visual contact with the human. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo watch_human
```

### Demo `go_to_point`:
This demo is for checking the correct functionality of calculating the final place of movement from the human gesture. Robot only takes a picture without finding a human, then processes it and after the confirmation moves to computed point. You can launch it via the command below:
```shell
rosrun python_control_testing_functions main.py --demo go_to_point
```

## Results 
The functionality of this implementation is demonstrated in [this](https://youtu.be/sJkSQmx5dcA) video, where the procces of finding human, keeping it in the center of robot vision and then moving to pointed-to place is shown in practice.


