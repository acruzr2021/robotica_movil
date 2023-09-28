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

In first place, we will use a data collected by the robot laser as a method of security. For these task, I used a fuction called parse_laser_data which returns an dobble array where the firts column is the angle of the laser and the second the distance to the obstacle. We also have another fuction called laser_mean which return a middle distance of the obstacles that the robot has around it. At first I used this second option, but I ended up finding more precise the first option doing some changes.
To decide if the object is near the robot, I code an method called distance whose argument is the output of the function paser_laser_data. "distance" shortens the angle of interest and use an angle of about 60 degrees. If an obstcle is less of 0.5 meters of the robot in these range of angles, the method return 1, else 0.

![WhatsApp Image 2023-09-27 at 19 20 28](https://github.com/acruzr2021/robotica_movil/assets/92941137/dec115cc-4a9d-4e3b-81dd-2728529df47a)

Later, I implemented four different states:

  - *Spiral*: (State 0) The code starts in this state. Consist of leaving a constant angular speed and increasing the linear speed, causing the spiral to increase in radius, covering more area. The  direction of rotation is random.
  - *Back*: (State 1) Consist on go back for a BackingTime. Has a constant linear negative speed and has not an angular speed.
  - *Forward*: (State 2) Consist on go forward for a ForwardTime. Has a constant linear positive speed and has not an angular speed.
  - *Turn*: (State 3) Consist on turn for a TurningTime. Has a constant angular speed and has not a linear speed.

Those states are representated in the follow diagram:
