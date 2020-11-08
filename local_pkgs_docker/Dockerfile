FROM ros:foxy

# install ros build tools
RUN apt-get update && apt-get install -y \
      python3-colcon-common-extensions && \
    rm -rf /var/lib/apt/lists/*

# copy demos files
ENV ROS_WS /opt/ros_ws
RUN mkdir -p $ROS_WS/src
COPY demos /opt/ros_ws/src/demos
WORKDIR $ROS_WS


# install ros package dependencies
RUN apt-get update && \
    rosdep update && \
    rosdep install -y \
      --from-paths \
        src/demos/demo_nodes_cpp \
        src/demos/demo_nodes_py \
      --ignore-src && \
    rm -rf /var/lib/apt/lists/*


# build ros package source
RUN . /opt/ros/$ROS_DISTRO/setup.sh && \
    colcon build \
      --packages-select \
        demo_nodes_cpp \
        demo_nodes_py \
      --mixin $OVERLAY_MIXINS

# source entrypoint setup
# copy ros package install via multi-stage
FROM ros:foxy
ENV ROS_WS /opt/ros_ws
COPY --from=0  $ROS_WS/install $ROS_WS/install
RUN sed --in-place --expression \
      '$isource "$ROS_WS/install/setup.bash"' \
      /ros_entrypoint.sh

#ENTRYPOINT ["/opt/ros/overlay_ws/ros_entrypoint.sh"]
# run launch file
CMD ["ros2", "launch", "demo_nodes_cpp", "talker_listener.launch.py"]
#CMD["bash"]
# CMD ["ros2", "launch", "turtlebot3_gazebo", "empty_world.launch.py"]
#         src/turtlebot3/turtlebot3/turtlebot3_bringup \
#        src/turtlebot3/turtlebot3/turtlebot3_description \
#        src/turtlebot3/turtlebot3/turtlebot3_example \
#        src/turtlebot3/turtlebot3/turtlebot3_navigation \
#        src/turtlebot3/turtlebot3/turtlebot3_slam \
#       src/turtlebot3/turtlebot3/turtlebot3_teleop \