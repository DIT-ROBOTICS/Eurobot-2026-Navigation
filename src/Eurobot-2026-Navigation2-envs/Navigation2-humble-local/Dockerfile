##############################################################################
# Base Image: ros2-humble
##############################################################################

FROM osrf/ros:humble-desktop-full-jammy

RUN apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get clean && rm -rf /var/lib/apt/lists/*

##############################################################################
# Arguments
##############################################################################

ARG USERNAME=user
ARG USERID=1000
ARG USERGID=${USERID}

##############################################################################
# Label
##############################################################################

LABEL org.opencontainers.image.authors="ginga"
LABEL shell="/bin/bash"

##############################################################################
# Set environment variables
##############################################################################

ENV USERNAME=${USERNAME}
ENV SHELL=/bin/bash
ENV TERM=xterm-256color
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV ROS_DISTRO=humble
ENV XDG_RUNTIME_DIR=/tmp/runtime-user

##############################################################################
# Install packages, Set Language, Set Timezone, and Clean apt-cache (Combined RUN)
##############################################################################

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    sudo \
    vim \
    curl \
    wget \
    tmux \
    htop \
    git \
    git-extras \
    gnupg2 \
    net-tools \
    locales \
    build-essential \
    cmake \
    python3-colcon-common-extensions \
    tzdata \
    libsdl-image1.2-dev \
    libsuitesparse-dev \
    libfreeimage-dev \
    libyaml-cpp-dev \
    libgl1-mesa-glx \
    libglu1-mesa \
    mesa-utils \
    ros-humble-tf2-sensor-msgs \
    ros-humble-libg2o \
    ros-humble-teleop-twist-keyboard \
    ros-humble-navigation2 \
    ros-humble-nav2-bringup \
    ros-humble-gazebo-ros-pkgs \
    ros-humble-geographic-msgs \
    ros-humble-geometry-msgs \
    ros-humble-test-msgs \
    ros-humble-bondcpp \
    ros-humble-foxglove-bridge \
    ros-humble-rmw-cyclonedds-cpp && \
    \
    # Set Language
    echo "en_US.UTF-8 UTF-8" > /etc/local.gen && locale-gen en_US.UTF-8 && \
    \
    # Set timezone (Taipei)
    TZ=Asia/Taipei && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata && \
    \
    # Clean apt-cache after all installs
    apt-get autoremove -y && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

##############################################################################
# Add new user (Passwordless with sudo)
##############################################################################

RUN groupadd --gid ${USERGID} ${USERNAME} \
    && useradd --uid ${USERID} --gid ${USERGID} -m ${USERNAME} -s /bin/bash \
    # passwordless
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME

##############################################################################
# Set Default User & Working Directory & Configure .bashrc
##############################################################################

USER ${USERNAME}
WORKDIR /home/${USERNAME}

# Configure .bashrc directly (added conditional checks for robustness)
RUN echo "# Custom .bashrc configurations for ROS2 and Nav2" >> ~/.bashrc && \
    echo " " >> ~/.bashrc && \
    echo "# Sourcing ROS2 Humble setup script" >> ~/.bashrc && \
    echo "if [ -f \"/opt/ros/humble/setup.bash\" ]; then" >> ~/.bashrc && \
    echo "  source /opt/ros/humble/setup.bash" >> ~/.bashrc && \
    echo "fi" >> ~/.bashrc && \
    echo " " >> ~/.bashrc && \
    echo "# Sourcing Gazebo setup script (Optional)" >> ~/.bashrc && \
    echo "if [ -f \"/usr/share/gazebo/setup.sh\" ]; then" >> ~/.bashrc && \
    echo "  source /usr/share/gazebo/setup.sh" >> ~/.bashrc && \
    echo "fi" >> ~/.bashrc && \
    echo " " >> ~/.bashrc && \
    echo "if [ -f \"/home/${USERNAME}/Eurobot-2026-Navigation2-ws/install/setup.bash\" ]; then" >> ~/.bashrc && \
    echo "  source \"/home/${USERNAME}/Eurobot-2026-Navigation2-ws/install/setup.bash\"" >> ~/.bashrc && \
    echo "fi" >> ~/.bashrc && \
    echo " " >> ~/.bashrc && \
    echo " " >> ~/.bashrc && \
    echo "alias cb=\"colcon build --symlink-install\"" >> ~/.bashrc && \
    echo "alias cbs=\"colcon build --symlink-install --packages-select\"" >> ~/.bashrc

##############################################################################
# Copy tf2_sensor_msgs to avoid error from building navigation2
##############################################################################

COPY ./settings/tf2_sensor_msgs.hpp /opt/ros/humble/include/tf2_sensor_msgs/tf2_sensor_msgs.hpp

##############################################################################
# Default command
##############################################################################

SHELL ["/bin/bash", "-c"]
CMD ["bash"]