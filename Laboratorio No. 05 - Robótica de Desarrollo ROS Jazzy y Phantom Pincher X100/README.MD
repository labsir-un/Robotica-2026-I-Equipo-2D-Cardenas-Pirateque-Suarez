
<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h3>Curso de Robótica 2026-I</h3>
<h1>Laboratorio No. 05</h1>
<h2>Phantom X Pincher X100 – ROS 2 Jazzy – RViz</h2>
<h4>Profesores: Pedro Fabián Cárdenas Herrera · Manuel Felipe Carranza Montenegro</h4>
<h4>Estudiantes: David Felipe Cárdenas Cubides · David Santiago Pirateque Suárez </h4>

<p>
  <img alt="Ubuntu 24.04 LTS" src="https://img.shields.io/badge/Ubuntu-24.04%20LTS-E95420?logo=ubuntu&logoColor=white">
  <img alt="ROS 2 Jazzy" src="https://img.shields.io/badge/ROS%202-Jazzy-22314E?logo=ros&logoColor=white">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white">
  <img alt="Dynamixel" src="https://img.shields.io/badge/Dynamixel-AX--12A%20%7C%20XL430-00979D">
  <img alt="Estado" src="https://img.shields.io/badge/Estado-Completo-brightgreen">
</p>

<br>
<img src="Images/pincher.png" alt="ros" style="border-radius: 5px; width: 300px;"><br>
<b>Figura 1. Robot Phantom X Pincher X100.</b>
</div>


# Laboratorio No. 05: Phantom X Pincher X100 – ROS 2 Jazzy – RViz.

## 1. Objetivos

- Controlar las articulaciones del modelo simulado del Phantom X Pincher X100 en ROS 2 Jazzy.
- Medir y modelar la geometría del manipulador.
- Implementar movimientos individuales, simultáneos, secuenciales e interpolados sobre el modelo en RViz.
- Aplicar cinemática directa e inversa validando contra la pose observada en RViz.
- Programar trayectorias, repetición de poses y una tarea artística (trazado) y una coreografía, todo en simulación.



## 2. Estructura del repositorio

```
.
├── README.md                     <- este documento
├── docs/
├── phantomx_sim_bringup/         <- launch files RViz + joint_state_publisher_gui
├── phantom_ws/         <- paquete ROS 2 con nodos propios (Python)
│   ├── src
│   ├── phantomx_pincher
│   │   ├── phantomx_pincher_description
│   │   ├── phantomx_pincher_bringup
│   │   ├── phantomx_pincher_moveit_config
│   │   └── pincher_cproyect
│   ├── scripts/
│   │   ├── act15.py
│   ├── build        
│   ├── install    
│   └── log      
```

---

## 3. Identificación del Hardware y Geometría (Actividades 2 y 3)

### 3.1 Identificación de Motores y Articulaciones
*(Resultados de la Actividad 2. Complete la siguiente tabla tras la inspección física del robot)*

| Articulación | ID | Referencia | Sentido positivo | Función |
| :--- | :---: | :--- | :--- | :--- |
| **Base** | | | | |
| **Hombro** | | | | |
| **Codo** | | | | |
| **Muñeca** | | | | |
| **Pinza** | | | | |

### 3.2 Medición del Robot y Plano de Planta
*(Resultados de la Actividad 3. Enumera las dimensiones obtenidas y adjunta el diagrama con los sistemas coordenados)*
* **Altura de la base:** * **Distancia entre ejes consecutivos:**
* **Longitud de cada eslabón:**
* **Distancia TCP:**
* **Dimensiones de la pinza:**

`![Plano de Planta / Diagrama Geométrico](ruta/a/tu/diagrama.png)`

---

## 4. Control Articular y Calibración (Actividades 4, 5 y 6)

### 4.1 Calibración de Cero y Error Articular
*(Resultados de la Actividad 5. Añade las gráficas de posición deseada vs medida y los cálculos de error)*
* **Error máximo:**
* **Error promedio:**
* **Desplazamiento de cero calculado:**

`![Gráficas de Calibración](ruta/a/tu/grafica1.png)`

### 4.2 Determinación de Límites Seguros
*(Resultados de la Actividad 6. Complete la siguiente tabla sin alcanzar los topes mecánicos)*

| Articulación | Límite inferior | Límite superior | Margen de seguridad |
| :--- | :---: | :---: | :---: |
| **Base** | -145.0° | 145.0° | 5.0° |
| **Hombro** | -96.0° | 96.0° | 4.0° |
| **Codo** | -138.0° | 144.0° | 6.0° |
| **Muñeca** | -106.0° | 124.0° | 4.0° |
| **Pinza** | -35.0° | 35.0°  | 5.0° |

---

## 5. Cinemática del Manipulador (Actividades 11 y 12)

### 5.1 Cinemática Directa
*(Explica la obtención de los parámetros DH y cómo se implementó en el código. Evalúa las 5 configuraciones de la Actividad 7)*
* **Tabla de Parámetros Denavit-Hartenberg:** *(Puedes agregar una tabla aquí si lo deseas)*
* **Comparación con RViz:** *(Comenta si los cálculos coincidieron con la visualización)*

### 5.2 Cinemática Inversa
*(Explica el enfoque utilizado: geométrico o algebraico. Detalla cómo se descartan las soluciones fuera de límites y cómo se maneja codo arriba/abajo)*

---

## 6. Generación de Trayectorias y Tareas Complejas (Actividades 7 a 10, 13 a 15)

En esta sección se documentan los diferentes *scripts* o módulos desarrollados para el control de trayectorias.

### 6.1 Movimiento Simultáneo y Secuencial
* **Descripción:** Comparativa entre mover articulaciones a la vez vs una por una.
* **Análisis de suavidad y tiempo de ejecución:** *(Añadir conclusiones de la Actividad 8)*

### 6.2 Interpolación y Trayectoria Sinusoidal
*(Gráficas y análisis de la Actividad 9 y 10)*
* **Interpolación Lineal vs Cúbica/Quíntica:** *(Añadir gráficas)*
* **Trayectoria Sinusoidal:** *(Indicar error máximo y error cuadrático medio)*

### 6.3 Enseñanza y Repetición de Poses (Guardado YAML)
* **Descripción:** Explicación de cómo el script lee y guarda las configuraciones en el archivo `.yaml`.
* **Ruta del archivo YAML en el repo:** `ruta/a/tu/archivo_poses.yaml`

### 6.4 Trazado de Figura
* **Figura seleccionada:** *(Triángulo, Cuadrado, Círculo o Iniciales)*
* **Implementación:** *(Cómo se generaron los puntos cartesianos y se pasaron por la cinemática inversa)*

### 6.5 Coreografía Robótica
* **Canción seleccionada:** *(Chipi Chipi Chapa Chapa / Pedro, Pedro, Pe-)*
* **Estructura del código de la coreografía:** *(Cómo se sincronizaron los tiempos de ROS con la música)*

---

## 7. Enlaces a Videos Demostrativos

> **Nota:** Todos los videos inician con la introducción oficial del laboratorio LabSIR.

* 🎬 **1. Movimiento individual, simultáneo y secuencial:** [Enlace a YouTube / Drive]
* 🎬 **2. Enseñanza y repetición de poses:** [Enlace a YouTube / Drive]
* 🎬 **3. Trazado de figuras:** [Enlace a YouTube / Drive]
* 🎬 **4. Coreografía robótica (Reto final completo):** [Enlace a YouTube / Drive]

---

## 8. Conclusiones Individuales

### Conclusiones de [Nombre del Integrante 1]
* *(Escribe aquí tus conclusiones sobre ROS 2, la cinemática, las dificultades, etc.)*
* ...

### Conclusiones de [Nombre del Integrante 2]
* *(Escribe aquí tus conclusiones sobre ROS 2, la cinemática, las dificultades, etc.)*
* ...
