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
| **Ubuntu** | 24.04 LTS |   |
| **ROS2**| Jazzy Jalisco |   |
| **Python**| 3.9 |   |
| **rclpy**| ROS2 |   |
| **Turtlesim** | ROS2  |
| **Git** | Web |   |

</div>
<br>

## 4. Desarrollo e Implementación
(Aquí desglosan las funcionalidades exigidas. Pueden usar estos subtítulos):

    ### Control Manual y Acciones Básicas (Flechas, cambiar lápiz, reset).

    ### Figuras Geométricas y Modo Autónomo (Cuadrado, Triángulo, evasión de bordes).

    ### Dibujo de Iniciales (Explicar brevemente cómo calcularon los tiempos y giros para sus letras).

    ### Sistema Líder-Seguidor (Explicar la matemática del control proporcional y cómo se comunican las dos tortugas).

## 5. Análisis de la Arquitectura en ROS 2 (Evidencias)
(Esta es la sección de las capturas de pantalla de la terminal. Subtítulos sugeridos):

    ### Nodos y Tópicos Activos (Imágenes de ros2 node list y ros2 topic list).

    ### Servicios Disponibles (Imagen de ros2 service list).

    ### Telemetría de Posición (Imagen del echo al tópico de pose).

    ### Mapa de Interconexión (rqt_graph) (Adjuntar la imagen exportada de rqt_graph mostrando cómo su nodo se habla con el simulador).

## 6. Instrucciones de Ejecución
(Paso a paso rápido con los comandos de consola para que el profesor pueda compilar el workspace y correr el nodo sin problemas).

## 7. Video Explicativo
(El enlace de YouTube/Vimeo del video de máximo 10 minutos).
