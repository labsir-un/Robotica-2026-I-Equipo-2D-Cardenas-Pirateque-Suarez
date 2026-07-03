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

donde el archivo de principal edición seria el `move_turtle.py` 

## Diagrama de flujo

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

## 5. Codigo
```python



```

## 6. Instrucciones de Ejecución
(Paso a paso rápido con los comandos de consola para que el profesor pueda compilar el workspace y correr el nodo sin problemas).

## 7. Video Explicativo
(El enlace de YouTube/Vimeo del video de máximo 10 minutos).
