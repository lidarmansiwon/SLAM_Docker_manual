# SLAM_Docker_manual
This repository contains the SLAM_Docker documentation created by lidarsiwon

docker.io page -->  https://hub.docker.com/repository/docker/lidarmansiwon/macro_slam/general

## 1. Pull docker image from Docker server

arm64 users --> 
``` 
docker pull lidarmansiwon/macro_slam_arm64:2.0
```

amd64 users -->
```
docker pull lidarmansiwon/macro_slam:2.0
```

if you need Permission, using "```sudo```"

``` sudo docker images ```  --> Check the pulled image

**output** :  

```
REPOSITORY                            TAG                    IMAGE ID            CREATED             SIZE
lidarmansiwon/macro_slam_arm64        2.0                    812ae31625b3      10 minutes ago        4.04GB
```

## 2. Make lidarmansiwon/macro_slam docker container  

When you create a docker container, you need several options to use the GUI and share folders.

``` 
docker run -it -d --rm -e DISPLAY=:0 -v /tmp/.X11-unix:/tmp/.X11-unix --pid=host --ipc=host lidarmansiwon/macro_slam_arm64:2.0
```

or

**If bashrc already has an alias** 

```
slam_docker
``` 


```
docker ps
```

**output** :  

```
CONTAINER ID                            TAG                    IMAGE ID            CREATED             SIZE
lidarmansiwon/macro_slam_arm64        2.0                    812ae31625b3      10 minutes ago        4.04GB
```

## 3. Attach lidarmansiwon/macro_slam docker container and launch slam

``` sudo docker exec -it [container ID] bash ```

``` ros2 launch lidarslam lidarslam.launch.py ```

or 

``` ros2 launch hdl_localization hdl_localization_2.launch.py```

**If bashrc already has an alias** 

``` sudo docker exec -it [container ID] bash ```

``` mapping ``` 

``` localization ```

## If you need to edit ROS_NAMESPACE 

If you need to edit ROS_NAMESPACE. Make sure to modify the file below

![Untitled (53)](https://github.com/lidarmansiwon/SLAM_Docker_manual/assets/117976120/bb923350-f9fc-46d6-95e4-7216e20b7f9e)


