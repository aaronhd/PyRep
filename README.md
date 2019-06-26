# PyRep [![Build Status](https://travis-ci.com/stepjam/PyRep.svg?token=bQxtiYV3p3bPYhzWMoLi&branch=master)](https://travis-ci.com/stepjam/PyRep)

__PyRep is a toolkit for robot learning research, built on top of the virtual
robotics experimentation platform ([V-REP](www.coppeliarobotics.com/)).__

- [Install](#install)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Supported Robots](#supported-robots)
- [Planned Future Updates](#planned-future-updates)
- [Contributing](#contributing)


## Install

In addition to the PyRep API, you will aso need to download the latest version of V-REP [from the downloads page](http://www.coppeliarobotics.com/downloads.html).

Once you have downloaded V-REP, you can pull PyRep from git:

```bash
git clone https://github.com/stepjam/PyRep.git
cd PyRep
```

Add the following to your *~/.bashrc* file: (__NOTE__: the 'EDIT ME' in the first line)

```bash
export VREP_ROOT=EDIT/ME/PATH/TO/V-REP/INSTALL/DIR
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$VREP_ROOT
export QT_QPA_PLATFORM_PLUGIN_PATH=$VREP_ROOT
```

__Remember to source your bashrc after this:__ ```source ~/.bashrc```.

Finally install the python library:

```bash
python3 setup.py install --user
```

You should be good to go!
Try running one of the examples in the *examples/* folder.

_Although you can use V-REP on any platform, communication via PyRep is currently only supported on Linux._

#### Running Headless

If you plan to run on a headless machine, you will also need to run with a virtual framebuffer. E.g.

```bash
sudo apt-get install xvfb
xvfb-run python3 my_pyrep_app.py
```

## Getting Started

1. First take a look at [Usage](#usage) and the examples in the *examples/* folder to see if PyRep might be able to accelerate your research.
2. Take a look at the V-REP [tutorials](http://www.coppeliarobotics.com/helpFiles/en/tutorials.htm).

## Usage

The best way to see how PyRep can help in your research is to look at the examples in the *examples/* folder!

#### Launching the simulation

```python
from pyrep import PyRep

pr = PyRep()
# Launch the application with a scene file in headless mode
pr.launch('scene.ttt', headless=True) 
pr.start()  # Start the simulation

# Do some stuff

pr.start()  # Stop the simulation
pr.shutdown()  # Close the application
```


#### Modifying the Scene

```python
from pyrep.objects.shape import Shape
from pyrep.const import PrimitiveShape

object = Shape.create(type=PrimitiveShape.CYLINDER, 
                      color=[r,g,b], size=[w, h, d],
                      position=[x, y, z])
object.set_color([r, g, b])
object.set_position([x, y, z])
```

#### Using Robots

Robots are designed to be modular; arms are treated separately to grippers.

Use the robot ttm files defined in robots/ttms. These have been altered slightly from the original ones shipped with V-REP to allow them to be used with motional planning out of the box. 
The 'tip' of the robot may not be where you want it, so feel free to play around with this.

```python
from pyrep import PyRep
from pyrep.robots.arms.panda import Panda
from pyrep.robots.end_effectors.panda_gripper import PandaGripper

pr = PyRep()
# Launch the application with a scene file that contains a robot
pr.launch('scene_with_panda.ttt') 
pr.start()  # Start the simulation

arm = Panda()  # Get the panda from the scene
gripper = PandaGripper()  # Get the panda gripper from the scene

velocities = [.1, .2, .3, .4, .5, .6, .7]
arm.set_joint_target_velocities(velocities)
pr.step()  # Step physics simulation

done = False
# Open the gripper halfway at a velocity of 0.04.
while not done:
    done = gripper.actuate(0.5, velocity=0.04)
    pr.step()
    
pr.start()  # Stop the simulation
pr.shutdown()  # Close the application
```

We recommend constructing your robot in a dictionary or a small structure, e.g.


```python
class MyRobot(object):
  def __init__(self, arm, gripper):
    self.arm = arm
    self.gripper = gripper

arm = Panda()  # Get the panda from the scene
gripper = PandaGripper()  # Get the panda gripper from the scene

# Create robot structure
my_robot_1 = MyRobot(arm, gripper)
# OR
my_robot_2 = {
  'arm': arm,
  'gripper': gripper
}
```

## Supported Robots

Here is a list of robots currently supported by PyRep:

#### Arms

- Kinova Mico
- Kinova Jaco
- Rethink Baxter
- Rethink Sawyer
- Franka Emika Panda
- Kuka LBR iiwa 7 R800
- Kuka LBR iiwa 14 R820
- Universal Robots UR3
- Universal Robots UR5
- Universal Robots UR10

#### Grippers

- Kinova Mico Hand
- Kinova Jaco Hand
- Rethink Baxter Gripper
- Franka Emika Panda Gripper

Feel free to send pull requests for new robots!

## Planned Future Updates

- Support for mobile bases (including planning)
- Support for MuJoCo
- Sim-to-Real support (e.g. domain randomization)

## Contributing

We want to make PyRep the best tool for rapid robot learning research. If you would like to get involved, then please [get in contact](https://www.doc.ic.ac.uk/~slj12/)!

Pull requests welcome for bug fixes!