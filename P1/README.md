# Práctica 1 - Basic Vacuum Cleaner

Esta práctica consiste en programar una aspiradora capaz de desplazarse por una intentando cubrir la mayor cantidad posible de superficie. Para ello he usado una máquina de estados que comian distintos tipos de movimientos

## Funcionamiento general

El robot usa el sensor laser para detectar los obstaculos, se usa el valor de la posición `90`, la cual esta relacionada con la posición justamente central del robot, por la parte de delante.
Cuando el robot detecta un obstáculo a menos de `0.4 m` cambia su comportamiento para no chocarse.

He dividido el comportamiento en cuatro estados:

- **SPIRAL(0):** el robot avanza realizando una espiral.
- **BACKWARDS(1):** el robot retrocede cuando se encuentra un obstaculo.
- **TURN(2):** el robot realiza un giro aleatorio antes de volver a avanzar.
- **FORWARD(3):** el robot avanza en línea recta durante un pequeño periodo de tiempo.

El ciclo habitual de evasión es:

**SPIRAL → OBSTÁCULO → BACKWARDS → TURN → FORWARD → SPIRAL**

## Estados del robot
### SPIRAL
Se trata del estado inicial del robot, este avanza mientras gira de manera simultanea realizando una trayectoria en forma de espiral.
A este estado del ciclo le hemos atribuido las siguientes velocidades:
- **Velocidad lineal:** 0.6
- **Velocidad angular:** 0.7
La velocidad va aumentando poco a poco a medida que pasa el tiempo para que la espiral vaya cambiando su tamaño
Si se detecta un obstáculo, el robot pasa al estado de marcha atrás (BACKWARDS)

### BACKWARDS
Cuando nos encontramos con uno de los obstaculos, entonces el robot retrocede durante unos segundos (aproximadamente `2 segundos`), y depues de retroceder, pasamos al siguiente estado (TURN) que nos ayudara a alejarnos del obstáculo

### TURN
En este estado el robot se queda quieto y gira sobre si mismo, el tiempo que dura el giro se genera de manera aleatoria entre `1 y 8 segundos`. Tambien se decide de manera aleatoria si el robot va a girar hacia un lado o hacia otro (1 y -1).
Una vez termina el giro, el robot pasa al estado (FORWARD) para avanzar en línea recta.

### FORWARD
En este estado el robot avanza en línea recta con una velocidad de `0.6` durante aproximadamente `1 segundo`.
Una vez termina el avance, el robot vuelve al estado inicial (SPIRAL) y comienza de nuevo el ciclo.

## Conclusión
Con este algoritmo, el robot puede explorar el entorno de una manera sencilla y aleatoria, evitando los obstáculos que encuentra durante su recorrido.

Para poder hacer la practica he usado la documentacion de Unibotics: https://jderobot.github.io/RoboticsAcademy/exercises/MobileRobots/vacuum_cleaner




https://github.com/user-attachments/assets/69928a81-82a8-401f-ba94-5a8d5a87258f
