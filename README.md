# Udacity PathPlanning Project

### Goals
The goal of the project is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of 
the 50 MPH speed limit. The simulator provides the car's localization and sensor fusion data, there is also a sparse 
map list of waypoints around the highway. The car tries to go as close as possible to the 50 MPH speed limit,
 which means passing slower traffic when possible, note that other cars will try to change lanes too. 
 The car avoids hitting other cars at all cost as well as driving inside of the marked road lanes at all times, 
 unless going from one lane to another. The car will able to make one complete loop around the 6946m highway.
  Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. 
  Also the car does not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.
         
## Rubric Criteria

####The car is able to drive at least 4.32 miles without incident..
![4.38 miles](./data/milesDriven.png)

####The car drives according to the speed limit.
There were no speed limit violations

####Max Acceleration and Jerk are not Exceeded.
Acceleration was below 10 m/s^2 and jerk was below 10 m/s^3

####Car does not have collisions.
There were no collision events

####The car stays in its lane, except for the time between changing lanes.
The car tends to stay in the center lane. Lane change only happens if its blocked.

####The car is able to change lanes
The car changes lanes to maintain 50 mph speed

## Reflection

### Behaviour Planning Steps
The path planning program consists of the following steps:
1. Parse the sensor fusion list and determine the lanes used by the car around ego car.
2. A lane is considered occupied, if the candidate vehicle is 30 m behind or ahead of the ego car in frenet coordinates
3. Car behaviour: [line 154 to line 177](./src/main.cpp#L154)
    * If the center lane is not occupied move to center lane and stay there
    * If there is a car ahead try to move to the left lane if:
        * available and
        * lane is not occupied
    * If the above step doesn't work try to move to the right lane if:
        * available and
        * lane is not occupied
    * [If none of the alternatives work keep following the closest in path vehicle](./src/main.cpp#L166)
 
###Path Generation  ([line 180 to line 293](./src/main.cpp#L180))       
The path planning code is invoked every few tens of milliseconds. Along with car's position, it also returns the 
previous waypoints that are not used. The last two waypoints are used to generate the anchor points for the future
spline trajectory. In case the simulator consumes all the waypoints, before the next iteration of path planning, the 
car's position and yaw information are used to generate the spline root anchor points 
([line 191 to line 217](./src/main.cpp#L180)). The target anchor points for the spline is obtained from the target 
lane assigned by the [behaviour code](./src/main.cpp#L171). These anchor points are
used to create a spline trajectory. The car in the simulator takes the position given as a points list to it every 
0.02 s. The spline generated is interpolated so that the velocity, acceleration and jerk are minimized.   

# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
### Simulator.
You can download the Term3 Simulator which contains the Path Planning Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases/tag/T3_v1.2).  

To run the simulator on Mac/Linux, first make the binary file executable with the following command:
```shell
sudo chmod u+x {simulator_file_name}
```



#### The map of the highway is in data/highway_map.txt
Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes from 0 to 6945.554.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

Here is the data provided from the Simulator to the C++ Program

#### Main car's localization Data (No Noise)

["x"] The car's x position in map coordinates

["y"] The car's y position in map coordinates

["s"] The car's s position in frenet coordinates

["d"] The car's d position in frenet coordinates

["yaw"] The car's yaw angle in the map

["speed"] The car's speed in MPH

#### Previous path data given to the Planner

//Note: Return the previous list but with processed points removed, can be a nice tool to show how far along
the path has processed since last time. 

["previous_path_x"] The previous list of x points previously given to the simulator

["previous_path_y"] The previous list of y points previously given to the simulator

#### Previous path's end s and d values 

["end_path_s"] The previous list's last point's frenet s value

["end_path_d"] The previous list's last point's frenet d value

#### Sensor Fusion Data, a list of all other car's attributes on the same side of the road. (No Noise)

["sensor_fusion"] A 2d vector of cars and then that car's [car's unique ID, car's x position in map coordinates, car's y position in map coordinates, car's x velocity in m/s, car's y velocity in m/s, car's s position in frenet coordinates, car's d position in frenet coordinates. 

## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.

## Tips

A really helpful resource for doing this project and creating smooth trajectories was using http://kluge.in-chemnitz.de/opensource/spline/, the spline function is in a single hearder file is really easy to use.

---

## Dependencies

* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
