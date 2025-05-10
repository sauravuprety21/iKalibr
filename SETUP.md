# INML Ros Noetic Desktop Image on ARC
SSH into ARC with X11 forwarding enabled. (if on linux MobaXterm has X11 forwarding into ARC by default)
```
ssh -X user.name@arc.ucalgary.ca
```
Test X11 forwarding of GUI is working (eyes will popup!)
```
xeyes
```
Allocate some compute resources as necessary, enable X11 here too
```
salloc -N1 -n1 -c8 --mem=64gb --gres=gpu:1 -p bigmem -t 1:00:00 --x11
```

# Sandbox
## Example - osrf\ros:noetic-desktop-full

```
mkdir /home/$USER/containers && cd /home/$USER/containers
```

### Sandbox setup
Build the ros-noetic-desktop-full image
```
apptainer build ros_noetic_desktop.image docker://osrf/ros:noetic-desktop-full
```

Build the sandbox for ros-noetic-desktop-full
```
apptainer build --sandbox ros_noetic_desktop_sandbox/ ros_noetic_desktop.sif
```

In the sandbox create /root/.Xauthority for --fakeroot (sudo privalaged)  mount
```
mkdir -p /home/$USER/containers/ros_noetic_desktop_sandbox/root && mkdir -p /home/$USER/containers/ros_noetic_desktop_sandbox/run/user/$UID
&& touch /home/$USER/containers/ros_noetic_desktop_sandbox/root/.Xauthority
```

### Test - rviz display 

Start an instance named ros_noetic. An instance is a running container which can be accessed through `shell` form other terminals. This is required when running ros (like `rviz`) prgorams which need a terminal dedicated to `roscore`

```
apptainer instance start --writable --fakeroot --nv --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID ros_noetic_desktop_sandbox/ ros_noetic
```

In a new terminal (on your local PC) ssh with -X into the ARC, then also ssh into the GPU environment. For example in my original terminal if this is the handle:
```
[saurav.uprety1@mmg2 containers]$
```

I will SSH into ARC again in my new terminal. 
```
ssh -X saurav.uprety1@ucalgary.ca 
```
Which will give me the following handle at first  `[saurav.uprety1@arc ~]$`, then must ssh one more time into `mmg2` to reach the GPU environment (say yes to the fingerprints details.)
```
ssh -X mmg2
```
Now my new shell will have the same handle `[saurav.uprety1@mmg2 ~]`, run the following command and ensure that you see the running `ros_noetic` apptainer instance. 
```
apptainer instance list
```
We will shell into this apptainer instance on both termnials. In one we will run `roscore`, the other we will run `rviz`.
#### Terminal 1 
Access `ros_noetic` instance through shell
```
apptainer shell --nv --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID instance://ros_noetic 
```
Then run roscore 
```
source /opt/ros/noetic/setup.bash && roscore
```
#### Terminal 2
```
cd /home/$USER/containers
```
Access `ros_noetic` instance through shell 
```
apptainer shell --nv --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID instance://ros_noetic
```
Then run rviz
```
source /opt/ros/noetic/setup.bash && rviz
```

apptainer shell --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID ros_noetic_desktop_sandbox/

Container
```
apptainer shell --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID ros_noetic_desktop.sif
```
```
apptainer instance start --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID ros_noetic_desktop.sif ros_noetic
```
```
apptainer shell --env DISPLAY=$DISPLAY --bind $HOME/.X11-unix:/tmp/.X11-unix --bind $HOME/.Xauthority:/root/.Xauthority --bind /dev/dri:/dev/dri --bind /run/user/$UID:/run/user/$UID ros_noetic_desktop.sif instace://ros_noetic
```


