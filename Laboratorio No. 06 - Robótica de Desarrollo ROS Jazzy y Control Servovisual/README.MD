# 4. Diseño del gripper/herramientas: planos + fotos + justificación.


[![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Montado.jpg)](/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/blob/main/images/Gripper_Montado.jpg)  
**Figura. Herramienta (electroimán) montada en el efector final del robot ABB IRB 140.**

Para la manipulación de los componentes electrónicos (resistencias, capacitores, conectores y circuitos integrados) se seleccionó como efector final un **electroimán**. Esta decisión se justifica principalmente porque muchos de estos componentes cuentan con terminales o encapsulados metálicos capaces de ser atraídos por un campo magnético, lo que permite un pick confiable sin necesidad de una ventosa ni de un sistema neumático de vacío adicional. Además, el electroimán se controla mediante una única salida digital binaria (activado/desactivado), lo cual simplifica enormemente la lógica de control: en el programa RAPID de la Etapa 1, la señal `DO_06` es la encargada de activar y liberar la pieza (`Reset DO_06` para sujetar, `Set DO_06` para soltar), sin requerir variables ni rutinas adicionales de control de fuerza o presión. Esto lo hace una solución compacta, ligera y de bajo costo de integración, adecuada para el tamaño y peso reducido de los componentes que se recogen desde la banda y se depositan en las cajas del almacén.

## Plano de la herramienta

[![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Plano_Gripper.png)](/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/blob/main/images/Plano_Gripper.png)  
**Figura. Plano de la herramienta electroimán, con vista frontal (patrón de sujeción a la brida, R28.48 mm de radio de ubicación de tornillos, R3 mm de redondeo), vista lateral (espesor de 10 mm y longitud total de 73.24 mm) y vista isométrica del conjunto.**

El plano recoge las cotas verificadas físicamente con calibrador (26.33 mm de ancho, 31.74 mm de profundidad, 73.24 mm de longitud total), y define el patrón circular de 4 tornillos con el que la herramienta se acopla a la brida del robot. Todas las medidas están en milímetros. El plano completo en formato PDF puede consultarse en [`Plano_Gripper.pdf`](./Anexos/Plano_Gripper.pdf).

## Modelado 3D (aproximación para RobotStudio)

Para poder representar la herramienta dentro del entorno de simulación e integrarla al `tooldata` (`MyNewToolEI`) definido en RAPID, se realizó un modelado 3D aproximado en Autodesk Inventor a partir de las medidas tomadas físicamente sobre la pieza real. Este modelo no busca ser una réplica exacta del electroimán comercial, sino una aproximación geométrica suficiente para representar correctamente el volumen, la orientación y el punto central de herramienta (TCP) al importarlo a RobotStudio.

| ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Inventor_1.png) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Inventor_2.png) |
|---|---|

**Figura. Modelado de la herramienta en Autodesk Inventor (`ElectroImanUnido.ipt`): vista general del conjunto y vista lateral del acople a la brida, usadas como base para la aproximación geométrica cargada en RobotStudio.**

## Montaje sobre el efector final

La herramienta se fija a la brida del robot mediante un adaptador circular atornillado, que garantiza una posición y orientación repetibles cada vez que se instala. La marca "1" y las referencias en "X" grabadas sobre el cuerpo de la herramienta se utilizaron como guía de alineación durante el montaje, asegurando que la orientación del electroimán respecto al TCP definido en el programa coincidiera con la orientación real de la pieza física.

| ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Detalle_1.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Detalle_2.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Detalle_3.jpg) |
|---|---|---|

**Figura. Detalle del montaje de la herramienta sobre la brida del robot, incluyendo las marcas de referencia "1" y "X" usadas para garantizar la orientación correcta.**

## Verificación dimensional

Antes de definir el `tooldata` en el programa, se verificaron con calibrador digital las dimensiones reales de la herramienta y del adaptador de brida, con el fin de que el TCP declarado en RAPID (offset `[0.05,0,41.6]`) correspondiera a las medidas físicas del conjunto.

| ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_1.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_2.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_3.jpg) |
|---|---|---|

**Figura. Verificación con calibrador digital del diámetro del adaptador de brida (56.96 mm), el espesor del cuerpo de la herramienta (4.27 mm) y la longitud total del conjunto ensamblado (73.24 mm).**

Estas medidas se contrastaron adicionalmente desde otros ejes para confirmar la geometría completa de la herramienta antes de su puesta en funcionamiento.

| ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_4.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_5.jpg) | ![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Medida_6.jpg) |
|---|---|---|

**Figura. Medición del ancho del cuerpo de la herramienta (31.74 mm), de la altura de referencia junto a las marcas de alineación (26.33 mm) y de una cota de control adicional sobre el adaptador de brida.**

## Tornillería utilizada

La sujeción del conjunto se realiza en dos etapas, cada una con un tornillo de diámetro distinto:

- **Cuerpo del electroimán al adaptador de brida:** tornillos Allen de cabeza cilíndrica con diámetro de rosca medido en aproximadamente 4.27 mm, valor que corresponde de forma cercana a la referencia estándar **M4** (DIN 912), cuyo diámetro nominal de rosca es de 4.0 mm; la diferencia respecto a la medida obtenida es atribuible a la tolerancia normal de fabricación y al margen de error del calibrador al medir sobre la cabeza del tornillo.
- **Adaptador de brida al robot ABB IRB 140:** tornillos **M6**, dispuestos en un patrón circular de 4 puntos (radio R28.48 mm), consistentes con la interfaz de brida estándar ISO 9409-1 utilizada por el manipulador.

[![](https://github.com/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/raw/main/images/Gripper_Final.jpg)](/david-pi3141/Proyecto-Final-Robotica-Industrial-Automatizacion-del-Proceso-de-Ensamblaje/blob/main/images/Gripper_Final.jpg)  
**Figura. Herramienta final ensamblada, lista para su instalación en el robot.**
