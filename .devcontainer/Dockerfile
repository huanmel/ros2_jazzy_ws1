FROM osrf/ros:jazzy-desktop-full
ARG USERNAME=USERNAME
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Delete user if it exists in container (e.g Ubuntu Noble: ubuntu)
RUN if id -u $USER_UID ; then userdel `id -un $USER_UID` ; fi

# Install some dependencies packages
RUN apt update -q \
    && apt upgrade -q -y \
    && apt install -y --no-install-recommends \
    software-properties-common \
    python3-pip \
    xauth \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME


    # Create workspace so that user own this directory
RUN mkdir -p /home/$USERNAME/ros2_ws/src
WORKDIR /home/$USERNAME/ros2_ws

# Setup bashrc
## Make ros2 public to the system via `source` command
RUN echo "source /opt/ros/$ROS_DISTRO/setup.bash" >> /home/$USERNAME/.bashrc

## Check if own build has been made; if yes, source it 
RUN echo "if [ -f \"/home/$USERNAME/ros2_ws/install/setup.bash\" ]; then" >> /home/$USERNAME/.bashrc \
    && echo "    source /home/$USERNAME/ros2_ws/install/setup.bash" >> /home/$USERNAME/.bashrc \
    && echo "else" >> /home/$USERNAME/.bashrc \
    && echo "    echo \"/home/$USERNAME/ros2_ws/install/setup.bash has not yet been created.\"" >> /home/$USERNAME/.bashrc \
    && echo "    echo \"Make sure to build your package(s).\"" >> /home/$USERNAME/.bashrc \
    && echo "fi" >> /home/$USERNAME/.bashrc
    
# Setup entrypoint
COPY ./ros_entrypoint.sh /
ENTRYPOINT ["/ros_entrypoint.sh"]

# RUN apt-get update && apt-get upgrade -y
# RUN sudo apt update && sudo apt upgrade -y
# RUN apt-get install -y mesa-utils
# RUN apt-get install -y python3-pip
ENV SHELL /bin/bash

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME
CMD ["/bin/bash"]