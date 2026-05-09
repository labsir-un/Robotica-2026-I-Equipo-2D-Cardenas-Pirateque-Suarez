<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h1>Laboratorio No. 02 - Robótica Industrial - Análisis y Operación del Manipulador Motoman MH6.</h1>
<h2>Profesores: <br>Pedro Fabián Cárdenas Herrera <br> Manuel Felipe Carranza Montenegro</h2>

<br>
<img src="Images/motoman.png" alt="Motoman" style="border-radius: 5px; width: 400px;"><br>
<b>Figura 1. Manipulador Motoman MH6.</b>
</div>

---

## 1. Introducción

Los manipuladores industriales son herramientas clave en la automatización industrial. Cada modelo tiene sus propias características técnicas y configuraciones iniciales que los hacen ideales para diferentes aplicaciones. En este taller, se busca realizar una comparación técnica entre el manipulador Motoman MH6 y el ABB IRB140, comprender las configuraciones iniciales del Motoman MH6, explorar los diferentes modos de operación manual, y realizar simulaciones y ejecuciones reales de trayectorias usando RoboDK.

## 2. Comparación: Motoman MH6 vs ABB IRB140

| Característica | **Motoman MH6** | **ABB IRB140** |
|---|---|---|
| **Fabricante** | Yaskawa Motoman | ABB Robotics |
| **Estructura** | Articulada  | Articulada  |
| **Grados de Libertad** | 6 + 2 externos | 6 |
| **Masa del robot** | 130 kg | 98 kg |
| **Alcance** | 1422 mm | 810 mm |
| **Repetibilidad** |	0.08 mm |	0.08 mm |
| **Software de simulación** | MotoSim EG-VRC | RobotStudio |


## 3. Configuraciones Home1 y Home2 del Motoman MH6

## 4. Movimientos Manuales, modos, traslaciones y rotaciones 

## 5. Niveles de Velocidad para Movimientos Manuales

## 6. Software RoboDK – Aplicaciones y Comunicación



RoboDK es un potente software de simulación y programación de robots industriales diseñado para ser compatible con más de 40 fabricantes de robots incluyendo Yaskawa/Motoman, ABB, KUKA, Universal Robots, entre otros.. Sus aplicaciones principales en el entorno industrial y académico incluyen:



- Programación Fuera de Línea (Offline Programming - OLP): Permite diseñar, simular y programar el robot en un entorno virtual tridimensional sin necesidad de detener la máquina física en la línea de producción.

- Simulación de trayectorias y validación: Sirve para verificar alcances, detectar posibles colisiones entre el brazo, la herramienta y el entorno, y evitar singularidades articulares antes de ejecutar el movimiento real.

- Manufactura avanzada: Es ampliamente utilizado para generar trayectorias complejas a partir de modelos CAD/CAM para aplicaciones como fresado robótico, impresión 3D, soldadura, pintura, y operaciones de pick and place (recoger y colocar).



## 7. Comparación: RoboDK vs RobotStudio


| Característica | **RoboDK** | **RobotStudio** |
|---|---|---|
| **Fabricante** | RoboDK Inc. (spin-off CoRo Lab, Canadá) | ABB Robotics |
| **Compatibilidad** | +40 fabricantes (Yaskawa, KUKA, FANUC, ABB, UR, etc.) | Exclusivo para robots ABB |
| **Fidelidad de simulación** | Alta (cinemática correcta), pero sin controlador virtual real | Máxima fidelidad: usa el controlador virtual ABB (VRC) idéntico al real |
| **Lenguaje de robot** | Genera código nativo vía post-procesadores (no ejecuta INFORM/RAPID directamente) | Ejecuta y depura RAPID directamente |
| **Multi-robot** | Sí, múltiples marcas en la misma simulación | Solo robots ABB |
| **API / Programación** | Python, C#, .NET, C++ | Visual Basic, RAPID, C# |

## 8. Diagrama de Flujo del Robot

## 9. Plano de Planta del Laboratorio

## 10. Código RoboDK 

### 🐍 Código Python para RoboDK

```python


```

## 11. Demostración en Video

Para evidenciar el correcto funcionamiento de la arquitectura de control diseñada, se ha documentado la ejecución de la celda de manufactura en video. 

En la demostración se puede observar 

Haga clic en el siguiente enlace para ver el video completo de la implementación simulada y física:

<br>

<div align="center">
  <a href="youtube.com" target="_blank">
    <img src="https://img.shields.io/badge/Ver_Video_en_YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white" alt="Ver Video" />
  </a>
</div>





