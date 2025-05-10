# INML Ros Noetic Desktop Image on ARC
SSH into ARC with X11 forwarding enabled.
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

Sandbox 
```
apptainer shell   --writable --fakeroot --env DISPLAY=$DISPLAY  --bind $HOME/.X11-unix:/tmp/.X11-unix   --bind $HOME/.Xauthority:/root/.Xauthority   --bind /dev/dri:/dev/dri ros_noetic_desktop_sandbox/
```
