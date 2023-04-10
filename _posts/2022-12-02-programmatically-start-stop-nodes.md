---
title: "Programmatically Start/Stop ROS or ROS2 Nodes"
date: 2022-12-02T12:00:00-05:00
excerpt_separator: "<!--more-->"
categories:
  - Robotics Notes
  - Gists
tags:
  - Python
  - ROS
  - ROS 2
---

As part of the effort to bring self-adaptation to ROS and ROS2, we need a programmatic way to handle the launching and shutting-down of nodes. Although this feature is embedded in ROS2 as the "Lifecycle" feature, many existing nodes do not include that as a standard. Here, the author provide a prototye Python script that act as a "guard" of other launched nodes. The script has a ROS version and a ROS2 version.

<!--more-->

### ROS2 version

This has been tested to work with ROS2 Galactic and later. It uses the `launch_ros` API which the `ros2 launch` verb calls.

```python
# ROS2 launch python api test
import multiprocessing
import launch_ros
from launch import LaunchService
from launch import LaunchDescription
from launch import LaunchIntrospector
import time
import nest_asyncio
import os
import signal

# apply nest_asyncio because we are nesting parallel processes
nest_asyncio.apply()

# Here is an example of three cyclic talkers/listeners
num_parallel = 3
# whatever launch description for each node is put in here.
ld = [LaunchDescription([
    launch_ros.actions.Node(
        package='demo_nodes_cpp',
        executable='talker',
        output='screen',
        remappings=[('chatter', 'my_chatter'+str(i % num_parallel))]),
    launch_ros.actions.Node(
        package='demo_nodes_cpp',
        executable='listener',
        output='screen',
        remappings=[('chatter', 'my_chatter'+str((i+1) % num_parallel))]),
])
    for i in range(num_parallel)]

for item in ld:
    print(LaunchIntrospector().format_launch_description(item))

# Construct launch service pool. Each launch service can handle one launch description
# add noninteractive=True to handle sigint
lsPool = [LaunchService(noninteractive=True) for i in ld]
i = 0
for ls in lsPool:
    ls.include_launch_description(ld[i])
    i += 1

# Construct process pool. Each process starts one launch service
pPool = [multiprocessing.Process(target=ls.run) for ls in lsPool]

# starting the stack
print("Starting everything")
for p in pPool:
    p.start()
time.sleep(10)
# shutting down the stack
print("Stopping everything")
for p in pPool:
    print("stopping: "+str(p.pid))
    # use SIGINT instead of SIGTERM to stop child processes ahead of launch service
    os.kill(p.pid, signal.SIGINT)
    # DON'T DO THIS as it will lead to a dirty shutdown with zombie nodes
    # p.terminate()
for p in pPool:
    p.join()

time.sleep(3)
# re-starting the stack
# Launch service pool and process pool are non-reusable
lsPool = [LaunchService(noninteractive=True) for i in ld]
i = 0
for ls in lsPool:
    ls.include_launch_description(ld[i])
    i += 1
pPool = [multiprocessing.Process(target=ls.run) for ls in lsPool]

print("Re-Starting everything")
for p in pPool:
    p.start()
time.sleep(5)
print("Stopping everything")
for p in pPool:
    print("stopping: "+str(p.pid))
    # use SIGINT instead of SIGTERM to stop child processes ahead of launch service
    os.kill(p.pid, signal.SIGINT)
for p in pPool:
    p.join()
```

<details>
  <summary>Expected outputs</summary>

  ```
  <launch.launch_description.LaunchDescription object at 0x7f8f8cf4bd90>
  ├── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='talker'), '--ros-args'], cwd=None, env=None, shell=False)
  └── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='listener'), '--ros-args'], cwd=None, env=None, shell=False)
  <launch.launch_description.LaunchDescription object at 0x7f8f8d6b4970>
  ├── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='talker'), '--ros-args'], cwd=None, env=None, shell=False)
  └── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='listener'), '--ros-args'], cwd=None, env=None, shell=False)
  <launch.launch_description.LaunchDescription object at 0x7f8f88431ed0>
  ├── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='talker'), '--ros-args'], cwd=None, env=None, shell=False)
  └── ExecuteProcess(cmd=[ExecInPkg(pkg='demo_nodes_cpp', exec='listener'), '--ros-args'], cwd=None, env=None, shell=False)
  Starting everything
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [talker-1]: process started with pid [65087]
  [INFO] [listener-2]: process started with pid [65089]
  [INFO] [talker-1]: process started with pid [65091]
  [INFO] [listener-2]: process started with pid [65093]
  [INFO] [talker-1]: process started with pid [65101]
  [INFO] [listener-2]: process started with pid [65105]
  [talker-1] [INFO] [1681090285.038044195] [talker]: Publishing: 'Hello World: 1'
  [listener-2] [INFO] [1681090285.038464320] [listener]: I heard: [Hello World: 1]
  [listener-2] [INFO] [1681090285.039563924] [listener]: I heard: [Hello World: 1]
  [talker-1] [INFO] [1681090285.039156352] [talker]: Publishing: 'Hello World: 1'
  [talker-1] [INFO] [1681090285.042381216] [talker]: Publishing: 'Hello World: 1'
  [listener-2] [INFO] [1681090285.042884418] [listener]: I heard: [Hello World: 1]
  [talker-1] [INFO] [1681090286.038069258] [talker]: Publishing: 'Hello World: 2'
  [listener-2] [INFO] [1681090286.038509842] [listener]: I heard: [Hello World: 2]
  [listener-2] [INFO] [1681090286.039599521] [listener]: I heard: [Hello World: 2]
  [listener-2] [INFO] [1681090286.042839326] [listener]: I heard: [Hello World: 2]
  [talker-1] [INFO] [1681090286.039185127] [talker]: Publishing: 'Hello World: 2'
  [talker-1] [INFO] [1681090286.042425170] [talker]: Publishing: 'Hello World: 2'
  [listener-2] [INFO] [1681090287.038159305] [listener]: I heard: [Hello World: 3]
  [talker-1] [INFO] [1681090287.037952582] [talker]: Publishing: 'Hello World: 3'
  [listener-2] [INFO] [1681090287.039317500] [listener]: I heard: [Hello World: 3]
  [talker-1] [INFO] [1681090287.039130653] [talker]: Publishing: 'Hello World: 3'
  [listener-2] [INFO] [1681090287.042553077] [listener]: I heard: [Hello World: 3]
  [talker-1] [INFO] [1681090287.042357801] [talker]: Publishing: 'Hello World: 3'
  [talker-1] [INFO] [1681090288.038071460] [talker]: Publishing: 'Hello World: 4'
  [listener-2] [INFO] [1681090288.038564743] [listener]: I heard: [Hello World: 4]
  [listener-2] [INFO] [1681090288.039561572] [listener]: I heard: [Hello World: 4]
  [listener-2] [INFO] [1681090288.042849625] [listener]: I heard: [Hello World: 4]
  [talker-1] [INFO] [1681090288.042442624] [talker]: Publishing: 'Hello World: 4'
  [talker-1] [INFO] [1681090288.039169335] [talker]: Publishing: 'Hello World: 4'
  ...
  [talker-1] [INFO] [1681090292.037881635] [talker]: Publishing: 'Hello World: 8'
  [listener-2] [INFO] [1681090292.038060630] [listener]: I heard: [Hello World: 8]
  [listener-2] [INFO] [1681090292.039187895] [listener]: I heard: [Hello World: 8]
  [talker-1] [INFO] [1681090292.039028193] [talker]: Publishing: 'Hello World: 8'
  [talker-1] [INFO] [1681090292.042374958] [talker]: Publishing: 'Hello World: 8'
  [listener-2] [INFO] [1681090292.042685016] [listener]: I heard: [Hello World: 8]
  [listener-2] [INFO] [1681090293.038100442] [listener]: I heard: [Hello World: 9]
  [talker-1] [INFO] [1681090293.037897712] [talker]: Publishing: 'Hello World: 9'
  [listener-2] [INFO] [1681090293.039181348] [listener]: I heard: [Hello World: 9]
  [talker-1] [INFO] [1681090293.039005587] [talker]: Publishing: 'Hello World: 9'
  [talker-1] [INFO] [1681090293.042451099] [talker]: Publishing: 'Hello World: 9'
  [listener-2] [INFO] [1681090293.042669538] [listener]: I heard: [Hello World: 9]
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [listener-2] [INFO] [1681090294.028972415] [rclcpp]: signal_handler(signum=2)
  [listener-2] [INFO] [1681090294.027723267] [rclcpp]: signal_handler(signum=2)
  [listener-2] [INFO] [1681090294.031303766] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090294.034787205] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090294.035657759] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090294.038026950] [rclcpp]: signal_handler(signum=2)
  [INFO] [listener-2]: process has finished cleanly [pid 65089]
  [INFO] [talker-1]: process has finished cleanly [pid 65091]
  [INFO] [talker-1]: process has finished cleanly [pid 65101]
  [INFO] [listener-2]: process has finished cleanly [pid 65093]
  [INFO] [talker-1]: process has finished cleanly [pid 65087]
  [INFO] [listener-2]: process has finished cleanly [pid 65105]
  Stopping everything
  stopping: 65072
  stopping: 65073
  stopping: 65078
  Re-Starting everything
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [launch]: All log files can be found below /home/***/.ros/log/***
  [INFO] [launch]: Default logging verbosity is set to INFO
  [INFO] [talker-1]: process started with pid [65526]
  [INFO] [talker-1]: process started with pid [65525]
  [INFO] [listener-2]: process started with pid [65530]
  [INFO] [listener-2]: process started with pid [65529]
  [INFO] [talker-1]: process started with pid [65537]
  [INFO] [listener-2]: process started with pid [65543]
  [talker-1] [INFO] [1681090298.209056737] [talker]: Publishing: 'Hello World: 1'
  [listener-2] [INFO] [1681090298.209319158] [listener]: I heard: [Hello World: 1]
  [talker-1] [INFO] [1681090298.210271025] [talker]: Publishing: 'Hello World: 1'
  [listener-2] [INFO] [1681090298.210691514] [listener]: I heard: [Hello World: 1]
  [listener-2] [INFO] [1681090298.211921745] [listener]: I heard: [Hello World: 1]
  [talker-1] [INFO] [1681090298.211579789] [talker]: Publishing: 'Hello World: 1'
  [listener-2] [INFO] [1681090299.209601209] [listener]: I heard: [Hello World: 2]
  [talker-1] [INFO] [1681090299.209132713] [talker]: Publishing: 'Hello World: 2'
  [listener-2] [INFO] [1681090299.210804944] [listener]: I heard: [Hello World: 2]
  [listener-2] [INFO] [1681090299.212024214] [listener]: I heard: [Hello World: 2]
  [talker-1] [INFO] [1681090299.210378899] [talker]: Publishing: 'Hello World: 2'
  [talker-1] [INFO] [1681090299.211633594] [talker]: Publishing: 'Hello World: 2'
  [talker-1] [INFO] [1681090300.209125253] [talker]: Publishing: 'Hello World: 3'
  [listener-2] [INFO] [1681090300.209577234] [listener]: I heard: [Hello World: 3]
  [listener-2] [INFO] [1681090300.210662442] [listener]: I heard: [Hello World: 3]
  [listener-2] [INFO] [1681090300.211902773] [listener]: I heard: [Hello World: 3]
  [talker-1] [INFO] [1681090300.210295558] [talker]: Publishing: 'Hello World: 3'
  [talker-1] [INFO] [1681090300.211569335] [talker]: Publishing: 'Hello World: 3'
  [talker-1] [INFO] [1681090301.209103442] [talker]: Publishing: 'Hello World: 4'
  [listener-2] [INFO] [1681090301.209548204] [listener]: I heard: [Hello World: 4]
  [listener-2] [INFO] [1681090301.210624575] [listener]: I heard: [Hello World: 4]
  [listener-2] [INFO] [1681090301.211884107] [listener]: I heard: [Hello World: 4]
  [talker-1] [INFO] [1681090301.210294245] [talker]: Publishing: 'Hello World: 4'
  [talker-1] [INFO] [1681090301.211570027] [talker]: Publishing: 'Hello World: 4'
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [WARNING] [launch]: user interrupted with ctrl-c (SIGINT)
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [listener-2]: sending signal 'SIGINT' to process[listener-2]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [INFO] [talker-1]: sending signal 'SIGINT' to process[talker-1]
  [listener-2] [INFO] [1681090302.189877906] [rclcpp]: signal_handler(signum=2)
  [listener-2] [INFO] [1681090302.189838610] [rclcpp]: signal_handler(signum=2)
  [listener-2] [INFO] [1681090302.189868872] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090302.198448969] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090302.200352365] [rclcpp]: signal_handler(signum=2)
  [talker-1] [INFO] [1681090302.197377289] [rclcpp]: signal_handler(signum=2)
  [INFO] [listener-2]: process has finished cleanly [pid 65543]
  [INFO] [talker-1]: process has finished cleanly [pid 65525]
  [INFO] [listener-2]: process has finished cleanly [pid 65530]
  [INFO] [talker-1]: process has finished cleanly [pid 65526]
  [INFO] [listener-2]: process has finished cleanly [pid 65529]
  [INFO] [talker-1]: process has finished cleanly [pid 65537]
  Stopping everything
  stopping: 65508
  stopping: 65509
  stopping: 65512
  ```
</details>

### ROS1 version

This version uses the `roslaunch` API.

```python
import roslaunch
import rospy

"""
Wrapper for launching threads and ROS components within Python
"""
class Launcher:
    def __init__(self, launcherName:str, auto_start=True):
        self.roscoreProc = None
        self.nodeLauncher = None
        self.name = launcherName
        self.uuid = roslaunch.rlutil.get_or_generate_uuid(None, False)
        roslaunch.configure_logging(self.uuid)
        self.roscoreProc = roslaunch.parent.ROSLaunchParent(
            self.uuid, roslaunch_files=[], is_core=True)
        self.nodeLauncher = roslaunch.parent.ROSLaunchParent(
            self.uuid, roslaunch_files=[], is_core=False)
        if auto_start:
            self.activate()

    def activate(self):
        # start roscore
        self.roscoreProc.start()
        self.roscoreProc.spin_once()
        # start interface to accept roslaunch files
        self.nodeLauncher.start(auto_terminate=False)
        self.spin_once()
        rospy.init_node(self.name)
        self.spin_once()
        rospy.sleep(0.05)

    def launch(self, pkgName:str='', fileName:str='', args:tuple=(), fullPathList:list=[]):
        if (pkgName and fileName):
            fp = roslaunch.rlutil.resolve_launch_arguments([pkgName, fileName, *args])
            if args:
                fp = [(fp[0], list(args))]
        else:
            fp = []
        for line in fullPathList:
            # fullPathList APPENDS description from pkgName/fileName.
            if len(line)>1:
                fp.append((roslaunch.rlutil.resolve_launch_arguments(line)[0], line[1:]))
            else:
                fp.append(roslaunch.rlutil.resolve_launch_arguments(line)[0])
        # wait until roscore is available to handle the state transition.
        roslaunch.rlutil.get_or_generate_uuid(None, True)
        cfg = roslaunch.config.load_config_default(fp, None, verbose=False)
        self.nodeLauncher.runner.config.params.update(cfg.params)
        # hack to update parameter server...
        self.nodeLauncher.runner._load_parameters()
        nodeProcs = []
        # only launches local nodes.
        local_nodes = [n for n in cfg.nodes if roslaunch.core.is_machine_local(n.machine)]
        for node in local_nodes:
            self.nodeLauncher.spin_once()
            self.roscoreProc.spin_once()
            proc, success = self.nodeLauncher.runner.launch_node(node)
            if success:
                nodeProcs.append(proc)
            self.nodeLauncher.spin_once()
            self.roscoreProc.spin_once()
        return tuple(nodeProcs)

    def stop(self, proc):
        for p in proc:
            p.shutdown()

    def status(self):
        # returns status of the launcher
        coreStat = Fstat.NULL
        nodeStat = Fstat.NULL
        if hasattr(self.roscoreProc, 'runner'):
            if not (self.roscoreProc.runner is None):
                if not self.roscoreProc.pm.is_shutdown:
                    coreStat = Fstat.RUNNING
                else:
                    coreStat = Fstat.OFF
        if hasattr(self.nodeLauncher, 'runner'):
            if not (self.nodeLauncher.runner is None):
                if not self.nodeLauncher.pm.is_shutdown:
                    nodeStat = Fstat.RUNNING
                else:
                    nodeStat = Fstat.OFF
        return {'rosCore': coreStat, 'nodeLauncher': nodeStat}

    def spin(self):
        rate = rospy.Rate(20.0)
        while (not self.roscoreProc.pm.is_shutdown) or (not self.nodeLauncher.pm.is_shutdown):
            self.roscoreProc.spin_once()
            self.nodeLauncher.spin_once()
            rate.sleep()
        rospy.signal_shutdown("ROS Launcher Exiting...")
    
    def spin_once(self):
        self.nodeLauncher.spin_once()
        self.roscoreProc.spin_once()
        
    def shutdown(self):
        self.nodeLauncher.shutdown()
        self.roscoreProc.shutdown()

if __name__ == "__main__":
    # a simple test case with two empty modules.
    launcher = Launcher("RosNodeRunner")
    launcher.spin()
    # Ctrl-C triggers exit
    launcher.shutdown()
```

