# Práctica 1 - Basic Vacuum Cleaner

Esta práctica consiste en programar una aspiradora capaz de desplazarse por una intentando cubrir la mayor cantidad posible de superficie. Para ello he usado una máquina de estados que comian distintos tipos de movimientos

## Funcionamiento general

El robot usa el sensor laser para detectar los obstaculos, se usa el valor de la posición `90`, la cual esta relacionada con la posición justamente central del robot, por la parte de delante.
Cuando el robot detecta un obstáculo a menos de `0.4 m` cambia su comportamiento para no chocarse.

He dividido el comportamiento en cuatro estados:

- **SPIRAL(0):** el robot avanza realizando una espiral.
- **BACKWARDS(1):** el robot retrocede cuando se encuentra un obstaculo.
- **TURN(2):** el robot realiza un giro aleatorio antes de volver a avanzar.
- **DASH(3):** el robot avanza rapidamente durante un pequeño periodo de tiempo.

El ciclo habitual de evasión es:

**SPIRAL → BACKWARDS → DASH → TURN → SPIRAL**

## Estados del robot
### SPIRAL
Se trata del estado inicial del robot, este avanza mientras gira de manera simultanea realizando una trayectoria en forma de espiral.
A este estado del ciclo le hemos atribuido las siguientes velocidades:
- **Velocidad Linea:** 0.6
- **Velocidad angular:** 0.7
La velocidad va aumentando poco a poco a medida que pasa el tiempo para que la espiral vaya cambiando su tamaño

### BACKWARDS

Cuando nos encontramos con uno de los obstaculos de masa, entonces el robot retrocede durante unos segundos, y depues de retroceder, pasamos al siguiente eestado
