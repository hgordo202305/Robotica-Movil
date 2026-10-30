# Práctica 3 - Local navigation with VFF
Esta práctica consiste en desarrollar un coche de F1 que es capaz de desplazarse de manera autónoma por un circuito, evitando otros coches que actúan como obstáculos y alcanzando una seri de puntos, para ello hemos 
implementado un algoritmo de navegación local basado en el campo de fuerzas virtuales (VFF) combinando una fuerza de atracción hacia el objetivo con una fuerza de respulsión generada a partir de las lecturas del sensor laser.

## Funcionamiento General
### Detección de obstaculos (procesado de láser)
El robot obtiene la información del entorno mediante el uso del laser que nos proporciona hasta 180 medidas de distacia que
corresponden a diferentes direcciones alrededor de la parte frontal del vehículo.

Para poder hacer uso de esas medidas convertimos los índices del láser en ángulos respecto al frente del coche, de modo que
cada ángulo corresponde al frente, izquierda o derecha del coche.

Hemos podido hacer esto usando el código proporcionado en la ***user guide*** de unibotics de esta práctica.
```python
for i in range(min(180, len(laser_data.values))):
    dist = laser_data.values[i]
    angle = math.radians(i - 90)

    laser_polar.append((dist, angle))

    x = dist * math.cos(angle)
    y = dist * math.sin(angle)
    laser_xy.append((x, y))

```
Una vez hemos procesado las lectras podemos descartar los valores no válidos y las distancias superiores a un rango 
establecido (unos 3.5 metros). De esta manera el algoritmo se centra en los obstáculos que pueden afectar a la trayectoria del 
movimiento.

### Cálculo del vector de atracción 
El vector de atracción indica la dirección del siguiente punto objetivo, para poder calcularlo, se obtiene la diferencia
entre la posición absoluta del objetivo y la posición del coche.

Después se transforma este vector al sistema de coordenadas relativo del robot usando una matriz de rotación y usando la
dirección actual del vehiculo. Así podemos conseguir que el vector apunte siempre hasta el objetivo, independientemente de la orientación
del coche.

Hemos usado una constante preestablecida para poder evitar que la distancia al objetivo afecte de manera excesiva a la 
magnitud de la fuerza esto sumado a la normalización del vector.

Al final cuando nos encontramos a menos de un metro del objetivo este se va a marcar como alcanzaddo y se solicita el siguiente.

### Cálculo del vector de repulsión
Para poder evitar las colisiones cada una de las lecturas del láser genera una fuerza de repulsión en sentido contrario al dato detectado.
La magnitud de esta fuerza es inversamente proporcional al cuadrado de la distancia al obstáculo, de esta manera 
los objetps cercanos tienen mayor influencia sobre la dirección del coche.

Además hemos incorporado una ponderación la cual depende del ángulo de detección para reducir la influencia de los obstáculos
situados en los laterales:
* Para obstáculos situados a más de 60 grados respecto al frente, se aplica un peso de 0,25.
* Para obstáculos situados entre 35 y 60 grados, se aplica un peso de 0,5.
* Para obstáculos situados dentro de los 35 grados frontales, se mantiene el peso completo.
  
Acumulamos las fuerzas detectadas a la derecha y a la izquierda para poder determinar que lado tiene más riesgo, si hay muchos obstáculos
a la derecha favorecemos un giro a la izquierda, si predominan a la izquierda se favorece  un giro a la derecha.

### Cálculo del vector de dirección
Una vez calculados los vectores de atracción y repulsión, se obtiene la dirección final combinando el avance hacia el **waypoint**
con el ajuste lateral para poder evitar los obstáculos.

Si el vector resultante es prácticamente nulo se usa el último sentido de giro registrado o una dirección inicial de escape.

### Control de velocidad y giro
El ángulo de giro se calcula mediante atan2 y se utiliza para obtener la velocidad angular, limitada por W_MAX.

```python
angle = math.atan2(avg_y, avg_x)
w = clamp(K_W * angle, -W_MAX, W_MAX)
```
La velocidad lineal se ajusta entre 0,8 y 3,0, reduciéndose en los giros pronunciados. Además, se analiza la zona frontal del láser, en un ángulo de ±15°:
* Si el obstáculo está a menos de 0,45 metros, el vehículo se detiene.
* Si está a menos de 0,9 metros, la velocidad se reduce a 0,6.
* En otros casos, la velocidad depende del ángulo de giro.

Este control permite adaptar el movimiento a los obstáculos y reducir el riesgo de colisiones.

### Problemas Encontrados
Uno de los principales problemas encontrados durante el desarrollo ha sido conseguir un equilibrio entre la velocidad del coche y la capacidad de evitar obstáculos. 
Una repulsión demasiado intensa provoca que el vehículo se desvíe excesivamente de los waypoints, mientras que una repulsión demasiado débil puede hacer que no reaccione a tiempo y termine chocando.

### Conclusión
El algoritmo VFF permite al F1 navegar autónomamente hacia distintos waypoints, combinando la atracción hacia el objetivo y la repulsión de los obstáculos detectados por el láser.

Los ajustes realizados mejoran la estabilidad y el control de velocidad, aunque el algoritmo puede presentar dificultades en espacios estrechos o esquinas. 
Aun así, es una solución sencilla para la navegación reactiva sin necesidad de un mapa completo.

### Video del Funcionamiento
https://youtu.be/jjQQzrMO-1o




