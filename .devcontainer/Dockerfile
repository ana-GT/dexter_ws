FROM osrf/ros:rolling-desktop
ENV DEBIAN_FRONTEND=noninteractive

# Set user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  && apt-get update \
  && apt-get install -y sudo \
  && echo $USERNAME ALLL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
  && chmod 0440 /etc/sudoers.d/$USERNAME

# Set bash shell
SHELL ["/bin/bash", "-c"]

RUN apt-get update \
# Needed for OpenGL fix for Rviz to display
 && apt-get install -y software-properties-common \
 && add-apt-repository -y ppa:kisak/kisak-mesa \ 
 && apt update \ 
 && apt -y upgrade 

RUN apt-get install -y \
  libnlopt-dev \
  libnlopt-cxx-dev \
  ros-rolling-xacro \
  ros-rolling-joint-state-publisher \
  ros-rolling-srdfdom \
  ros-rolling-controller-interface \
  ros-rolling-realtime-tools \
  ros-rolling-control-toolbox \
  ros-rolling-joint-state-publisher-gui \
  ros-rolling-geometric-shapes \
  ros-rolling-controller-manager \
  ros-rolling-joint-trajectory-controller

#   \
#  && rm -rf /var/lib/apt/lists/* 

# Set user from root to non-root
USER $USERNAME

# Disable StrictHostKeyChecking
ENV GIT_SSH_COMMAND="ssh -o StrictHostKeyChecking=no"

# Copy the workspace into container
RUN mkdir -p /home/$USERNAME/dexter_ws
WORKDIR /home/$USERNAME/dexter_ws
ARG SRC_DIR="src"
COPY --chown=$USERNAME:$USERNAME $SRC_DIR src/.

# Install dependencies
RUN rosdep update && \
    rosdep install --from-paths src --ignore-src --default-yes -r || true


# Build workspace
#RUN \
#    source /opt/ros/rolling/setup.bash && \
#    colcon build --symlink-install

# Clear apt-cache to reduce image size
#RUN \ 
#    rm -rf /var/lib/apt/lists/*

# Sourcing the workspace
#RUN echo "source /home/$USERNAME/dexter_ws/install/setup.bash" >> /home/$USERNAME/.bashrc

#  ros-rolling-libfranka \
#  ros-rolling-object-recognition-msgs \
#  ros-rolling-octomap-msgs \
#  ros-rolling-ros-testing \
#  ros-rolling-eigen-stl-containers \
#  ros-rolling-generate-parameter-library \
#  libfcl-dev \
#  ros-rolling-ruckig \
#  ros-rolling-control-msgs \
#  ros-rolling-controller-manager-msgs \
#  ros-rolling-stomp \
#  freeglut3-dev \
#  ros-rolling-graph-msgs \
#  ros-rolling-warehouse-ros \
#  libompl-dev \
#  ros-rolling-control-toolbox \
#  ros-rolling-launch-param-builder \
#  ros-rolling-joint-state-broadcaster \

