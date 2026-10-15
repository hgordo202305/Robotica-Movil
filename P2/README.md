# Práctica 2 - Follow Line
Esta practica consiste en un coche de F1 capaz de completar varios circuitos haciendo uso de la detección de una linea roja, y usando un controlador para ajustar las velocidades de este.

## Funcionamiento General

### Detección de Linea (procesado de imagen)
El robot obtiene imagenes de la cámara y procesa unicamente la mitad inferior de esta, donde se encuentra la linea roja a seguir. Despues usaremos cv2 para convertir la imagen de BGR a HSV
y poder detectar el color rojo mediante la aplicacion de una máscara.
Vamos a usar dos rangos de rojo que luego vamos a combinar para obtener la máscara:
```python
red_orange_mask = cv2.inRange(bgr_to_hsv, lower_red1, upper_red1)
red_pink_mask = cv2.inRange(bgr_to_hsv, lower_red2, upper_red2)

mask = cv2.bitwise_or(red_orange_mask, red_pink_mask)
```
### Calculo del centroide
Una vez hemos procesado y filtrado la imagen para poder detectar la linea, vamos a calcular su posición horizontal media para obtener el centroide, este se obtiene recorriendo cada fila de la mascara y se calcula
la media de las posiciones horizontales (X) de los pixeles rojos detectados. Después, se calcula la media de todas las medias obtenidas en las filas con pixeles rojos, de esta manera se estima la posición del centroide
de la linea.

A partir de la posición del centroide podemos calcular el erorr respestco al centro de la imagen, si este es positivo entonces la linea esta desplazada hacia la derecha, si es negativo esta desplazada a la izquierda (el error lo usaremos
más adelante en el controlador PD para corregir la trayectoria del F1)

<img width="373" height="126" alt="image" src="https://github.com/user-attachments/assets/9ac1819b-ac15-427e-a5a8-fa234927fd0c" />


### Controlador PD
Para corregir la trayectoria he utilizado un controlador PD, compuesto por un término proporcional (P), que responde al error actual, y un término derivativo (D), que ayuda a reducir las oscilaciones.

Se ha elegido un PD en lugar de un PID porque no se ha considerado necesario añadir un término integral al controlador externo. El sistema de movimiento del robot puede incorporar su propia regulación interna y uan doble implementacion podría saturarlo.
```python
linear_speed = 12.0 - 0.025 * abs(error)
```
Si el robot pierde la línea, detiene el avance e intenta recuperarla girando según la dirección del último error registrado.

## Problemas Encontrados
Los principales problemas que he encontrado en la práctica han sido las oscilaciones, especialmente en las curvas mas cerradas donde el coche acababa por desubicarse y salirse del circuito. Para poder mejorar este comportamiento
he ido ajustando los parametros del PID para suavizar el error.

A la hora de elegir el valor adecuado para las velocidades y los parámetros de Kp y Kd, lo he decidido principalmente mediante **prueba y error**, observando el comportamiento en el circuito hasta que ha podido hacer un seguimiento 
mas o menos estable.

## Conclusion
Con este algoritmo, el F1 es capaz de detectar una línea roja mediante visión artificial y corregir su trayectoria utilizando un controlador PD. El ajuste de los parámetros ha sido fundamental para reducir las oscilaciones y mejorar el seguimiento, especialmente en las curvas.

## Video del Funcionamiento
### Video en el circuito simple


https://github.com/user-attachments/assets/a5edccff-1fac-4862-b5b3-07faa2f599b1


### Video en el circuito de Nurburing
En este video vemos el coche terminar el circuito de  este lo completa al máximo, pero no con los parámetros originales ya que si no el coche no podia completarlo, para hacerlo se ha aumentado la Kp y disminuido la velocidad

https://youtu.be/2WONi28UsYk

### Video en el circuito de Montmelo
En este video podemos ver como el coche inicia el circuito de  , pero no llega a completarlo debido a una curva muy cerrada que no es capaz de rebasar, aunque no lo complete, podemos observar en el video la logica de giro en busca de la linea roja de nuevo

https://youtu.be/HVly3yHMJ3I



