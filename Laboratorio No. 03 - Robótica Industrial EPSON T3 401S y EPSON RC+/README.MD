<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h1>Laboratorio No. 03 - Robótica Industrial - Análisis y Operación del Manipulador EPSON T3-401S.</h1>
<h2>Profesores: <br>Pedro Fabián Cárdenas Herrera <br> Manuel Felipe Carranza Montenegro</h2>

<br>
<img src="Images/Epson.png" alt="Epson" style="border-radius: 5px; width: 300px;"><br>
<b>Figura 1. Manipulador industrial EPSON T3-401S.</b>
</div>

---

## 1. Introducción
Los manipuladores industriales son herramientas clave en la automatización industrial. Cada modelo tiene sus propias características técnicas y configuraciones iniciales que los hacen ideales para diferentes aplicaciones. En este taller, se busca realizar una comparación técnica entre el manipulador EPSON T3-401S, el Motoman MH6 y el ABB IRB140, comprender las configuraciones iniciales del EPSON T3-401S, explorar los diferentes modos de operación manual, diseñar un gripper neumático y realizar simulaciones y ejecuciones reales de trayectorias usando EPSON RC+ 7.0.

## 2. Comparación Técnica: Motoman MH6, ABB IRB140 y EPSON T3-401S

En el desarrollo de competencias en robótica industrial, es esencial familiarizarse con diversas arquitecturas y entornos de programación. Puesto que a lo largo del curso se han utilizado los manipuladores ABB IRB140 y Yaskawa Motoman MH6, resulta importante identificar las diferencias entre ambos. A pesar de que los dos equipos pertenecen a la clasificación de brazos de estructura articulada vertical y comparten una carga útil máxima de 6 kg, presentan variaciones técnicas significativas en sus dimensiones, velocidades y rangos de movimiento, tal como se detalla a continuación en el siguiente análisis estructurado:

### 2.1 Datos Generales y Alcance

<br>
<div align="center">

| Característica | Motoman MH6 | ABB IRB140 | EPSON T3-401S |
| :--- | :--- | :--- | :--- |
| **Fabricante** | Yaskawa Motoman | ABB | EPSON |
| **Modelo Oficial** | YR-MH00006-A00 | IRB 140-6/0.8 | T3-401S |
| **Estructura** | Articulada vertical | Articulada vertical | SCARA (Horizontal) |
| **Grados de libertad** | **6 + 2 externos** | 6 | 4 |
| **Carga máxima (payload)**| 6 kg | 6 kg | 3 kg (Nominal: 1 kg) |
| **Alcance horizontal máx.**| 1.422 mm | 810 mm | 400 mm |
| **Alcance vertical máx.** | 2.486 mm | 1.243 mm (aprox.) | 150 mm (Carrera del eje Z) |
| **Repetibilidad** | ±0,08 mm | ±0,03 mm | ±0,02 mm |
| **Peso del manipulador** | ~130 kg | 98 kg | 16 kg |
| **Controlador** | DX100 | IRC5 | Integrado en la base |
| **Montajes disponibles** | Suelo, pared, techo | Suelo, invertido, cualquier ángulo | Mesa / Suelo |
| **Temperatura de operación**| 0°C a +45°C | +5°C a +45°C | +5°C a +40°C |
| **Nivel de protección** | IP54 | IP67 (todas las variantes) | IP20 (Estándar) |
| **Consumo de potencia** | 1.5 kVA (promedio) | 0.44 kW a 1000 mm/s | 0.48 kVA |
| **Software de programación**| MotoSim EG, RoboDK | RobotStudio, RoboDK | EPSON RC+ 7.0 |
| **Lenguaje de programación**| INFORM III / Python | RAPID | SPEL+ |

</div>
<br>

**Análisis:** La diferencia de escala es evidente. El Motoman MH6 posee un volumen de trabajo significativamente mayor, logrando casi el doble de alcance vertical y horizontal frente al IRB140, sacrificando a cambio un poco de precisión (repetibilidad de ±0.08mm vs ±0.03mm) y requiriendo un chasis más pesado (130 kg). El detalle más importante radica en los **dos ejes externos adicionales** que posee la celda del Motoman, otorgando un nivel de configuración superior para posicionadores o rieles en comparación al estándar de 6 ejes del ABB.

### 2.2 Rangos de Movimiento por Eje

<br>
<div align="center">

| Eje | Motoman MH6 | ABB IRB140 | EPSON T3-401S (SCARA) |
| :--- | :--- | :--- | :--- |
| **Eje 1 (S / Giro base)** | ±170° | +180° / -180° | ±132° (Articulación 1) |
| **Eje 2 (L / Brazo inf.)** | +155° / -90° | +110° / -90° | ±141° (Articulación 2) |
| **Eje 3 (U / Brazo sup.)** | +250° / -175° | +50° / -230° | **150 mm** (Desplazamiento Z) |
| **Eje 4 (R / Rot. muñeca)**| ±180° | ±200° (por defecto) | ±360° (Giro de muñeca - U) |
| **Eje 5 (B / Pitch-Yaw)** | +225° / -45° | ±115° | *No aplica* |
| **Eje 6 (T / Giro muñeca)**| ±360° | ±400° (por defecto) | *No aplica* |

</div>
<br>

**Análisis:** Es interesante notar cómo se distribuyen los movimientos. El Motoman MH6 presenta un rango excepcionalmente amplio en el Eje 3 (+250°/-175°) permitiéndole articularse sobre sí mismo de maneras muy flexibles para alcanzar puntos lejanos, mientras que el ABB IRB140 compensa con mayor rotación en la muñeca (Ejes 4 y 6), facilitando maniobras ágiles con la herramienta en espacios reducidos sin riesgo de enredos.

### 2.3 Velocidades Máximas por Eje

<br>
<div align="center">

| Eje | Motoman MH6 | ABB IRB140 | EPSON T3-401S |
| :--- | :--- | :--- | :--- |
| **Eje 1** | 220 °/s | 200 °/s | ~3700 mm/s (Combinada J1 + J2) |
| **Eje 2** | 200 °/s | 200 °/s | ~3700 mm/s (Combinada J1 + J2) |
| **Eje 3** | 220 °/s | 260 °/s | ~1100 mm/s (Lineal) |
| **Eje 4** | 410 °/s | 360 °/s | ~2600 °/s (Rotacional) |
| **Eje 5** | 410 °/s | 360 °/s | *No aplica* |
| **Eje 6** | 610 °/s | 450 °/s | *No aplica* |

</div>
<br>

**Análisis:** En términos de dinámica, el Motoman MH6 es notablemente más rápido en su muñeca (Ejes 4, 5 y 6), alcanzando hasta 610 °/s en el giro de la herramienta. Esto lo hace muy eficiente para reposicionamientos constantes en procesos como soldadura. El ABB IRB140, por su parte, mantiene un equilibrio en todo el brazo, destacando en la velocidad del Eje 3 (260 °/s).

### 2.4 Aplicaciones Típicas

<br>
<div align="center">

| Motoman MH6 | ABB IRB140 | EPSON T3-401S |
| :--- | :--- | :--- |
| Soldadura por arco y láser | Soldadura por arco | *Pick and Place* a alta velocidad |
| Ensamble general | Ensamble de piezas | Ensamble de electrónica (PCBs) |
| Paletizado y empaque | Mecanizado ligero | Dispensado de pegamento / resinas |
| Alimentación de máquinas CNC | Manipulación de piezas | Empaque de componentes pequeños |
| Manejo de materiales | Dispensado / pegamento | Inspección óptica automatizada |
| Aplicaciones multipropósito | Sala limpia (variante *Clean Room*) | Alimentación de cintas / *Kitting* |
| — | Fundición (variante *Foundry Plus 2*) | — |

</div>
<br>

**Análisis final:** Al observar las aplicaciones ideales, el ABB IRB140 sobresale en entornos que demandan control minucioso o ambientes especializados (Clean Room, Foundry), donde su alta precisión y tamaño compacto son vitales. En contraposición, la robustez, el extenso alcance y la velocidad en la muñeca del Motoman MH6 lo consolidan como un manipulador versátil (multipropósito) ideal para operaciones a mayor escala como paletizado, empaque y soldadura láser.



