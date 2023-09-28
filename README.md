# ROBÓTICA MÓVIL

# Indice
* [Indice][ind]
* [Welcome][wel]
* [Basic Vacuum Cleaner][p1]


[ind]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#indice
[wel]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#welcome
[p1]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#basic-vacuum-cleaner


---

# Welcome

Greetings, esteemed readers, and a warm welcome to my blog. My name is Alba Cruz Rodríguez and this is my website for the mobile robotics subject. These space will be dedicated to my practices, their development, the differents ideas and these results until we find the solution.

Robotics is a discipline we seek to solve some problems to the humanity joining sensors, actuators and software on a machine. In this doctrine studies the systems that make a connection intelligent between the perceptual and acting system. We will focus on the "brain" of the machine (or the *software*), devising the most effective and efficient algorithm for each project.

Join me to discover the exciting world of robotics and the discipline of *Mobile Robotics*! 

---

# Basic Vacuum Cleaner

## Goal
On this first proyect we will program an algorithm for an autonomous vacuum cleaner that must owes the largest area of a house in the shrotest surface time possible. For these task we will program code based on states. The code will be on Python and we will use the web [Unibotics](https://unibotics.org/)

## Process

In first place, we will use a data collected by the robot laser as a method of security. For these task, I used a fuction called parse_laser_data which returns an dobble array where the firts column is the angle of the laser and the second the distance to the obstacle. 

We also have another fuction called laser_mean which return a middle distance of the obstacles that the robot has around it. At first I used this second option, but I ended up finding more precise the first option doing some changes.

To decide if the object is near the robot, I code an method called distance whose argument is the output of the function paser_laser_data. "distance" shortens the angle of interest and use an angle of about 60 degrees. If an obstcle is less of 0.5 meters of the robot in these range of angles, the method return 1, else 0.

![dibujo drawio](https://github.com/acruzr2021/robotica_movil/assets/92941137/c5c56404-e776-4d18-81df-e6f1d43f8433)


Later, I implemented four different states:

  - *Spiral*: (State 0) The code starts in this state. Consist of leaving a constant angular speed and increasing the linear speed, causing the spiral to increase in radius, covering more area. The  direction of rotation is random. If an obstacle is detected less than half a meter, the robot actuvate state 1 (back).
  - *Back*: (State 1) Consist on go back for a BackingTime. Has a constant linear negative speed and has not an angular speed. If the backingtime ends, the robot enters state 3 (turn). 
  - *Forward*: (State 2) Consist on go forward for a ForwardTime. Has a constant linear positive speed and has not an angular speed. If forwardtime ends,  the state 0 or spiral (in random direction) starts. If an obstable is detected near, the state 1 starts.
  - *Turn*: (State 3) Consist on turn for a TurningTime. Has a constant angular speed and has not a linear speed. If an obstacke is detected near, keeps in this state. If the turningtime ends, goes to the state 2 (forward).

Those states are representated in the follow diagram:

![diagramaEstados drawio](https://github.com/acruzr2021/robotica_movil/assets/92941137/0dd41046-b4f9-4b38-9a22-5c5131c25744)


I thought that could be a great idea to implement a bumper as an additional security method, but if the robot collided from behind, detected the right or left side since it does not have a rear sensor. If the robot collided on the right side (at the front) and it was implemented to go backwards, if it was then hit from behind, it could not get out of there and vice versa. Then, I decided to include to the code a timer to change the states.

Another discarded idea was to make the direction of the spin state random since many times it ended up spinning without leaving the state. Also I tried to make the spiral state random when the robot was in forward state, but I find more effective make the transition after a specific timer.

Another idea that i implemented but was also descarted was this relationship of states:

| Current Status | Previous Status   | Action        |
| :---:          |     :---:         |         :---: |
| near           | near              | turn          |
| near           | far               | back          |
| far            | near              | forward       |
| far            | far               | spiral        |

in this case we take into account the current state and a previous one before making the decision. This implementation has a lot of concatenated ifs and it was less effective.

The current code has had many differents versions
