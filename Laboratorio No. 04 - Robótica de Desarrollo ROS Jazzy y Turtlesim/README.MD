<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h3>Curso de Robótica 2026-I</h3>
<h1>Laboratorio No. 04</h1>
<h2>Robótica de Desarrollo, Intro a ROS2 Jazzy Jalisco - Turtlesim</h2>
<h4>Profesores: Pedro Fabián Cárdenas Herrera · Manuel Felipe Carranza Montenegro</h4>
<h4>Estudiantes: David Felipe Cardenas Cubides · David Santiago Pirateque Suarez </h4>

<p>
  <img alt="Ubuntu 24.04 LTS" src="https://img.shields.io/badge/Ubuntu-24.04%20LTS-E95420?logo=ubuntu&logoColor=white">
  <img alt="ROS 2 Jazzy" src="https://img.shields.io/badge/ROS%202-Jazzy-22314E?logo=ros&logoColor=white">
  <img alt="TurtleBot3" src="https://img.shields.io/badge/TurtleBot3-RViz2-2ea44f">
  <img alt="Estado" src="https://img.shields.io/badge/Estado-Completo-brightgreen">
</p>

<br>
<img src="Images/ros.png" alt="ros" style="border-radius: 5px; width: 300px;"><br>
<b>Figura 1. Robot Operating System.</b>
</div>


# Laboratorio 04: Control y Arquitectura en ROS 2 Jazzy (Turtlesim)

## 1. Objetivos

### Objetivo General

Implementar un paqueto de ROS2 Jazzy Jalisco que permita controlar  de una a dos tortugas del suimulador de Turtlesim mediante un nodo configurado desde python, este debe incluir funciones para control manual, trayectorias automaticas, dibujo tanto de escrituras como de letras y un sistema de líder-seguidor. 


### Objetivo específicos

- Comprender ROS2.
- Implementar nodos.
- Controlar turtlesim.
- Implementar comunicación mediante tópicos y servicios.
- Desarrollar un sistema líder-seguidor.


## 2. Introducción

En elm presente laboratorio se tuvo como objetivo desarrollar una aplicación en ROS2 que permitiera controlar el movimiento de una tortuga dentro del simulador Turtlesim, aplicando los conceptos básicos de programación en python de nodos y comunicación entre ellos. A lo largo del desarrollo se implementaron diferentes funciones que permitieron interactuar con el simulador tanto de forma manual como automática.

La solución fue desarrollada en Ubuntu 24.04 utilizando ROS2 Jazzy Jalisco, Python y rclpy. Entre las principales funcionalidades implementadas se encuentran el control mediante el teclado, la generación automática de trayectorias como cuadrados y triángulos, el dibujo de letras correspondientes a las iniciales de los integrantes del grupo en este caso D F C P S, el manejo del lápiz de dibujo, el reinicio de la tortuga y un sistema líder-seguidor en el que una segunda tortuga replica el movimiento de la principal a partir de la información recibida por medio de tópicos de ROS2.



## 3. Tecnologías usadas


<br>
<div align="center">

| Tecnología | Versión | Descripción |
| :--- | :--- | :--- |
| **Ubuntu** | 24.04 LTS | Sistema operativo donde se instaló y ejecutó el entorno de desarrollo ROS2.  |
| **ROS2**| Jazzy Jalisco | Framework utilizado para desarrollar la aplicación robótica basada en nodos, tópicos y servicios. |
| **Python**| 3.9 | Lenguaje de programación utilizado para implementar la logica del laboratorio.  |
| **rclpy**| ROS2 | Biblioteca de python para creación de nodos, publicadores, suscriptores y clientes de servicios. |
| **Turtlesim** | ROS2 | Simulador utilizado para visualizar y controlar el movimiento de las tortugas durante el laboratorio. |
| **Git** | Web | Sistema de gestion donde se alamcena el repositorio. |
| **VirtualBox** | 7.2.8 | Software de maquina virtual donde se instaló Ubuntu 24.04 lts. |

</div>
<br>

## 4. Estructura del proyecto

El desarrollo del laboratorio se realizó dentro de uno de los paquetes llamado **my_turtle_controller**, el cual se encuentra ubicado en el *workspace* de ROS 2. Esto se debe a que el paquete contiene no solo el nodo principal encargado del movimiento de la tortuga, sino también los archivos necesarios para ejecutarlo. teniendo de estructura:

```
~/ros2_jazzy/src/
└── my_turtle_controller/
    ├── package.xml
    ├── setup.py
    ├── setup.cfg
    ├── resource/
    │   └── my_turtle_controller
    ├── my_turtle_controller/
    │   ├── __init__.py
    │   └── move_turtle.py
    └── test/
```

El archivo principal de edición es `move_turtle.py`, el cual contiene toda la lógica implementada para el desarrollo del laboratorio. En este archivo se encuentran las funcionalidades correspondientes a:

* Control manual.
* Configuración de figuras.
* Configuración de letras.
* Lógica del modo líder-seguidor.

Además de esto, se modificó el archivo `setup.py` para registrar el nodo ejecutable, añadiendo las siguientes líneas:

```python
entry_points={
    'console_scripts': [
        'move_turtle = my_turtle_controller.move_turtle:main',
    ],
},
```

Por último, se modificó el archivo `package.xml` para incluir las dependencias necesarias, como `rclpy`, `turtlesim`, entre otras. Para ello, se añadieron las siguientes líneas:

```
<depend>rclpy</depend>
<depend>geometry_msgs</depend>
<depend>turtlesim</depend>
<depend>std_srvs</depend>
```

## 5. Diagrama de flujo

```mermaid
flowchart TD
    A([Inicio del programa]) --> B[Inicializar ROS 2]
    B --> C[Crear nodo MoveTurtle]
    C --> D[Crear publicadores, suscriptores, clientes y Timer Líder-Seguidor]
    D --> E[Iniciar hilo dedicado: rclpy.spin]
    D --> F[Hilo Principal: Entrar al bucle de teclado run]

    %% HILO DE PROCESAMIENTO ROS 2 (Sub-proceso de fondo)
    E --> Z[¿Se activa el callback del Timer a 20Hz?]
    Z -->|Sí| AA{¿Existe turtle2 spawned?}
    AA -->|No| Z
    AA -->|Sí| AB[Leer pose de turtle1 y turtle2]
    AB --> AC[Calcular distancia y orientación]
    AC --> AD[Calcular velocidades proporcionalmente]
    AD --> AE[Publicar en /turtle2/cmd_vel]
    AE --> Z

    %% BUCLE PRINCIPAL DE TECLADO
    F --> G[Leer tecla del teclado get_key]
    G --> H{¿Qué tecla fue presionada o evento?}

    H -->|↑ ↓ ← →| I[Control manual fluido]
    I --> J[Publicar velocidad en /turtle1/cmd_vel]
    J --> F

    H -->|I| S[Invertir estado de self.initials_mode]
    S --> F

    H -->|S, T, P, D, F, C| K{¿self.initials_mode es True?}
    K -->|Sí| L[Dibujar Letra según la tecla D, S, P, F o C]
    K -->|No| M{¿Qué tecla estándar?}
    M -->|S| N[Dibujar cuadrado]
    M -->|T| O[Dibujar triángulo]
    M -->|P| P[Activar/Desactivar lápiz toggle_pen]
    L --> J
    N --> J
    O --> J
    P --> F

    H -->|A| Q[Ejecutar trayectoria automática]
    Q --> QA{¿Cerca del borde?}
    QA -->|Sí| QB[Girar a velocidad angular auto]
    QA -->|No| QC[Avanzar en línea recta]
    QB --> J
    QC --> J
    
    H -->|V| U[Ejecutar secuencia automática de video]
    U --> J

    H -->|R| M_R[Reiniciar simulación reset_turtle]
    M_R --> F

    H -->|2| V[Generar turtle2 spawn_turtle2]
    V --> F

    H -->|3| W[Eliminar turtle2 kill_turtle2]
    W --> F

    H -->|Q / Inactividad > 0.15s| X[Detener movimiento total stop]
    X --> J

    H -->|Ctrl+C / Excepción| Y([Restaurar terminal y Finalizar programa])
```

## 6. Arquitectura del paquete


## 7. implementación

La solución del laboratorio se desarrolló principalmente en el archivo `move_turtle.py`, donde se implementó un nodo de ROS 2 encargado de controlar el movimiento de la tortuga, ejecutar trayectorias automáticas, dibujar letras y gestionar el sistema de control líder-seguidor.

### 7.1 Creacion de nodo, suscrptores y publicadores.

Para comenzar se inicia el nodo principal da la aplicacion iniciando asi todos los programas necesarios para el funcionamiento

```python

class MoveTurtle(Node):

    def __init__(self):
        super().__init__('move_turtle_node')

```

Despues se crean los publicadores y suscriptores donde los publicadores seran los encargados de enviar los diferentes comandos de velocidad lineal y angular a las tortugas y los suscriptores los encargados de resivir continuamente la pósicion de las las tortugas.

```python

self.cmd_vel_pub = self.create_publisher(
    Twist,
    '/turtle1/cmd_vel',
    10
)

self.pose_sub = self.create_subscription(
    Pose,
    '/turtle1/pose',
    self.pose_callback,
    10
)

```

Ademas de esto creo otro publicador y otro seguidor para el sistema de lider-seguidor 

```python

self.cmd_vel2_pub = self.create_publisher(
    Twist,
    '/turtle2/cmd_vel',
    10
)

self.pose2_sub = self.create_subscription(
    Pose,
    '/turtle2/pose',
    self.pose2_callback,
    10
)

```
### 7.2 Creación de clientes de servicios.

Continuando se programan los distintos servicios para contorlar las diferentes funcionalidades

```python

self.teleport_client = self.create_client(
    TeleportAbsolute,
    '/turtle1/teleport_absolute'
)

self.set_pen_client = self.create_client(
    SetPen,
    '/turtle1/set_pen'
)

self.spawn_client = self.create_client(
    Spawn,
    '/spawn'
)

self.kill_client = self.create_client(
    Kill,
    '/kill'
)

```

### 7.3 Control manual.

Usando la función `get_key()` se lee el teclado para despues llamar a las siguientes funciones encargadas del movimientos. 

```python

if key == KEY_UP:
    self.move_forward()

elif key == KEY_DOWN:
    self.move_backward()

elif key == KEY_LEFT:
    self.turn_left()

elif key == KEY_RIGHT:
    self.turn_right()

```
donde función publica un mensaje Twist sobre el tópico /turtle1/cmd_vel

```python
def move_forward(self):
    self.current_v = LINEAR_SPEED
    self.publish_velocity(
        linear=self.current_v,
        angular=self.current_w
    )

```
### 7.4 Figuras automaticas.

Para el dibujo de elementos se implemento usando las funciones basicas de giro y movimientos.

para el triangulo se realiza el mismo movimiento 3 veces para crear sus tres lados de la siguiente forma:


```python

    def draw_triangle(self, side_length=SHAPE_SIDE_LENGTH):
        self.get_logger().info('Dibujando triangulo...')
        for lado in range(3):
            if not self.move_by_distance(side_length): return
            if not self.turn_by_angle(2 * math.pi / 3): return
        self.get_logger().info('Triangulo completado.')

```


Obteniendo como resultado:


<br>

<div align="center">
  <img src="Images/triangulo.png" alt="triag" style="border-radius: 5px; width: 350px;">
  <br>
  <b>Figura 2. Figura de triangulo.</b>
</div>

<br>


para el rectangulo se realiza el mismo movimiento 4 veces para crear sus lados de la siguiente forma:



```python

    def draw_square(self, side_length=SHAPE_SIDE_LENGTH):
        self.get_logger().info('Dibujando cuadrado...')
        for lado in range(4):
            if not self.move_by_distance(side_length): return
            if not self.turn_by_angle(math.pi / 2): return
        self.get_logger().info('Cuadrado completado.')

```
Obteniendo como resultado:

<br>

<div align="center">
  <img src="Images/rectangulo.png" alt="rec" style="border-radius: 5px; width: 350px;">
  <br>
  <b>Figura 3. Figura de rectangulo.</b>
</div>

<br>


### 7.5 Dibujo de letras.

En el caso del dibujo de letras, se implementó una rutina independiente para cada una de ellas, con el fin de facilitar su construcción a partir de líneas rectas y semicircunferencias, como se puede ver acontinuación:

```python 

def draw_D(self):
        self.get_logger().info('Dibujando letra D...')
        self._set_pen(False) 
        if not self.turn_to_angle(math.pi / 2): return       
        if not self.move_by_distance(2.0): return            
        if not self.turn_to_angle(0.0): return               
        if not self.draw_arc(-math.pi, radius=1.0): return   
        self._set_pen(True)  
        self.turn_to_angle(0.0)

    def draw_S(self):
        self.get_logger().info('Dibujando letra S...')
        self._set_pen(False) 
        if not self.turn_to_angle(math.pi / 2): return               
        if not self.draw_arc(1.5 * math.pi, radius=0.5): return    
        if not self.turn_to_angle(0.0): return           
        if not self.draw_arc(-1.5 * math.pi, radius=0.5): return   
        self._set_pen(True)  
        self.turn_to_angle(0.0) 

    def draw_P(self):
        self.get_logger().info('Dibujando letra P...')
        self._set_pen(False) 
        if not self.turn_to_angle(math.pi / 2): return       
        if not self.move_by_distance(2.0): return            
        if not self.turn_to_angle(0.0): return               
        if not self.draw_arc(-math.pi, radius=0.65): return   
        self._set_pen(True)  
        self.turn_to_angle(0.0)

    def draw_F(self):
        self.get_logger().info('Dibujando letra F...')
        self._set_pen(False) 
        if not self.turn_to_angle(math.pi / 2): return       
        if not self.move_by_distance(2.0): return            
        if not self.turn_to_angle(0.0): return               
        if not self.move_by_distance(1.0): return            
        
        self._set_pen(True)  
        if not self.turn_to_angle(math.pi): return           
        if not self.move_by_distance(1.0): return            
        if not self.turn_to_angle(-math.pi / 2): return      
        if not self.move_by_distance(1.0): return            
        if not self.turn_to_angle(0.0): return               
        
        self._set_pen(False) 
        if not self.move_by_distance(0.8): return            
        self._set_pen(True)  
        self.turn_to_angle(0.0)

    def draw_C(self):
        self.get_logger().info('Dibujando letra C...')
        self._set_pen(True)  
        if not self.turn_to_angle(math.pi / 2): return       
        if not self.move_by_distance(2.0): return
        if not self.turn_to_angle(0.0): return               
        if not self.move_by_distance(1.0): return
        if not self.turn_to_angle(math.pi): return           
        
        self._set_pen(False) 
        if not self.draw_arc(math.pi, radius=1.0): return    
        self._set_pen(True)  
        self.turn_to_angle(0.0)
```

Con esto, al presionar la tecla `V`, se ejecuta la rutina definida a partir de estos movimientos y se ubica cada una de las letras de forma ordenada, dando como resultado:



<br>

<div align="center">
  <img src="Images/letras.png" alt="letr" style="border-radius: 5px; width: 350px;">
  <br>
  <b>Figura 4. Dibujo de letras.</b>
</div>

<br>

### 7.6 Sistema líder-seguidor.

el seguimiento funciona premero con la implementacion de un temporizador que calcula la distancia entra embas tortugas.

```python
dx = self.current_pose.x - self.pose2.x
dy = self.current_pose.y - self.pose2.y

distance = math.hypot(dx, dy)

target_theta = math.atan2(dy, dx)
```
ya con esos datos se calcula la velocidad proporcional

```python
msg.linear.x = 1.5 * distance

msg.angular.z = 6.0 * error_theta

```
Finalmente, la velocidad es enviada al tópico correspondiente.

```python
self.cmd_vel2_pub.publish(msg)

```

<br>

<div align="center">
  <img src="Images/seguidor.png" alt="seg" style="border-radius: 5px; width: 350px;">
  <br>
  <b>Figura 5. Sistema Líder-seguidor.</b>
</div>

<br>

## 8. Instrucciones de Ejecución
(Paso a paso rápido con los comandos de consola para que el profesor pueda compilar el workspace y correr el nodo sin problemas).

## 9. Video Explicativo
(El enlace de YouTube/Vimeo del video de máximo 10 minutos).
