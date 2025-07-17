## Building the Docker Image

To build the Docker image required for this project, navigate to the directory named `Dockerfile` (which contains the actual `Dockerfile`) and run the following command:

```bash
docker image build -t my-vulcanexus:humble-desktop .
```

## Running the Docker Container

To run the Docker container based on the previously built image, make sure you are located inside the directory that contains the `ros2_ws` folder. This folder is required because it will be mounted into the container at runtime. The `ros2_ws` directory is included in the GitHub project.

```bash
docker run -it --rm --name vulcanexus-container --user vulcanexus_user \
  -v $PWD/ros2_ws:/ros2_ws -w /ros2_ws \
  --network=host --ipc=host \
  -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  my-vulcanexus:humble-desktop
```

## First-Time Container Setup

After launching the container for the first time, you need to perform some initial setup steps inside the container. These commands should be executed inside the `/ros2_ws` directory.

```bash
colcon build

source /opt/ros/humble/setup.bash
source install/setup.bash
export GAZEBO_PLUGIN_PATH=/opt/ros/humble/lib

rosdep update && rosdep install --ignore-src --from-paths . -y
```

## Opening a New Terminal Inside the Running Container


```bash
docker exec -it vulcanexus-container /bin/bash

source /opt/ros/humble/setup.bash
source install/setup.bash
export GAZEBO_PLUGIN_PATH=/opt/ros/humble/lib
```

## Rebuilding After Changes

Every time you make changes to the workspace files inside the container — such as modifying source code, updating packages, or changing build configuration — you need to clean and rebuild the workspace (inside the `/ros2_ws` directory).

```bash
rm -rf build/ log/ install/
colcon build

source /opt/ros/humble/setup.bash
source install/setup.bash
export GAZEBO_PLUGIN_PATH=/opt/ros/humble/lib
```

## Visualizing the Robot in Gazebo or RViz

```bash
ros2 launch ur_simulation_gazebo ur_sim_control.launch.py ur_type:=ur10e
ros2 launch ur_description view_ur.launch.py ur_type:=ur10
```

## Launching the Robot in Gazebo using `spraying_pathways` package

```bash
ros2 launch spraying_pathways bringup.launch.py
```

## Sending the Robot to its Home Position

```bash
ros2 run spraying_pathways go_home_node
```

## Executing Surface Scan or Scan & Glue Spraying

Before running any of the commands below, make sure the robot is already at its home position.

```bash
ros2 run spraying_pathways cartesian_path_planner_trajectory_v1_node
ros2 run spraying_pathways cartesian_path_planner_cubes_v2_node
ros2 run spraying_pathways cartesian_path_planner_cubes_v3_node
```
