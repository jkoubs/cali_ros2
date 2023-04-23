FROM osrf/ros:galactic-desktop
ENV DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

# dependencies
RUN apt-get update --fix-missing && \
    apt-get install -y git && \
    apt-get install -y build-essential \
                       terminator \
                       nano \
                       vim \
                       python3-pip \
                       libeigen3-dev \ 
                       tree \
                       wget \
                       bash-completion \
                       gedit \
                       ros-galactic-turtlebot3-gazebo \
                       ros-galactic-nav2-simple-commander \
                       ros-galactic-turtlebot3 \
                       ros-galactic-gazebo-ros-pkgs && \
    apt install qtbase5-dev \
                libqt5svg5-dev \
                libzmq3-dev \
                libdw-dev 
                
RUN apt-get -y dist-upgrade
RUN pip3 install transforms3d
# transforms3d = Functions for 3D coordinate transformations
# Conversions between different representations: 3x3 rotation matrices, Euler angles, quaternions

# Create env_ws containing the turtlebot3 pkgs
RUN mkdir -p /env_ws/src/turtlebot3_pkgs

# Source and Git clone turtlebot3_simulations, turtlebot3_msgs and turtlebot3 pkgs 
RUN source /opt/ros/galactic/setup.bash && \
    apt-get update --fix-missing && \
    cd /env_ws/src/turtlebot3_pkgs  && \
    git clone -b galactic-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git && \
    git clone -b galactic-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git && \
    git clone -b galactic-devel https://github.com/ROBOTIS-GIT/turtlebot3.git && \
    cd /env_ws/src  && \
    git clone https://github.com/BehaviorTree/Groot.git && \
    cd Groot && \
    git submodule update --init --recursive && \
    mkdir build; cd build && \
    cmake .. && \
    make && \
    cd /env_ws && \
    # This installs dpdencies declared in package.xml from all pkgs in the src folder for ROS 2 Galactic
    rosdep install -i --from-path src --rosdistro galactic -y && \
    # Build env_ws
    colcon build --symlink-install
  

# Source automatically the underlay (all necessary setup to run ROS2) (add it to .bashrc file)
RUN echo "source /opt/ros/galactic/setup.bash" >> ~/.bashrc
# Source automatically the overlay (workspace -> env_ws) (add it to .bashrc file)
RUN echo "source /env_ws/install/setup.bash" >> ~/.bashrc
RUN echo "source /usr/share/gazebo/setup.sh" >> ~/.bashrc

COPY ros_entrypoint.sh /ros_entrypoint.sh
# Set Working directory
WORKDIR '/'
ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
FROM galactic_tb_env:latest
# ENV DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

RUN echo "source /ros2_ws/install/setup.bash" >> ~/.bashrc

# Setup colcon_cd
RUN echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.bashrc
RUN echo "export _colcon_cd_root=/opt/ros/galactic/" >> ~/.bashrc

# Setup colcon tab completion
RUN echo "source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash" >> ~/.bashrc


COPY /ros2_ws/ /ros2_ws/
RUN source /opt/ros/galactic/setup.bash && \
    apt-get update --fix-missing && \
    cd /ros2_ws  && \
    # This installs dpdencies declared in package.xml from all pkgs in the src folder for ROS 2 Galactic
    rosdep install -i --from-path src --rosdistro galactic -y && \
    # Build env_ws
    colcon build --symlink-install 

# Set Working directory
WORKDIR '/ros2_ws'
# ENTRYPOINT ["/ros_entrypoint.sh"]
