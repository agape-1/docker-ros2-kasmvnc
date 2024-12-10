## docker-ros2-kasmvnc

Run ROS2 and Gazebo using [KasmVNC](https://github.com/linuxserver/docker-baseimage-kasmvnc).

### Requirements

* Docker

### Getting started

#### Docker CLI

```bash
docker run \
  -p 3000:3000 \
  -p 9002:9002 \
  --security-opt seccomp:unconfined \
  --shm-size 512m \
  -e PYTHONUNBUFFERED=1 \
  -e XDG_RUNTIME_DIR \
  -e PUID=1000 \ 
  -e PGID=1000 \ 
  -e VNC_PORT=3000 \
  -e WEBSOCKET_PORT=9002 \
  ghcr.io/agape-1/docker-ros2-kasmvnc:jazzy-harmonic
```

##### Nvidia GPUs
If you have Docker Nvidia support, run this command instead:

```bash
docker run
  -p 3000:3000 \
  -p 9002:9002 \
  --security-opt seccomp:unconfined \
  --shm-size 512m \
  -e PYTHONUNBUFFERED=1 \
  -e XDG_RUNTIME_DIR \
  -e PUID=1000 \ 
  -e PGID=1000 \ 
  -e VNC_PORT=3000 \
  -e WEBSOCKET_PORT=9002 \
  -e NVIDIA_VISIBLE_DEVICES=all \
  -e NVIDIA_DRIVER_CAPABILITIES=all \
  -e __NV_PRIME_RENDER_OFFLOAD=1 \
  -e __GLX_VENDOR_LIBRARY_NAME=nvidia \
  --runtime nvidia \ 
  --gpus 1 \
  ghcr.io/agape-1/docker-ros2-kasmvnc:jazzy-harmonic
```

Replace `jazzy` and `harmonic` with the ROS distrubiton and Gazebo version of choice, respectively. See [supported versions](#supported-versions) for more details.

#### Local Build
Clone the repository and run the following command:


```bash
docker compose up gz_sim
```

##### Nvidia GPUs
If you have Docker Nvidia support, run this command instead:

```bash
docker compose up gz_sim_nvidia
```

### Accessing Gazebo 
Visit http://localhost:3000 or `http://localhost:$VNC_PORT` to access the Gazebo GUI.

This simulation also supports Gazebo's [visualization](https://app.gazebosim.org/visualization) tool. Access the tool and enter ws://localhost:9002 or `ws://localhost:$WEBSOCKET_PORT` and press 'Connect'.

### Quick tips

* If you accidentally closed the application and ended up with a blank screen, simply right click the background and you'll see an application menu to reopen Gazebo.

* Use a Chromium based browser for seamless clipboard integration.

* By default, the docker container exposes the network across `0.0.0.0`, which allows other clients to access your simulation from their browser. To add authentication, supply a `CUSTOM_USER` and `CUSTOM_PASSWORD` variables to `.env` and update the container from the [configuration](#configuration) section.

* Additional VNC [options](https://github.com/linuxserver/docker-baseimage-kasmvnc?tab=readme-ov-file#options) are available.

### Supported Versions

All currnet non-EOL ROS [versions](https://docs.ros.org/en/rolling/Releases.html) are supported with the recommended Gazebo [releases]([https://gazebosim.org/docs/latest/ros_installation/](https://gazebosim.org/docs/latest/ros_installation/#summary-of-compatible-ros-and-gazebo-combinations)).

The supported builds are always viewable from the following github [workflow](https://github.com/agape-1/docker-ros2-kasmvnc/blob/release/.github/workflows/default-build.yml):

* docker-ros2-kasmvnc:jazzy-harmonic
* docker-ros2-kasmvnc:humble-fortress
* docker-ros2-kasmvnc:iron-fortress
* docker-ros2-kasmvnc:rolling-ionic


### Configuration

Environment variables are supplied in `.env` for quick and intuitive flexibility. Simply change the variables and run the following command:

```
docker compose up gz_sim --force-recreate --build
```

to rerun the container with the updated configuration. Adjust as necessary similar to [nvidia startup steps](#nvidia-gpus).

#### .env Table


[//]: # (Table was generated by ChatGPT and then modified/double checked for its contents)

| Variable Name              | Default Value               | Description                                                              |
|----------------------------|-----------------------------|--------------------------------------------------------------------------|
| `UBUNTU_DISTRO`            | `jammy`                   | Specifies the underlying Ubuntu base distribution to use.          |
| `ROS_DISTRO`               | `humble`                   | Specifies the ROS (Robot Operating System) distribution to use.          |
| `GZ_VERSION`               | `garden`                   | Defines the version of Gazebo to use.                                    |
| `WEBSOCKET_GZLAUNCH_FILE`  | `websocket.gzlaunch`       | Specifies the Gazebo/IGN launch file for WebSocket configuration. Unless you need to configure gz launch, this can be safely ignored.           |
| `GZ_SIM_OPTIONS`           | (empty)                    | Options for customizing Gazebo simulator options at runtime, identical to `gz sim $GZ_SIM_OPTIONS`. Empty by default.     |
| `WEBSOCKET_PORT`           | `9002`                     | Port number used for Gazebo's [visualization](https://app.gazebosim.org/visualization) tool.                               |
| `VNC_PORT`                 | `3000`                     | Port number used to access the Gazebo GUI via the browser.         |

### Development

As a result of using [`KasmVNC`](https://github.com/linuxserver/docker-baseimage-kasmvnc), changes to the Docker container will require a full removal of the container before rebuilding.


```bash
docker stop $DOCKER_ID && docker rm $DOCKER_ID

docker compose up gz_sim --force-recreate --build

```

where `$DOCKER_ID` is the previously composed Docker container ID, if any.

#### Entrypoint

For compatibility purposes, the entrypoint has been placed in the [`autostart`](/root/defaults/autostart) file. Do not attempt to manually place a `ENTRYPOINT`layer in the [`Dockerfile`](/Dockerfile) to avoid VNC initialization failures.

### Acknowledgements and Related Projects

* Run Gazebo and ROS through X11 - https://github.com/brean/gz-sim-docker/
* Minimium Docker ROS2 build for Jazzy - https://github.com/UNF-Robotics/docker-ros2-jazzy/ 
* Run ROS2 Desktop using noVNC - https://github.com/Tiryoh/docker-ros2-desktop-vnc/
