<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h1>Laboratorio No. 02 - Robótica Industrial - Análisis y Operación del Manipulador Motoman MH6.</h1>
<h2>Profesores: <br>Pedro Fabián Cárdenas Herrera <br> Manuel Felipe Carranza Montenegro</h2>

<br>
<img src="Images/motoman.png" alt="Motoman" style="border-radius: 5px; width: 200px;"><br>
<b>Figura 1. Manipulador Motoman MH6.</b>
</div>

---

## 1. Introducción

La evolución de la manufactura moderna exige una integración cada vez más profunda de los sistemas robóticos, donde la versatilidad y el conocimiento de múltiples marcas se han convertido en competencias indispensables en la ingeniería. En este contexto, el presente informe documenta el desarrollo del Laboratorio No. 02 del curso de Robótica Industrial, cuyo objetivo principal es la inmersión en la arquitectura, operación y programación del brazo antropomórfico Yaskawa Motoman MH6. Como punto de partida, se establecerá una comparación técnica entre las capacidades de este equipo y las del manipulador ABB IRB 140 (utilizado durante el laboratorio anterior), permitiendo dimensionar las fortalezas y aplicaciones ideales de cada plataforma.

Para operar el Motoman MH6 de manera eficiente y segura dentro de la celda de trabajo, resulta imperativo comprender la disposición de sus articulaciones y sus configuraciones cinemáticas de referencia. Por ello, se analizará en detalle la distinción técnica y funcional entre sus posiciones *Home 1* (posición de transporte) y *Home 2* (cero mecánico de calibración). Asimismo, el desarrollo de la práctica abarca el dominio de la interfaz física de control, el *Teach Pendant*. A través de este dispositivo, se exploran los procedimientos estandarizados para realizar movimientos manuales (jogging), la navegación entre sistemas de coordenadas (articulares y cartesianos), y la correcta gestión de los niveles de velocidad, parámetros críticos para garantizar la integridad tanto del equipo físico como de los operadores durante la puesta a punto.

Más allá de la manipulación manual, la automatización industrial contemporánea depende de herramientas de software robustas que cierren la brecha entre el diseño de trayectorias y la ejecución en planta. En consecuencia, este documento introduce el uso de RoboDK, una potente plataforma de simulación y Programación Fuera de Línea (OLP) que destaca por su compatibilidad con múltiples fabricantes. Se abordarán sus aplicaciones fundamentales, su método de comunicación en línea con el controlador del Motoman, y se presentará un análisis crítico de sus ventajas, limitaciones y enfoques de uso frente a entornos de simulación propietarios y de alta fidelidad como RobotStudio.

Finalmente, como culminación práctica de la sesión, se expone el diseño, simulación y ejecución de una trayectoria paramétrica polar. Este ejercicio no solo valida los conceptos de cinemática inversa y generación de trayectorias, sino que demuestra la viabilidad técnica de comandar movimientos complejos desde un computador hacia el manipulador Motoman MH6 real, consolidando un flujo de trabajo integral desde la estructuración del código en RoboDK hasta la respuesta física del robot en el laboratorio.

## 2. Comparación: Motoman MH6 vs ABB IRB140

En el desarrollo de competencias en robótica industrial, es esencial familiarizarse con diversas arquitecturas y entornos de programación. Puesto que a lo largo del curso se han utilizado los manipuladores ABB IRB140 y Yaskawa Motoman MH6, resulta importante identificar las diferencias entre ambos. A pesar de que los dos equipos pertenecen a la clasificación de brazos de estructura articulada vertical y comparten una carga útil máxima de 6 kg, presentan variaciones técnicas significativas en sus dimensiones, velocidades y rangos de movimiento, tal como se detalla a continuación en el siguiente análisis estructurado:

### 2.1 Datos Generales y Alcance

<br>
<div align="center">

| Característica | **Motoman MH6** | **ABB IRB140** |
| :--- | :--- | :--- |
| **Fabricante** | Yaskawa Motoman | ABB |
| **Modelo Oficial** | YR-MH00006-A00 | IRB 140-6/0.8 |
| **Estructura** | Articulada vertical | Articulada vertical |
| **Grados de libertad (ejes)** | **6 + 2 externos** | 6 |
| **Carga máxima (payload)** | 6 kg | 6 kg |
| **Alcance horizontal máximo** | 1.422 mm | 810 mm |
| **Alcance vertical máximo** | 2.486 mm | 1.243 mm (aprox.) |
| **Repetibilidad (ISO)** | ±0,08 mm | ±0,03 mm |
| **Peso del manipulador** | ~130 kg | 98 kg |
| **Controlador** | DX100 | IRC5 |
| **Montajes disponibles** | Suelo, pared, techo | Suelo, invertido, pared (cualquier ángulo) |
| **Temperatura de operación** | 0°C a +45°C | +5°C a +45°C |
| **Humedad relativa máxima** | 95% | 95% (temperatura constante) |
| **Nivel de protección** | IP54 | IP67 (todas las variantes) |
| **Consumo de potencia** | 1.5 kVA (promedio) | 0.44 kW a 1000 mm/s |
| **Aceleración máx. TCP** | No especificada por fabricante | 57 m/s² |
| **Software de programación** | MotoSim EG, RoboDK | RobotStudio, RoboDK |
| **Lenguaje de programación** | INFORM III / Python | RAPID |

</div>
<br>

**Análisis:** La diferencia de escala es evidente. El Motoman MH6 posee un volumen de trabajo significativamente mayor, logrando casi el doble de alcance vertical y horizontal frente al IRB140, sacrificando a cambio un poco de precisión (repetibilidad de ±0.08mm vs ±0.03mm) y requiriendo un chasis más pesado (130 kg). El detalle más importante radica en los **dos ejes externos adicionales** que posee la celda del Motoman, otorgando un nivel de configuración superior para posicionadores o rieles en comparación al estándar de 6 ejes del ABB.

### 2.2 Rangos de Movimiento por Eje

<br>
<div align="center">

| Eje | **Motoman MH6** | **ABB IRB140** |
| :--- | :--- | :--- |
| **Eje 1 (S - giro base)** | ±170° | +180° / -180° |
| **Eje 2 (L - brazo inferior)**| +155° / -90° | +110° / -90° |
| **Eje 3 (U - brazo superior)**| +250° / -175° | +50° / -230° |
| **Eje 4 (R - rotación muñeca)**| ±180° | ±200° (por defecto) |
| **Eje 5 (B - pitch/yaw)** | +225° / -45° | ±115° |
| **Eje 6 (T - giro muñeca)** | ±360° | ±400° (por defecto) |

</div>
<br>

**Análisis:** Es interesante notar cómo se distribuyen los movimientos. El Motoman MH6 presenta un rango excepcionalmente amplio en el Eje 3 (+250°/-175°) permitiéndole articularse sobre sí mismo de maneras muy flexibles para alcanzar puntos lejanos, mientras que el ABB IRB140 compensa con mayor rotación en la muñeca (Ejes 4 y 6), facilitando maniobras ágiles con la herramienta en espacios reducidos sin riesgo de enredos.

### 2.3 Velocidades Máximas por Eje

<br>
<div align="center">

| Eje | **Motoman MH6** | **ABB IRB140** |
| :--- | :--- | :--- |
| **Eje 1 (S)** | 220 °/s | 200 °/s |
| **Eje 2 (L)** | 200 °/s | 200 °/s |
| **Eje 3 (U)** | 220 °/s | 260 °/s |
| **Eje 4 (R)** | 410 °/s | 360 °/s |
| **Eje 5 (B)** | 410 °/s | 360 °/s |
| **Eje 6 (T)** | 610 °/s | 450 °/s |

</div>
<br>

**Análisis:** En términos de dinámica, el Motoman MH6 es notablemente más rápido en su muñeca (Ejes 4, 5 y 6), alcanzando hasta 610 °/s en el giro de la herramienta. Esto lo hace muy eficiente para reposicionamientos constantes en procesos como soldadura. El ABB IRB140, por su parte, mantiene un equilibrio en todo el brazo, destacando en la velocidad del Eje 3 (260 °/s).

### 2.4 Aplicaciones Típicas

<br>
<div align="center">

| **Motoman MH6** | **ABB IRB140** |
| :--- | :--- |
| Soldadura por arco y láser | Soldadura por arco |
| Ensamble | Ensamble de piezas |
| Paletizado y empaque | Mecanizado |
| Alimentación de máquinas CNC | Manipulación de piezas |
| Manejo de materiales | Dispensado / pegamento |
| Aplicaciones multipropósito | Sala limpia (variante Clean Room, ISO clase 6) |
| — | Fundición (variante Foundry Plus 2) |

</div>
<br>

**Análisis final:** Al observar las aplicaciones ideales, el ABB IRB140 sobresale en entornos que demandan control minucioso o ambientes especializados (Clean Room, Foundry), donde su alta precisión y tamaño compacto son vitales. En contraposición, la robustez, el extenso alcance y la velocidad en la muñeca del Motoman MH6 lo consolidan como un manipulador versátil (multipropósito) ideal para operaciones a mayor escala como paletizado, empaque y soldadura láser.

## 3. Configuraciones Home 1 y Home 2 del Motoman MH6

En la programación y operación del manipulador Motoman MH6, se establecen dos posiciones de referencia predefinidas en el controlador denominadas *Home 1* y *Home 2*. Estas configuraciones articulares son fundamentales dentro de la celda de manufactura, ya que sirven como puntos seguros de inicio, finalización de rutinas y verificación del sistema de coordenadas. A continuación, se detalla la posición de cada articulación en ambas configuraciones para dar cumplimiento a los requerimientos del análisis.

### 3.1 Home 1 (Work Home Position)

La posición *Home 1* constituye la postura base o neutral de trabajo del manipulador. Físicamente, en esta configuración el eje U (Upper Arm) se orienta de forma horizontal y el eje R (Rotate) queda en posición vertical, resultando en una postura de reposo característica. Esta configuración facilita las verificaciones rápidas del área de trabajo, minimiza los torques estáticos sobre los actuadores y es comúnmente empleada como posición de inicio (*Origin*) en aplicaciones de embalaje y manipulación de materiales.

Los valores de las articulaciones para esta posición, expresados en pulsos de encoder tal como los reporta el controlador DX100, son los siguientes:

<br>
<div align="center">

| Articulación | Denominación | Origen (pulsos) | Actual (pulsos) |
| :---: | :---: | :---: | :---: |
| **Eje 1** | S (Shoulder/Base) | 0 | 1 |
| **Eje 2** | L (Lower Arm) | −115.290 | −115.291 |
| **Eje 3** | U (Upper Arm) | −111.622 | −111.621 |
| **Eje 4** | R (Rotate) | 1 | 0 |
| **Eje 5** | B (Bend) | 42.647 | 42.646 |
| **Eje 6** | T (Twist/Tool) | 1.392 | 1.393 |

</div>
<br>

> *Nota técnica: La columna "Actual" corresponde a la posición medida en el Teach Pendant al momento de la captura. La desviación máxima respecto al origen es de apenas ±1 pulso, lo que confirma una excelente calibración del sistema.*

<br>
<div align="center">
  <table>
    <tr>
      <td align="center">
        <img src="Images/home1.png" alt="Home 1 físico" width="350"><br>
        <b>Figura 2. Vista física del manipulador en configuración Home 1.</b>
      </td>
      <td align="center">
        <img src="Images/tp_home1.jpg" alt="Teach Pendant Home 1" width="350"><br>
        <b>Figura 3. Interfaz del Teach Pendant mostrando los pulsos para Home 1 (Work Home Position).</b>
      </td>
    </tr>
  </table>
</div>
<br>

### 3.2 Home 2 (Second Home Position)

La posición *Home 2* corresponde a una postura elevada y retraída. En esta configuración, el brazo adopta una geometría casi extendida verticalmente, lo cual proporciona un amplio espacio de acceso a los elementos mecánicos del robot. Por su naturaleza compacta frente al entorno, está diseñada específicamente para operaciones de mantenimiento, cambio seguro de efectores finales (herramientas) y para el tránsito libre del manipulador reduciendo el riesgo de colisiones.

Los valores de las articulaciones para esta posición, que se acercan considerablemente al cero mecánico en los ejes principales, son los siguientes:

<br>
<div align="center">

| Articulación | Denominación | Especificado (pulsos) | Actual (pulsos) | Diferencia |
| :---: | :---: | :---: | :---: | :---: |
| **Eje 1** | S (Shoulder/Base) | 0 | 0 | 0 |
| **Eje 2** | L (Lower Arm) | 2.037 | 2.037 | 0 |
| **Eje 3** | U (Upper Arm) | 2.359 | 2.359 | 0 |
| **Eje 4** | R (Rotate) | 0 | 0 | 0 |
| **Eje 5** | B (Bend) | −121 | −121 | 0 |
| **Eje 6** | T (Twist/Tool) | 1.392 | 1.392 | 0 |

</div>
<br>

> *Nota técnica: Los valores de los ejes L, U y B próximos a cero evidencian la postura extendida. La diferencia nula (0) en todas las articulaciones demuestra que el robot alcanzó exactamente el target especificado en la memoria del controlador.*

<br>
<div align="center">
  <table>
    <tr>
      <td align="center">
        <img src="Images/home2.png" alt="Home 2 físico" width="350"><br>
        <b>Figura 4. Vista física del manipulador en configuración Home 2 (Postura de mantenimiento).</b>
      </td>
      <td align="center">
        <img src="Images/tp_home2.png" alt="Teach Pendant Home 2" width="350"><br>
        <b>Figura 5. Interfaz del Teach Pendant mostrando los pulsos para Home 2 (Second Home Position).</b>
      </td>
    </tr>
  </table>
</div>
<br>

### 3.3 Análisis: ¿Cuál posición es óptima?

Respondiendo a la interrogante sobre cuál de las dos configuraciones es mejor, la respuesta depende estrictamente del escenario de uso, sin embargo, desde el punto de vista operativo estándar, **el Home 1 representa la posición óptima** como referencia habitual para la celda de manufactura. 

Esta elección se fundamenta técnica y mecánicamente en los siguientes criterios:

1. **Minimización de Torques Gravitacionales:** Al mantener el antebrazo horizontal y el brazo compensado, la postura del *Home 1* distribuye de forma equilibrada las cargas gravitacionales sobre los actuadores. Esto reduce drásticamente el torque estático que deben soportar los frenos eléctricos de los motores mientras el robot está inactivo, prolongando su vida útil.
2. **Referencia de Calibración:** Al estar predefinida como el origen de trabajo, facilita la verificación visual y de software de la calibración del robot, permitiendo detectar de forma temprana desvíos en la repetibilidad antes de iniciar la producción.
3. **Coherencia de Ciclo:** La programación industrial exige puntos de inicio y retorno predecibles. El *Home 1* sitúa la herramienta en una zona media de fácil acercamiento a las mesas de trabajo, optimizando los tiempos de ciclo.

Por el contrario, el **Home 2 no es ideal para el reposo continuo**, sino que debe reservarse exclusivamente como una posición transitoria de servicio (mantenimiento preventivo, ajuste de herramental o resguardo en caso de ingreso de personal a la celda), donde su postura elevada garantiza la seguridad espacial.

## 4. Movimientos Manuales, modos, traslaciones y rotaciones 

Para manipular manualmente el robot Motoman MH6, es necesario hace uso del Teach Pendant. 



<br>
<div align="center">
  <img src="Images/teach.png" alt="teach" style="border-radius: 5px; width: 200px;">
  <br>
  <b>Figura 4. Teach Pendant.</b>
</div>
<br>


El procedimiento estándar para habilitar el movimiento y navegar entre los distintos modos de operación es el siguiente:
### 4.1. Posicionar la llave en modo manual (Modo TEACH)

El *Teach Pendant* cuenta con un selector de modo físico que dispone de tres posiciones distintas, las cuales determinan el tipo de control sobre el manipulador:

* **Posición TEACH (Manual):** Activa el modo de operación manual. Permite mover el brazo robótico directamente desde el *Teach Pendant*, ya sea mediante movimientos articulares o cartesianos. Por medidas de seguridad, la velocidad de los motores se encuentra limitada en este modo.

* **Posición PLAY (Automático):** Permite al robot ejecutar de forma continua y automática los programas previamente guardados en su memoria.

* **Posición REMOTE (Remoto):** Se utiliza para cederle el control del robot a un dispositivo externo al *Teach Pendant*. Generalmente, se trata de un equipo maestro conectado a través de la red, como lo es un computador operando con el software **RoboDK**.


### 4.2. Desactivar la parada de emergencia
Antes de iniciar cualquier operación, se debe verificar y desactivar el botón de parada de emergencia (E-Stop), el cual se encuentra ubicado en la parte superior derecha del *Teach Pendant* (generalmente se desactiva girándolo levemente en el sentido de las flechas).

### 4.3. Habilitar la potencia de los motores (SERVO ON)
Posterior a la selección del modo *Teach* y la liberación del paro de emergencia, se procede a energizar los motores. Para esto, el operador debe presionar a la mitad el interruptor de hombre muerto (*Deadman switch*), ubicado en la parte posterior del *Teach Pendant* y, sin soltarlo, presionar el botón **[SERVO ON]**. 

### 4.4. Realizar el movimiento
Una vez que el robot tiene potencia, se utiliza el botón **[COORD]** para alternar entre los distintos modos de movimiento y las teclas de los ejes para desplazar el manipulador. Los modos principales son:

* **Movimiento articular (Joint):** Permite mover el robot controlando el ángulo de cada una de sus articulaciones (S, L, U, R, B, T) de forma independiente.
* **Movimiento cartesiano con respecto a la base (World/Base):** Realiza desplazamientos lineales y rotaciones tomando como referencia el sistema de coordenadas absoluto situado en la base del robot (no afecta a los ejes externos).
* **Movimiento cartesiano con respecto a la herramienta (Tool):** Realiza desplazamientos y rotaciones tomando como referencia el sistema de coordenadas del Punto Central de la Herramienta (TCP) seleccionada, facilitando aproximaciones precisas en la dirección en la que apunta el efector final.

<br>
<div align="center">
  <img src="Images/teachp2.png" alt="botn teach" style="border-radius: 5px; width: 200px;">
  <br>
  <b>Figura 5. Botones de Teach Pendant.</b>
</div>
<br>

## 5. Niveles de Velocidad para Movimientos Manuales

Durante la operación manual del manipulador Motoman MH6 desde el *Teach Pendant*, es fundamental controlar la velocidad de los movimientos por razones de precisión y seguridad. A continuación, se detallan sus características:

**5.1. Niveles de velocidad**
El sistema cuenta con cuatro niveles de velocidad preestablecidos para el movimiento manual (jogging), los cuales limitan la velocidad máxima a la que se moverán los motores al presionar las teclas de los ejes:
* **INCH (Micromovimiento / Paso a paso):** Es la velocidad más baja. El robot se mueve una distancia minúscula por cada pulsación de la tecla, independientemente de cuánto tiempo se mantenga presionada. Es ideal para aproximaciones muy finas y calibración.
* **SLOW (Lenta):** Movimiento continuo pero a una velocidad muy reducida. Útil para acercarse a la pieza de trabajo de forma segura.
* **MED (Media):** Velocidad moderada, utilizada para desplazamientos generales dentro de la celda de trabajo de forma controlada.
* **FAST (Rápida):** Es la velocidad máxima permitida en el modo manual (limitada por seguridad frente a la velocidad real del modo automático). Se usa para realizar desplazamientos largos y rápidos en zonas libres de obstáculos.

Para cambiar entre velocidades se hace uso de los botones de [SLOW] y [FAST] del teach pendant, además del de [HIGH SPEED], ubicados entre los botones para mover el manipulador, y se muestra en la pantalla la velocidad a la que está operando mediante un indicador de barras en la parte superior.


## 6. Software RoboDK – Aplicaciones y Comunicación

RoboDK es un potente software de simulación y programación de robots industriales diseñado para ser compatible con más de 40 fabricantes de robots incluyendo Yaskawa/Motoman, ABB, KUKA, Universal Robots, entre otros. Sus aplicaciones principales en el entorno industrial y académico incluyen:

- Programación Fuera de Línea (Offline Programming - OLP): Permite diseñar, simular y programar el robot en un entorno virtual tridimensional sin necesidad de detener la máquina física en la línea de producción.

- Simulación de trayectorias y validación: Sirve para verificar alcances, detectar posibles colisiones entre el brazo, la herramienta y el entorno, y evitar singularidades articulares antes de ejecutar el movimiento real.

- Manufactura avanzada: Es ampliamente utilizado para generar trayectorias complejas a partir de modelos CAD/CAM para aplicaciones como fresado robótico, impresión 3D, soldadura, pintura, y operaciones de pick and place (recoger y colocar).

Para que el manipulador físico realice los movimientos simulados, RoboDK actúa como un "traductor". A nivel interno, el software calcula la cinemática inversa para transformar las coordenadas cartesianas y trayectorias del entorno virtual en los ángulos específicos requeridos por las articulaciones del robot.

El elemento clave que hace posible el movimiento es el Post-procesador. Dado que cada fabricante de robots tiene su propio lenguaje de programación (por ejemplo, código INFORM para Yaskawa/Motoman o RAPID para ABB), el post-procesador de RoboDK toma las instrucciones genéricas de la simulación y genera automáticamente un archivo de código en el lenguaje nativo del controlador del robot específico que se está utilizando.

La comunicación entre el software en el PC y el controlador del robot se puede realizar mediante dos métodos principales:

 **Comunicación Fuera de Línea (Generación de programas):** RoboDK compila el programa a través de su post-procesador y genera un archivo de código. Este archivo se transfiere al controlador del manipulador de forma manual mediante una memoria USB o a través de una red local usando un protocolo FTP. Una vez cargado, el robot lo ejecuta de forma autónoma.

 **Comunicación en Línea (Robot Drivers / Modo Online):** Este es el método utilizado durante el desarrollo del laboratoio en donde se controlar el robot en tiempo real desde el PC. El computador se conecta a la red donde esta conectado físicamente el controlador del robot mediante un cable de red (Ethernet) utilizando protocolos TCP/IP. Para que esto funcione, la llave del *Teach Pendant* debe estar en la posición **REMOTE**. A través de sus "Drivers", RoboDK establece una conexión servidor-cliente y envía las instrucciones de la trayectoria (como la polar) paso a paso directamente a los motores, sin necesidad de descargar un archivo completo en la memoria del robot.

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
from robodk.robolink import *
from robodk.robomath import *
import math
from matplotlib.textpath import TextPath

# Inicialización de la API de RoboDK
RDK = Robolink()

# Elegir un robot (si hay varios, aparece un popup)
robot = RDK.ItemUserPick("Selecciona un robot", ITEM_TYPE_ROBOT)
if not robot.Valid():
    raise Exception("No se ha seleccionado un robot válido.")

"""# Conectar al robot físico
if not robot.Connect():
    raise Exception("No se pudo conectar al robot. Verifica que esté en modo remoto y que la configuración sea correcta.")

# Confirmar conexión
if not robot.ConnectedState():
    raise Exception("El robot no está conectado correctamente. Revisa la conexión.")

print("Robot conectado correctamente.")"""

frame = RDK.Item("Frame_from_Target1", ITEM_TYPE_FRAME)
if not frame.Valid():
    raise Exception("No se encontró el Frame de referencia.")

robot.setPoseFrame(frame)
# robot.setPoseTool(robot.PoseTool())
robot.setSpeed(300)
robot.setRounding(5)

# Parámetros de la figura
A = 150             # Radio máximo del lóbulo (Ajustado para mantener el tamaño original)
z_surface, z_safe = 0, 50            
puntos_curva = 180  # 180 puntos por lóbulo es suficiente para un trazo suave

# --- Posicionamiento inicial OBLIGATORIO ---
target_home = RDK.Item("Target_Home", ITEM_TYPE_TARGET) 
if not target_home.Valid():
    raise Exception("CRÍTICO: No se encontró 'Target_Home' en la estación. Es obligatorio para iniciar y terminar.")

robot.MoveJ(target_home)

def dibujar_lobulo(angulo_central):
    """
    Dibuja un lóbulo basado en la ecuación polar r = A * cos(2 * theta).
    El lóbulo apuntará en la dirección del 'angulo_central'.
    """
    # Un lóbulo completo se traza en un rango de pi/2 (-pi/4 a pi/4 respecto al centro)
    theta_i = angulo_central - math.pi / 4
    theta_f = angulo_central + math.pi / 4

    # Los lóbulos de esta ecuación siempre nacen en el origen (0,0)
    x_i, y_i = 0, 0
    
    robot.MoveJ(transl(x_i, y_i, z_surface - z_safe))
    robot.MoveL(transl(x_i, y_i, z_surface))
    
    for i in range(puntos_curva + 1):
        theta = theta_i + (i / puntos_curva) * (theta_f - theta_i)
        
        # Ecuación polar para el lóbulo
        r = A * math.cos(2 * (theta - angulo_central))
        
        # Filtro de seguridad: evita r negativo por precisión de decimales en los extremos
        r = max(0, r) 
        
        x = r * math.cos(theta)
        y = r * math.sin(theta)
        robot.MoveL(transl(x, y, z_surface))
        
    # Elevar la herramienta al terminar el lóbulo (de vuelta en el origen)
    robot.MoveL(transl(0, 0, z_surface - z_safe))

def dibujar_texto_transformado(texto, cx, cy, longitud):
    path = TextPath((0, 0), texto, size=1)
    bbox = path.get_extents()
    tw, th = bbox.x1 - bbox.x0, bbox.y1 - bbox.y0
    escala = longitud / tw
    
    def transformar(px, py):
        # Centrado local y aplicación de transformaciones (90° + Espejos)
        lx, ly = px - (bbox.x0 + tw / 2), py - (bbox.y0 + th / 2)
        rx, ry = ly, lx 
        return rx * escala + cx, ry * escala + cy

    for trazo in path.to_polygons():
        x0, y0 = transformar(trazo[0][0], trazo[0][1])
        robot.MoveJ(transl(x0, y0, z_surface - z_safe))
        robot.MoveL(transl(x0, y0, z_surface))
        
        for pt in trazo[1:]:
            x, y = transformar(pt[0], pt[1])
            robot.MoveL(transl(x, y, z_surface))
            
        robot.MoveL(transl(x, y, z_surface - z_safe))

# --- Ejecución de la rutina ---

# 1. Dibujar Figura Polar (Dos lóbulos independientes)
dibujar_lobulo(0)            # Dibuja el lóbulo hacia la derecha (0 radianes)
dibujar_lobulo(math.pi / 2)  # Dibuja el lóbulo hacia arriba (pi/2 radianes)

# 2. Dibujar Texto (Letras extra grandes: longitud=400, Ubicado más abajo: CX=-120)
dibujar_texto_transformado("David, Daniel, David", -120, 0, 650)

# --- Retorno a Home OBLIGATORIO ---
robot.MoveJ(target_home)

print("Trayectoria completada. El robot inició y regresó a Home exitosamente.")

```

## 11. Demostración en Video

Para evidenciar el correcto funcionamiento de la arquitectura de control diseñada, se ha documentado la ejecución de la celda de manufactura en video. 

Haga clic en el siguiente enlace para ver el video completo de la implementación simulada y física:

<br>

<div align="center">
  <a href="youtube.com" target="_blank">
    <img src="https://img.shields.io/badge/Ver_Video_en_YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white" alt="Ver Video" />
  </a>
</div>





