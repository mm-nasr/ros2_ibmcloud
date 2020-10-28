# ROS2 on IBM Cloud

This repository holds instructions on how to run ROS2 on IBM Cloud with your custom packages. It also holds sample files for illustration.

ROS2 is the new generation of ROS which gives more control over multi-robot formations. With the advancements of cloud computing, cloud robotics are becoming more important in today's age. In this tutorial, we will go through a short introduction on running ROS2 on IBM Cloud. By the end of the tutorial, you will be able to create your own packages in ROS2 and deploy them to the cloud using docker files. 

The following instructions assume you're using Linux and have been tested with Ubuntu 18.04 (Bionic Beaver).

## Step 1: Setting up your system
Before we go into how the exact process works, lets first make sure all the required software is properly installed. We'll point you towards the appropriate sources to set up your system and only highlight the details that pertain to our use-case.

### a) Docker files?

Docker files are a form of containers that can run separate from your system, this way, you can set-up potentially hundreds of different projects without affecting one another. You can even set-up different versions of Linux on one machine, without the need for virtual machine. Docker files have an advantage of saving space and only utilizing your system resources when in use. In addition, dockers are versatile and transferable. They contain all the required pre-requisites to run separately, meaning that you can easily use a docker file for a specific system or service without any other setup!
Excited yet? Let's start off by installing docker to your system by following the following tutorial.
From the tutorial, you should have done some sanity checks to make sure docker is properly set-up. Just in case, however, let's run the following command once again that uses the hello-world docker image:
```
sudo docker run hello-world
```

You should obtain the following output:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

### b) ROS2 Image

ROS [announced](https://discourse.ros.org/t/announcing-official-docker-images-for-ros2/7381/2) image containers for several ROS distributions in January 2019.
More detailed instructions on the use of ROS2 docker images can be found [here](https://hub.docker.com/_/ros/).

Let's skip through that and get to real-deal right away; creating a local ROS2 docker. We'll create our own Dockerfile since we'll need this method for deployment on IBM Cloud. First, we create a new directory which will hold our Dockerfile and any other files we need later on and navigate to it. Using your favorite $EDITOR of choice, open a new file named *Dockerfile* (make sure the file naming is correct):

```
mkdir ~/ros2_docker

cd ~/ros2_docker

$EDITOR Dockerfile
```

Insert the following in the *Dockerfile*, and save it:

```
FROM ros:crystal

# install ros packages for installed release
RUN apt-get update && apt-get install -y \
      ros-${ROS_DISTRO}-demo-nodes-cpp \
      ros-${ROS_DISTRO}-demo-nodes-py \
      ros-${ROS_DISTRO}-sros2 && \
    rm -rf /var/lib/apt/lists/*

# run ros package launch file
CMD ["ros2", "launch", "demo_nodes_cpp", "talker_listener.launch.py"]
```

