## Building the Docker Image

To build the Docker image required for this project, navigate to the directory named `Dockerfile` (which contains the actual `Dockerfile`) and run the following command:

```bash
docker image build -t my-vulcanexus:humble-desktop .

## Running the Docker Container

To run the Docker container based on the previously built image, make sure you are located inside the directory that contains the `ros2_ws` folder. This folder is required because it will be mounted into the container at runtime. The `ros2_ws` directory is included in the GitHub project.

```bash
docker run -it --rm --name vulcanexus-container --user vulcanexus_user \
  -v $PWD/ros2_ws:/ros2_ws -w /ros2_ws \
  --network=host --ipc=host \
  -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  my-vulcanexus:humble-desktop
