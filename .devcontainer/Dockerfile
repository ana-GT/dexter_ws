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
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
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
    rosdep install --from-paths src --ignore-src --default-yes -r || true && \
    sudo rm -rf /var/lib/apt/lists/*

# Build workspace
RUN \
    source /opt/ros/rolling/setup.bash && \
    colcon build  && \ 
    # Update entrypoint to source our new workspace && \
    echo -e '#!/bin/bash \nsource "/home/vscode/dexter_ws/install/setup.bash"\nexec "$@"' > /dexter_entrypoint.sh

ENTRYPOINT ["/dexter_entrypoint.sh"]
