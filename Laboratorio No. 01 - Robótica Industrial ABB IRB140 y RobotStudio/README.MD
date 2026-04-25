<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h1>Laboratorio No. 01: Robótica Industrial - Trayectorias, Entradas y Salidas Digitales</h1>
<h2>Profesores: <br>Pedro Fabián Cárdenas Herrera <br> Manuel Felipe Carranza Montenegro</h2>

<br>
<img src="images/abbs.jpeg" alt="ABB" style="border-radius: 5px; width: 400px;"><br>
<b>Figura 1. Celda de manufactura con manipuladores ABB IRB 140.</b>
</div>

---

## 1. Introducción

En el presente laboratorio se estudian los principios fundamentales de la robótica industrial mediante la programación de trayectorias, el diseño y calibración de herramientas (ToolData), y la integración de señales de control a través de entradas y salidas digitales (E/S). La práctica se desarrolla sobre un manipulador ABB IRB 140 controlado por la unidad IRC5, operando en conjunto con una banda transportadora. Este entorno físico se replica computacionalmente mediante el uso de gemelos digitales en el software RobotStudio, permitiendo la validación previa de la lógica de control.

El escenario de aplicación se inspira en la automatización de la industria alimentaria, proponiendo la ejecución de una rutina de decoración sobre una superficie virtual (torta). Para la resolución de este problema, se establecieron los siguientes requerimientos técnicos:

* Área de trabajo definida para un volumen equivalente a un pastel de 20 porciones.
* Parámetros de interpolación restringidos a velocidades entre `v100` y `v1000`, con una zona de aproximación máxima de `z10`.
* Movimiento continuo para cada trazo, partiendo y finalizando en una pose de seguridad (Home).
* Independencia en el trazo de cada uno de los nombres.
* Implementación de lógicas de control mediante señales digitales para la gestión de rutinas (decoración y mantenimiento) y el accionamiento de periféricos (banda transportadora).

---

## 2. Solución Planteada

La estrategia de resolución se dividió en dos fases: la preparación del entorno de trabajo (físico y virtual) y la arquitectura de control en lenguaje RAPID.

Inicialmente, se diseñó un actuador final (herramienta) capaz de sujetar un marcador. En el entorno de simulación, se estableció un *WorkObject* con dimensiones de 22×19×5 cm para representar la superficie de trabajo. Sobre estas coordenadas geométricas se diseñaron las trayectorias correspondientes a la decoración y a la escritura de los nombres.

<div align="center">
<table>
  <tr>
    <td align="center">
      <img src="images/disenomar.png" width="250"><br>
      <b>Figura 2. Modelado de herramienta</b>
    </td>
    <td align="center">
      <img src="images/Dibujopastel.png" width="250"><br>
      <b>Figura 3. Diseño de trayectorias</b>
    </td>
    <td align="center">
      <img src="images/disenopas.png" width="250"><br>
      <b>Figura 4. Modelado del WorkObject</b>
    </td>
  </tr>
</table>
</div>

La validación del sistema se realizó primero en RobotStudio. Tras calibrar el `ToolData` y el `WorkObject`, se programaron las secuencias de movimiento con parámetros nominales de `v100` y `z1`. La lógica de interacción con el entorno se definió mediante el mapeo de las siguientes señales digitales:

* **DI_01:** Inicia el ciclo principal. Activa la banda transportadora durante 3 segundos para el posicionamiento de la pieza, ejecuta la rutina de interpolación (decoración) y, al retornar el robot a su posición Home, reactiva la banda por 5 segundos para la evacuación del producto.
* **DI_02:** Desplaza el manipulador a una pose segura de mantenimiento (facilitando el cambio de herramienta) y pausa el sistema por un máximo de 10 segundos o hasta recibir una nueva confirmación.
* **DI_03:** Controla el retroceso manual de la banda transportadora durante 3 segundos para la calibración inicial de la posición del objeto.
* **DO_01:** Señalización óptica (piloto) activa durante la ejecución de la trayectoria de decoración.
* **DO_03:** Señalización óptica (piloto) activa mientras el robot permanece en la pose de mantenimiento.

Finalizada la simulación, la lógica fue transferida a la celda de manufactura real, donde se ajustaron las calibraciones del TCP y del objeto de trabajo para compensar las tolerancias de fabricación y la topología física del laboratorio.

<div align="center">
<img src="images/Simu.png" alt="ABB" style="border-radius: 5px; width: 400px;"><br>
<b>Figura 5. Entorno de simulación en RobotStudio.</b>
</div>

---

## 3. Diagrama de Flujo de Acciones

El siguiente diagrama ilustra la arquitectura lógica implementada en el controlador, detallando el ciclo de escaneo de señales y las decisiones operativas de la estación.

<div align="center">
  <img src="images/DiagramaFlujo_AccionesRobot.png" alt="Diagrama de Flujo" width="600px"><br>
  <b>Figura 5. Diagrama de flujo de la rutina principal y subrutinas.</b>
</div>
