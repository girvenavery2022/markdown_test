# ROS2 Tutorials
## Set locale

Make sure you have a locale which supports UTF-8. If you are in a minimal environment (such as a docker container), the locale may be something minimal like POSIX. We test with the following settings. However, it should be fine if you’re using a different UTF-8 supported locale.

locale  # check for UTF-8
```
sudo apt update && sudo apt install locales

sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```
## Setup Sources

You will need to add the ROS 2 apt repositories to your system. To do so, first authorize our GPG key with apt like this:

sudo apt update && sudo apt install curl gnupg2 lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg

And then add the repository to your sources list:
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```
## Install ROS 2 packages

Update your apt repository caches after setting up the repositories.
```
sudo apt update
```

Desktop Install (Recommended): ROS, RViz, demos, tutorials.
```
sudo apt install ros-foxy-desktop
```
ROS-Base Install (Bare Bones): Communication libraries, message packages, command line tools. No GUI tools.
```
sudo apt install ros-foxy-ros-base
```
## Environment setup

Sourcing the setup script

Set up your environment by sourcing the following file.
```
source /opt/ros/foxy/setup.bash
```
## Prerequisites to installing colcon 
Install colcon
```
sudo apt install python3-colcon-common-extensions
```
Install ROS 2

To build the samples, you will need to install ROS 2.

Follow the installation instructions.

Attention

If installing from Debian packages, this tutorial requires the desktop installation.
Basics

A ROS workspace is a directory with a particular structure. Commonly there is a src subdirectory. Inside that subdirectory is where the source code of ROS packages will be located. Typically the directory starts otherwise empty.

colcon does out of source builds. By default it will create the following directories as peers of the src directory:

    The build directory will be where intermediate files are stored. For each package a subfolder will be created in which e.g. CMake is being invoked.

    The install directory is where each package will be installed to. By default each package will be installed into a separate subdirectory.

    The log directory contains various logging information about each colcon invocation.

Note

Compared to catkin there is no devel directory.
Create a workspace

First, create a directory (ros2_example_ws) to contain our workspace:

mkdir -p ~/ros2_example_ws/src
cd ~/ros2_example_ws

At this point the workspace contains a single empty directory src:

.
└── src

1 directory, 0 files

## Add some sources

Let’s clone the examples repository into the src directory of the workspace:

git clone https://github.com/ros2/examples src/examples -b galactic

Now the workspace should have the source code to the ROS 2 examples:

.
└── src
    └── examples
        ├── CONTRIBUTING.md
        ├── LICENSE
        ├── rclcpp
        ├── rclpy
        └── README.md

4 directories, 3 files

## Source an underlay

It is important that we have sourced the environment for an existing ROS 2 installation that will provide our workspace with the necessary build dependencies for the example packages. This is achieved by sourcing the setup script provided by a binary installation or a source installation, ie. another colcon workspace (see Installation). We call this environment an underlay.

Our workspace, ros2_examples_ws, will be an overlay on top of the existing ROS 2 installation. In general, it is recommended to use an overlay when you plan to iterate on a small number of packages, rather than putting all of your packages into the same workspace.
Build the workspace

In the root of the workspace, run colcon build. Since build types such as ament_cmake do not support the concept of the devel space and require the package to be installed, colcon supports the option --symlink-install. This allows the installed files to be changed by changing the files in the source space (e.g. Python files or other not compiled resourced) for faster iteration.

colcon build --symlink-install

After the build is finished, we should see the build, install, and log directories:

.
├── build
├── install
├── log
└── src

4 directories, 0 files

## Run tests

To run tests for the packages we just built, run the following:

colcon test

## Source the environment

When colcon has completed building successfully, the output will be in the install directory. Before you can use any of the installed executables or libraries, you will need to add them to your path and library paths. colcon will have generated bash/bat files in the install directory to help setup the environment. These files will add all of the required elements to your path and library paths as well as provide any bash or shell commands exported by packages.

. install/setup.bash

## Background

ROS 2 relies on the notion of combining workspaces using the shell environment. “Workspace” is a ROS term for the location on your system where you’re developing with ROS 2. The core ROS 2 workspace is called the underlay. Subsequent local workspaces are called overlays. When developing with ROS 2, you will typically have several workspaces active concurrently.

Combining workspaces makes developing against different versions of ROS 2, or against different sets of packages, easier. It also allows the installation of several ROS 2 distributions (or “distros”, e.g. Dashing and Eloquent) on the same computer and switching between them.

This is accomplished by sourcing setup files every time you open a new shell, or by adding the source command to your shell startup script once. Without sourcing the setup files, you won’t be able to access ROS 2 commands, or find or use ROS 2 packages. In other words, you won’t be able to use ROS 2.
Prerequisites

Before starting these tutorials, install ROS 2 by following the instructions on the ROS 2 Installation page.

The commands used in this tutorial assume you followed the binary packages installation guide for your operating system (Debian packages for Linux). You can still follow along if you built from source, but the path to your setup files will likely be different. You also won’t be able to use the sudo apt install ros-<distro>-<package> command (used frequently in the beginner level tutorials) if you install from source.

If you are using Linux or macOS, but are not already familiar with the shell, this tutorial will help.
## Tasks
1. Source the setup files

You will need to run this command on every new shell you open to have access to the ROS 2 commands, like so:
```
source /opt/ros/galactic/setup.bash
```

Note

The exact command depends on where you installed ROS 2. If you’re having problems, ensure the file path leads to your installation.
2. Add sourcing to your shell startup script

If you don’t want to have to source the setup file every time you open a new shell (skipping task 1), then you can add the command to your shell startup script:

    echo "source /opt/ros/galactic/setup.bash" >> ~/.bashrc

To undo this, locate your system’s shell startup script and remove the appended source command.
3. Add colcon_cd to your shell startup script

The command colcon_cd allows you to quickly change the current working directory of your shell to the directory of a package. As an example colcon_cd some_ros_package would quickly bring you to the directory ~/ros2_install/src/some_ros_package.
```
echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.bashrc
echo "export _colcon_cd_root=~/ros2_install" >> ~/.bashrc
```
Depending to the way you installed colcon_cd and where your workspace is, the instructions above may vary, please refer to the documentation for more details. To undo this in Linux and macOS, locate your system’s shell startup script and remove the appended source and export commands.
4. Check environment variables

Sourcing ROS 2 setup files will set several environment variables necessary for operating ROS 2. If you ever have problems finding or using your ROS 2 packages, make sure that your environment is properly setup using the following command:
```
printenv | grep -i ROS
```

Check that variables like ROS_DISTRO and ROS_VERSION are set.

ROS_VERSION=2
ROS_PYTHON_VERSION=3
ROS_DISTRO=galactic

If the environment variables are not set correctly, return to the ROS 2 package installation section of the installation guide you followed. If you need more specific help (because environment setup files can come from different places), you can get answers from the community.
3.1 The ROS_DOMAIN_ID variable

See the domain ID article for details on ROS domain IDs.

Once you have determined a unique integer for your group of ROS 2 agents, you can set the environment variable with the following command:
```
export ROS_DOMAIN_ID=<your_domain_id>
```
To maintain this setting between shell sessions, you can add the command to your shell startup script:
```
echo "export ROS_DOMAIN_ID=<your_domain_id>" >> ~/.bashrc
```
## Summary

The ROS 2 development environment needs to be correctly configured before use. This can be done in two ways: either sourcing the setup files in every new shell you open, or adding the source command to your startup script.

If you ever face any problems locating or using packages with ROS 2, the first thing you should do is check your environment variables and ensure they are set to the version and distro you intended.



