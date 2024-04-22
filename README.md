# SLAM_Docker_manual
This repository contains the SLAM_Docker documentation created by lidarsiwon

docker.io page -->  https://hub.docker.com/repository/docker/lidarmansiwon/macro_slam/general

## Terminal Command sequence (If bashrc already has an alias for slam_docker)
```
slam_docker      
sudo docker ps   
sudo docker exec -it [container ID] bash  ## docker terminal 접속 명령어

# In docker terminal
medit         ## Edit for mapping parameter. You should match pointcloud topic and if you want, you can edit tf or other parameter.
mparam        ## If you edit mapping param. You can use this command
ledit         ## Edit for localization parameter. 
mapping
map_save      ## You can save map.pcd in path "/". Delete a file if it already exists in that location before using the command. [ros2 service call /map_save std_srvs/Empty] 
localization     
```

## 1. Pull docker image from Docker server

arm64 users --> 
``` 
docker pull lidarmansiwon/macro_slam_arm64:2.0
```

amd64 users -->
```
docker pull lidarmansiwon/macro_slam:5.0
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

## How to use slam 

Before you launch mapping, you can consider about tf. If you use ouster. you can choose os_sensor or os_lidar.
(맵핑하기 이전에 반드시 원하는 축으로 회전한뒤 매핑하여야함.)

```
    tf = launch_ros.actions.Node(
        package='tf2_ros',
        executable='static_transform_publisher',
        namespace=ros_namespace,
        arguments=['0','0','0','0','0','1','0','base_link','os_lidar']  # Change " w = 0 , z = 1 " from " w = 1 , z = 0 " cause changed os_lidar from os_sensor
        )
```


If you launch mapping(alias: ros2 launch lidarslam lidarslam.launch.py)and you got a well map. you should save map.pcd
(아래 명령어로 맵 데이터를 저장할 수 있다.)

```
ros2 service call /map_save std_srvs/Empty
```

You can find map.pcd file and pose_graph.g2o file in
After you find file. you can get path with command "pwd"
("pwd"로 경로 따서 런치 파일 수정해줘야함.)

And Then, For using localization you should edit hdl_localization_2.launch.py(/hdl-localization-ROS2/hdl_localization/launch/)

```
                    {'globalmap_pcd': '/home/sw/ros2_ws/src/lidarslam_ros2/map/map.pcd'},
                    {'convert_utm_to_local': True},
                    {'downsample_resolution': 0.3}]),  # 0.1 to 0.5
```
You should change globalmap_pcd path to your map.pcd and g2o path.

Also You can use tf!

```
    lidar_tf = Node(
        name='lidar_tf',
        package='tf2_ros',
        namespace=ros_namespace,
        executable='static_transform_publisher',
        arguments=['0.0', '0.0', '0.0', '0.0', '1.0',
                   '0.0', '0.0', 'odom', 'os_lidar']
    )
```

- frontend(scan-matcher)

|Name|Type|Default value|Description|
|---|---|---|---|
|registration_method|string|"NDT"|"NDT" or "GICP"|
|ndt_resolution|double|5.0|resolution size of voxel[m]|
|ndt_num_threads|int|0|threads using ndt(if `0` is set, maximum alloawble threads are used.)(The higher the number, the better, but reduce it if the CPU processing is too large to estimate its own position.)|
|trans_for_mapupdate|double|1.5|moving distance of map update[m]|

- backend(graph-based-slam)

|Name|Type|Default value|Description|
|---|---|---|---|
|registration_method|string|"NDT"|"NDT" or "GICP"|
|ndt_resolution|double|5.0|resolution size of voxel[m]|
|ndt_num_threads|int|0|threads using ndt(if `0` is set, maximum alloawble threads are used.)|
|voxel_leaf_size|double|0.2|down sample size of input cloud[m]|
|loop_detection_period|int|1000|period of searching loop detection[ms]|
|threshold_loop_closure_score|double|1.0| fitness score of ndt for loop clousure|
|distance_loop_closure|double|20.0| distance far from revisit candidates for loop clousure[m]|
|range_of_searching_loop_closure|double|20.0|search radius for candidate points from the present for loop closure[m]|
|search_submap_num|int|2|the number of submap points before and after the revisit point used for registration|
|num_adjacent_pose_cnstraints|int|5|the number of constraints between successive nodes in a pose graph over time|
|use_save_map_in_loop|bool|true|Whether to save the map when loop close(If the map saving process in loop close is too heavy and the self-position estimation fails, set this to `false`.)|

For example! 

Used parameters for indoor environment:
ndt_resolution: 1.0
ndt_num_threads: 5
trans_for_mapupdate: 0.2
voxel_leaf_size: 0.1
