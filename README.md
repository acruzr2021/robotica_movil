# ROBÓTICA MÓVIL

# Indice
* [Indice][ind]
* [Welcome][wel]
* [Basic Vacuum Cleaner][p1]
* [Follow line][p2]


[ind]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#indice
[wel]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#welcome
[p1]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#basic-vacuum-cleaner
[p2]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#follow-line


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

To decide if the object is near the robot, I code an method called distance whose argument is the output of the function paser_laser_data. "distance" shorters the angle of interest and use an angle of about 60 degrees. If an obstcle is less of 0.5 meters of the robot in these range of angles, the method return 1, else 0.

![dibujo drawio](https://github.com/acruzr2021/robotica_movil/assets/92941137/c5c56404-e776-4d18-81df-e6f1d43f8433)


Later, I implemented four different states:

  - *Spiral*: (State 0) The code starts in this state. Consist of decreasing the angular speed and increasing the linear speed, causing the spiral to increase in radius, covering more area. The  direction of rotation is random. If an obstacle is detected less than half a meter, the robot actuvate state 1 (back).
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

The current code has had many differents versions:
 - Leaving the spiral direction constant
 - Randomly changing the angular direction
 - Leaving constant the angular speed of the spiral
 - Counting iterations

All of these modifications had a similar behavior but less effective and efficient than leaving constant the turn's angular speed, changing the angular speed direction of spiral and randomly changing the angular speed.


## Result

And here we have two example videos of the final implementation.

[Screencast from 28-09-23 19:35:45.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/4d044ef1-0686-4d0b-8035-8e12313f7144)


[Screencast from 28-09-23 20:05:45.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/c562618e-c487-4828-bd7d-5309df03c891)


And here a photo of a 20 minute running:

![Screenshot from 2023-09-28 22-39-53](https://github.com/acruzr2021/robotica_movil/assets/92941137/8cbc06b7-9ba1-47b9-aaf9-4eced67f4456)



# Basic Vacuum Cleaner


## Meta

El objetivo de esta segunda práctica es conseguir que un coche de carreras pueda ser controlado, con el uso de uno o varios controladores de la familia de los PIDs, para seguir una línea roja y completar el circuito en el menor tiempo posile.


## Proceso 

Para empezar, necesitábamos filtrar la imagen para detectar la línea roja y pasarla a binario. Para ello, hicimos uso de las librerías de OpenCV *cv2* e hicimos un filtro de color, en mi caso, decidí usar el formato rgb. Elegí el tono más alto y más bajo para obtener un rango de colores que me permitiera percibir la línea sin que me detectara cualquier otro elemento de la imagen y convierte la imagen a binario. Una vez he creado la máscara de color, he decidido recortar la imagen por varios motivos: en la imagen aparecía el cielo el cual, en este caso, no nos ofrece ninguna información necesaria y por la parte de abajo nos da una información algo redundante. Además, a la hora de usar el PID, me he dado cuenta de que, con la imagen más cerca, el tiene un mejor comportamiento y es más fácil de manejar. 

Luego, he buscado los contornos de la línea para poder buscar el mayor contorno (mediante su área) y buscar el momento, que nos proporcionará el centro de la línea. Una vez tenemos esto, en forma de señalización visual, he puesto un círculo en la ubibicabbbb
