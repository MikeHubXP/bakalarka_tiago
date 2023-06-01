
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

- Ubuntu 18.04 with ROS Melodic and TIAGo++ workspace installed by [this](http://wiki.ros.org/Robots/TIAGo%2B%2B/Tutorials/Installation/InstallUbuntuAndROS) guide

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

- from [alphapose_changed](alphapose_changed) folder copy everything there to original AlphaPose repo

- from [motionbert_changed](motionbert_changed) folder copy everything there to original MotionBERT repo

Now you should be ready to launch the project!

## Starting up the programs

Fot the entire project to work, you need 4 simultaneously running terminal windows, which I will describe below:

1. First window you can launch is a window to run [alphapose_image_server.py](alphapose_changed/scripts/alphapose_image_server.py) script to start up the image recognition with AlphaPose. To run it, simply move to AlphaPose main repo folder and use the following command:
```shell
python scripts/alphapose_image_server.py
```
If you want to save the visualization results of image recognition, use optional argument `--debug` like so:
```shell
python scripts/alphapose_image_server.py --debug True
```
Folder containing results will appear in your home folder.



