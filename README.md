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
$ sudo docker run hello-world
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
$ mkdir ~/ros2_docker

$ cd ~/ros2_docker

$ $EDITOR Dockerfile
```

Insert the following in the *Dockerfile*, and save it:

```
FROM ros:foxy

# install ros package
RUN apt-get update && apt-get install -y \
      ros-${ROS_DISTRO}-demo-nodes-cpp \
      ros-${ROS_DISTRO}-demo-nodes-py && \
    rm -rf /var/lib/apt/lists/* && mkdir /ros2_home

WORKDIR /ros2_home

# launch ros package
CMD ["ros2", "launch", "demo_nodes_cpp", "talker_listener.launch.py"]
```

* __FROM__: creates a layer from the ros:foxy Docker image
* __RUN__: builds your container by installing vim into it and creating a directory called /ros2_home
* __WORKDIR__: informs the container where the working directory should be for it

Of course, you are free to change the ROS distribution (_foxy_ is used here) or change the directory name.
The above docker file sets up ROS-foxy and installs the demo nodes for C++ and Python. Then it launches a file which runs a talker and a listener node. We will see it in action in just a few, but they act very similar to the publisher-subscriber example found in the [ROS wiki](https://wiki.ros.org/ROS/Tutorials/WritingPublisherSubscriber%28c%2B%2B%29)

Now, we are ready to build the docker image to run ROS2 in it (yes, it is THAT easy!). 

__Note__: if you have errors due to insufficient privileges or _permission denied_, try running the command with _sudo_ privileges:

```
$ docker build .

# You will see a bunch of lines that execute the docker file instructions followed by:
Successfully built 0dc6ce7cb487
```

_0dc6ce7cb487_ will most probably be different for you, so keep note of it and copy it somewhere for reference. 
You can always go back and check the docker images you have using:

```
$ sudo docker ps -as
```

Now, run the docker file using:

```
$ docker run -it 0dc6ce7cb487
[INFO] [launch]: All log files can be found below /root/.ros/log/2020-10-28-02-41-45-177546-0b5d9ed123be-1
[INFO] [launch]: Default logging verbosity is set to INFO
[INFO] [talker-1]: process started with pid [28]
[INFO] [listener-2]: process started with pid [30]
[talker-1] [INFO] [1603852907.249886590] [talker]: Publishing: 'Hello World: 1'
[listener-2] [INFO] [1603852907.250964490] [listener]: I heard: [Hello World: 1]
[talker-1] [INFO] [1603852908.249786312] [talker]: Publishing: 'Hello World: 2'
[listener-2] [INFO] [1603852908.250453386] [listener]: I heard: [Hello World: 2]
[talker-1] [INFO] [1603852909.249882257] [talker]: Publishing: 'Hello World: 3'
[listener-2] [INFO] [1603852909.250536089] [listener]: I heard: [Hello World: 3]
[talker-1] [INFO] [1603852910.249845718] [talker]: Publishing: 'Hello World: 4'
[listener-2] [INFO] [1603852910.250509355] [listener]: I heard: [Hello World: 4]
[talker-1] [INFO] [1603852911.249506058] [talker]: Publishing: 'Hello World: 5'
[listener-2] [INFO] [1603852911.250152324] [listener]: I heard: [Hello World: 5]
[talker-1] [INFO] [1603852912.249556670] [talker]: Publishing: 'Hello World: 6'
[listener-2] [INFO] [1603852912.250212678] [listener]: I heard: [Hello World: 6]
```

If it works correctly, you should see something similar to what is shown above. As can be seen, there are two ROS nodes (a publisher and a subscriber) running and their output is provided to us.