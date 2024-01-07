# ROBÓTICA MÓVIL

# Indice
* [Indice][ind]
* [Welcome][wel]
* [Basic Vacuum Cleaner][p1]
* [Follow line][p2]
* [Obstacle Avoidance][p3]
* [Global Navigation][p4]
* [Monte Carlo Laser Localization][p5]


[ind]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#indice
[wel]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#welcome
[p1]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#basic-vacuum-cleaner
[p2]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#follow-line
[p3]: https://github.com/acruzr2021/robotica_movil/tree/main#obstacle-avoidance
[p4]: https://github.com/acruzr2021/robotica_movil/tree/main#global-navigation
[p5]: https://github.com/acruzr2021/robotica_movil/blob/main/README.md#monte-carlo-laser-localization


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


---


# Follow line


## Meta

El objetivo de esta segunda práctica es conseguir que un coche de carreras pueda ser controlado, con el uso de uno o varios controladores de la familia de los PIDs, para seguir una línea roja y completar el circuito en el menor tiempo posile.


## Proceso 

### Filtrado de imagen

Para empezar, necesitábamos filtrar la imagen para detectar la línea roja y pasarla a binario. Para ello, hicimos uso de las librerías de OpenCV *cv2* e hicimos un filtro de color, en mi caso, decidí usar el formato rgb. Elegí el tono más alto y más bajo para obtener un rango de colores que me permitiera percibir la línea sin que me detectara cualquier otro elemento de la imagen y convierte la imagen a binario. Una vez he creado la máscara de color, he decidido recortar la imagen por varios motivos: en la imagen aparecía el cielo el cual, en este caso, no nos ofrece ninguna información necesaria y por la parte de abajo nos da una información algo redundante. Además, a la hora de usar el PID, me he dado cuenta de que, con la imagen más cerca, el tiene un mejor comportamiento y es más fácil de manejar. 

Luego, he buscado los contornos de la línea para poder buscar el mayor contorno (mediante su área) y buscar el momento, que nos proporcionará el centro de la línea. Una vez tenemos esto, en forma de señalización visual, he puesto un círculo en la ubicación del centroide de la línea. Esta será la imagen que mostraremos por pantalla:


![image](https://github.com/acruzr2021/robotica_movil/assets/92941137/40609d27-6775-444e-a358-a66e173268a1)

### Controlador PID

Para controlar la velocidad angular, hemos implementado dos PIDs diferentes: uno en caso de detectar una curva cerrada y otro en caso de que el radio de la curva no sea muy cerrado o sea una recta. La distinción de casos nos permite reducir el tiempo en el que el coche da la vuelta ya que puedes colocar dos velocidades (o tramos de velocidades) diferentes con sus respectivos, cosa que, con un solo PID y una velocidad constante, nos vemos más limitados a la hora de poder diseñar un controlador preciso que ejecute buenos tiempos. 

Para la distinción de si es curva o es recta, hemos tenido en cuenta la diferencia entre el centro de la imagen (teniendo en cuenta que el coche está desplazado a un lado) y el centroide calculado en el filtrado de imagen. Si el absoluto de dicha diferencia es mayor a una constante de error, devolveremos que dicho tramo es un tramo curvo, en caso contrario, es un tramo recto. El error está en un rango entre [-1, 1], ya que nos facilitaba la elección de valores para el controlador. Estaba la posibilidad de que el coche se desorientara, perdiera la línea y no tuvieramos centroide, por lo que implementé que si perdía la línea, el error sería +- 1, donde el signo es dependiente del signo del error previo.

![Untitled Diagram drawio (1)](https://github.com/acruzr2021/robotica_movil/assets/92941137/032db622-77bc-4fcd-8175-65fdffef8489)

Una vez diferenciados los dos casos, he implementado dos controladores del tipo PID diferentes en cada caso. Para el cálculo de la salida de la velocidad angular, he hecho uso de la siguiente función:

$W = Kp * err_x + Kd * (err_x - prev_error) + Ki * sum_error/i$

Por otro lado, para la salida de la velocidad lineal diferenciamos también por los dos casos antariores:

  - Si es una recta, tendremos una velocidad constante de 9 u.
  - Si es una curva, la velocidad  variará en un rango de [0,3] u, dependiendo de esta fórmula: $V = Vmax/(abs(W) + 1)$.

## Comentarios

Durante el transcurso de la práctica he probado diferentes métodos para encontrar la mejor solución posible.

Intenté diferenciar las curvas de las rectas mediante la diferencia del área del mayor contorno menos la del menor, pero era un problema ya que la imagen está en perspectiva, la única forma eficaz que encontré fue comprobando el error entre el centro de la imagen y el centroide de la línea.

También ví que hay una mayor facilidad de manejo de valores si están en un rango definido. Además, hay mejora en el controlador centrando la cámara al mínimo imprescindible.

Un último apunte es que, en la solución final, en las curvas muy cerradas el coche sigue la línea pero a veces por fueera, ya que tuve que elegir entre hacer un programa que fuera muy exacto bastante lento o uno algo menos exacto más veloz.


## Resultado

Aquí dejo unos vídeos de la solución final:

Video en mapa Simple

[Screencast from 15-10-23 20:18:19.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/af0425a2-d9a1-443d-ba2f-5bae69ec3f87)

Video en mapa Montmelo

[Screencast from 15-10-23 20:35:05.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/b7d28fa2-76c7-49cb-b4fa-fdcd57a41ed9)

Video en mapa Nürburgring

[Screencast from 15-10-23 20:45:37.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/263d1885-7b11-427b-934e-bea88379aea4)


---


# Obstacle Avoidance

## Objetivo

El objetivo de esta práctica es conseguir que un coche de carreras esquive los obstáculos del circuito y pase por los puntos target mediante vectores de fuerzas atractivas y repulsivas en el menor tiempo posible.

## Proceso

### Algoritmo VFF

Para esta práctica nos vamos a basar en el Campo de Potencial (VFF). Este es un algoritmo de navegación local que nos permite la planificación del camino en entornos que el robot no conoce. Se basa en el concepto de un campo de fuerza donde actuan dos fuerzas: una atractiva y otra repulsiva, dando lugar a una tercera como relación de las dos anteriores. La fuerza atractiva estará relacionada en este caso con los puntos target que el robot quiere alcanzar, y la repulsiva, los obstáculos y paredes que el robot debe evitar. El vector resultante sigue esta fórmula:

$Fresultante = α * Fatractiva +  β * Frepulsiva$

El conjunto de fuerzas se pueden representar así:

![fuerzas](https://github.com/acruzr2021/robotica_movil/assets/92941137/a085680d-cc3f-45b2-a8cd-1d13d6f7c33f)


### Programación

Una vez tenemos a nivel teórico el algoritmo, debemos plasmarlo en código. El robot, en este caso, solo sensa el circuito mediante el laser. Trabajamos con dos sistemas de coordenadas: el absoluto (el sistemas de coordenadas del circuito) y el relativo o local (sistema de coordenadas del robot). Para ello usaremos distintas funciones que nos han sido proporcionadas:

  - absolute2relative: función que convierte unas coordenadas del mapa global al las coordenadas relativas al robot.

```python
def absolute2relative (x_abs, y_abs, robotx, roboty, robott):

  # robotx, roboty are the absolute coordinates of the robot
  # robott is its absolute orientation
  # Convert to relatives
  dx = x_abs - robotx
  dy = y_abs - roboty

  # Rotate with current angle
  x_rel = dx * math.cos (-robott) - dy * math.sin (-robott)
  y_rel = dx * math.sin (-robott) + dy * math.cos (-robott)

  return x_rel, y_rel
```

  - parse_laser_data: función para el uso del laser. Capa la detección del laser a 10 unidades (evitando los valores infinitos) y devuelve los datos sensados.

```python
def parse_laser_data (laser_data):
  laser = []
  i = 0
  if len(laser_data.values) == 0:
    return 0
  while (i < len(laser_data.values)):
    dist = laser_data.values[i]
    if dist > 10:
      dist = 10
    angle = math.radians(i-90) # because the front of the robot is -90 degrees
    laser += [(dist, angle)]
    i+=1
  return laser
```

 - *laser_vector*: convierte las medidas del laser a vector.

```python
def laser_vector(laser):
    laser_vectorized = []
    for d,a in laser:
        # (4.2.1) laser into GUI reference system
        x = np.exp(-d) * math.cos(a) * -1
        y = np.exp(-d) * math.sin(a) * -1
        v = (x,y)
        laser_vectorized += [v]

    laser_mean = np.mean(laser_vectorized, axis=0)
    return laser_mean
```

Para mayor efectividad, la función *laser_vector()* la modifiqué haciendo que en vez de multiplicar por la distancia, se multiplicara por la exponencial de la distancia, ya que mejoraba mucho a la hora de que el vector se viera incrementado cuando estaba cerca del obstáculo. Recalcar que esta función solo se usa para sacar el vector repulsivo.

En la función *run()* encapsulamos todo el código correspondiente al cálculo de las velocidades para llegar a los objetivos o targets propuestos. Al tener como vector atractivo un vector cuyo origen es el robot y destino el target, obtenemos unos números muy grandes, por lo cual fue necesario normalizar la coordenada x a 4 unidades y la coordenada y multiplicarla por Alpha. El valor Alpha (constante por la que se multiplcia el vector atractivo) es un valor muy pequeño respecto al valor Beta del repulsivo, ya que el vector correspondiente a los obstáculos es bastante pequeño. 

Alguno de los targets coincidian con la posición de obstáculos, por lo que, para que no se chocara, puse de condición que a 1 metro y medio del coche el target se diera por visitado o alcanzado.

Por otro lado, para completar el circuito en el menor tiempo posible con un correcto funcionamiento del robot, hay dos posibles velocidades lineales dependiendo de si hay un obstáculo o no (puede ser confuso ya que en el código está puesto dependiente del avgForce, pero la coordenada x crece principalmente si hay un obstáculo cerca). La velocidad angular, en cambio, depende del eje y del vector resultante

Por último, para que el vector resultante saliera con el eje x positivo, tuve que hacer que dicho eje tuviera el valor de la suma en valor absoluto de las fuerzas, ya que los pesos del vector repulsivo eran muy altos y con dirección negativa.

```python
avgForce = (abs(carForce[0] * ALPHA + obsForce[0] * BETA)), (carForce[1] * ALPHA + obsForce[1] * BETA)
```

## Resultado

Aquí dejo unos vídeos del comportamiento fi#nal. Quiero resaltar que la ejecución es bastante más rápida si el procesador está holgado. En estas ejecuciones se suma un retardo considerable al estar grabando la pantalla. El tiempo medio del código sin estrés es de unos 2 minutos 20 segundos en mi ordenador.

[Screencast from 06-11-23 18:45:18.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/d39eed23-736e-4093-8f89-d119298605b7)

Este es el mismo código solo que en vez de multiplicar la constante Alpha al hacer la suma, está multiplicado en el vector verde, por eso sale tan pequeño.

[Screencast from 05-11-23 17:07:08.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/4f0ba4ab-6f10-4c51-a627-193acf6e2676)


---

# Global Navigation

## Meta

En esta cuarta práctica tenemos como objetivo conseguir que un taxi llegue al target marcado en el menor tiempo posible con la ruta más óptima.

## Gradient Path Planning

Gradient Path Planning (Planificación de Trayectorias por Gradiente) es un algoritmo de navegación global utilizado en robótica y sistemas autónomos para planificar trayectorias eficientes y seguras en entornos complejos. El campo de gradiente indica la dirección y magnitud de la pendiente en cada ubicación del entorno.

Un robot puede utilizar un mapa de gradientes para entender la topografía del entorno, identificando áreas de mayor o menor costo para la navegación, donde el coste representa la dificultad que representa atravesar ese área. Durante la planificación de trayectorias, el robot busca encontrar una ruta que minimice el costo total de la trayectoria desde el punto de inicio hasta el destino.

El descenso por gradiente se utiliza para encontrar el mínimo local de una función, permitiendo encontrar la ruta donde el coste disminuye más rápidamente.

Esta técnica es especialmente útil en situaciones donde se requiere adaptabilidad a cambios en el entorno y la optimización de rutas.

![gradient](https://github.com/acruzr2021/robotica_movil/assets/92941137/9d015acb-ff50-4e26-948c-f042766aa774)

## Programación

Para abordar esta práctica, primero saqué el gradiente a partir del pseudocódigo proporcionado. Creo una nueva matriz inicializada a 0 del mismo tamaño que la matriz que contiene al mapa, donde vamos a guardar el gradiente y creamos una cola de prioridad añadiendo la posición del target con coste cero. Luego vamos iterando mientras que exista un elemento en la cola. Extraemos el elemento y comprobamos:

  - Si es igual al inicio, cortamos la ejecución.
  - Si el dato del mapa en esas coordenadas es 0, añadimos a la lista de obstáculos y pasamos a la siguuiente.
  - Si esa coordenada ya ha sido visitada, pasamos a la siguiente.

Añadimos el target a la lista de visitados y calculamos sus celdas vecinas. Por cada celda vecina revisamos si ha sido visitada, en cuyo caso, seguimos con la siguiente; en el caso contrario, miramos si es un obstáculo: si lo es, lo descartamos y si no calculamos el peso de la casilla vecina, el cual será el coste de la actual + 1 si está en una casilla horizontal o vertical respecto a la actual y 1.2 si está en la diagonal. Añadimos a la cola de prioridad el coste y el valor y en el gradiente, en la coordenada de la vecina, añadimos el valor 255-coste, ya que queremos las zonas de menor coste se vean más claras hasta llegar al blanco.

El gradient path planning se ve algo como esto:
![image](https://github.com/acruzr2021/robotica_movil/assets/92941137/6ddbf62e-f035-45c3-b14a-8207d97312be)

Después, para una mayor eficacia, recorro los obstáculos y a las casillas de alrededor que no sean obstáculos, les añado un coste adicional de 50 y a las contiguas a estas de 20. El tiempo de ejecución se incrementa considerablemente pero a cambio obtenemos mayor fiabilidad, asegurando así una distancia de seguridad entre el coche y los obstáculos.

![image](https://github.com/acruzr2021/robotica_movil/assets/92941137/51e8e9b1-8928-44ab-865d-ef36caaa4d56)

Luego, a la hora de navegar itero iniciando por la posición del coche hasta que el target esté a menos de 7 u. de distancia. Luego busco el vecino con menor cosye en un area de 
11x11 y paso las coordenadas de ese vecino a las del simulador, el cual será el subobjetivo que perseguiremos. Iteramos hasta que ese punto esté a menos de 3 posiciones de distancia y sacamos las posiciones relativas y absolutas del coche en el simulador. Luego, calculamos el vector que nos hará como fuerza atractiva. Esta secuencia se repetirá en bucle hasta alcanzar el objetivo.

## Dificultades

El principal problema que he tenido a la hora de programar esta práctica es el cambio de ejes que existe entre las coordenadas del coche y las del mapa, confunde mucho a la hora de probar el código. El cambio del mapa al simulador fue fácil gracias a las funciones que nos fueron proporcionadas.
Otra dificultad fueron las esquinas al navegar, ya que el coche es propenso a chocarse. La solución fue ajustar el área de casillas que busca a la hora de moverse a otro punto y la distancia a la que se daba ese mini objetivo como alcanzado.

## Resultado

Cálculo de gradiente:

[Screencast from 05-12-23 13:15:15.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/e8ebf667-c421-4b60-9861-56c1622730ce)

Navegación:

[Screencast from 05-12-23 13:30:13.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/1981b59d-f07a-4cb3-a6e2-a5f4aafdd1bd)

Otro ejemplo de navegación:

[Screencast from 05-12-23 13:43:57.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/44f88629-8a41-45a9-9969-02966ca91512)


---

# Monte Carlo Laser Localization

## Meta

En esta última práctica trata autolocalizarse a través de un algorimo de localización probabilística muestreada. 

## Localización probabilística muestreada

Este método se basa en la teoría de probabilidades y utiliza técnicas de muestreo para calcular la posición más probable.

El robot inicialmente se encuentra en un entorno desconocido, desconociendo su posición. El robot disponde tan solo de un laser como sensor. Lanzamos múltiples partículas por el mapa como muestras para determinar la posición del robot, se les asigna una determinada probabilidad, reflejando la posibilidad que tiene de estar en la misma posición que la máquina. A medida que el robot se mueve, las partículas deben moverse en la misma dirección y sentido. Compara los datos del laser y los de las partículas, dando más peso a las que más se parecen y luego, un determinado número de partículas, elegido al azar pero con mayor probabilidad a menor peso, se convergen con el resto de partículas.

Si los pesos están bien seleccionado y hay un número suficiente de partículas, se forma una nube de particulas alrededor del robot

## Programación

En esta última práctica no vamos a usar el simulador usado anteriormente, esta vez tenemos dos escenarios: el mapa donde vemos el movimiento del robot y de las partículas (una imagen de 400x400) y el mundo. Las coordenadas cambian dependiendo del escenario, por lo que deberemos operar con las funciones proporcionadas.

Inicializamos las partículas, teniendo en cuenta que no deben de estar en los píxeles negros o paredes. Como no tenemos simulador, deberemos configurarlo a mano, haciendo que se generen hasta que no estén en esas zonas. 

Luego, hago moverse al robot dando círculos, y hago que las partículas le sigan. Recojo los datos captados por el laser del robot y los láseres virtuales de las partículas, filtro a 1 rayo de laser por cada 10, es decir, de los 180 solo leo 18. Con esos datos filtrados, comparo los láseres de cada partícula con el del robot calculando el módulo de los rayos y la media. A menor es la media, más fiable es la partícula, por lo cual le doy mayor peso. Como son muchas partículas, he optado por usar multiprocessing, pasándole al callback el laser de la partícula, el laser del robot y la imagen del mapa. Este hilo calcula la distancia y el peso.

Luego, aleatoriamente se seleccionan algunas de las partículas de menor peso que convergen con las restantes.

Despues de unas cuantas iteraciones tenemos este resultado:

![image](https://github.com/acruzr2021/robotica_movil/assets/92941137/d4f37fcb-3490-4ec8-9dff-9794efc1185d)

# Dificultades

Traté de hacer que el robot pudiera moverse por la casa pero tenía el inconveniente de que no a veces el programa iba demasiado justo de recursos y atravesaba las paredes, haciendo que las partículas pedieran al robot. Una vez solucionado esto, me di cuenta que cuanto más se acerca el robot a la pared, las particulas se van perdiendo. Otra dificultad es que, al variar el estado en el que está moviéndose, las partículas no reaccionan bien y van quedando atrás.

## Ejecución

Para poder ejecutar el programa, descargamos el *.zip* y descomprimimos. Encontraremos la imagen del mapa, las librerías y el archivo *main.py*. Lo ejecutamos de la siguiente forma:

```
python3 main.py
```

Una vez aparezca el mapa, tendremos que presionar unas dos veces el teclado para que inicie la autolocalización.

## Resultado

Este es el vídeo de la localización base, con un movimiento circular uniforme:

[Screencast from 07-01-24 12:45:57.webm](https://github.com/acruzr2021/robotica_movil/assets/92941137/e28cb113-8ca5-4198-a8db-be59c2ed376f)

Como está grabando, pierde potencia, pero las partículas generalmente encuentran más rapidamente al robot.

