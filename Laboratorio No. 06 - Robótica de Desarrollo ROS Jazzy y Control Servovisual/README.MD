# 6. Código fuente comentado: RAPID / Python / SPEL+.

Código fuente utilizado para el desarrollo de la práctica. El programa fue escrito en RAPID y se ejecuta en el controlador IRC5 del robot ABB IRB 140 "Caín". Está organizado en un único módulo (`Module1`) que agrupa la declaración de variables y un conjunto de procedimientos, cada uno responsable de una parte concreta del proceso de recepción, clasificación y almacenamiento de componentes.

El archivo completo y comentado puede consultarse en [`Module1_Proyecto_Etapa1.txt`](./Anexos/Module1_Proyecto_Etapa1.txt). A continuación se describen las partes más relevantes del código junto con los fragmentos correspondientes.

## Variables de posición, herramienta y sistemas de coordenadas

El módulo define un conjunto de `robtarget` que representan los puntos de paso del robot en dos zonas de trabajo: la zona de la banda transportadora (donde ocurre el Pick) y la zona del almacén (donde ocurre el Place). Cada celda del almacén está representada por un par de posiciones: una de aproximación y una de depósito.

```
CONST robtarget Target_36:=[[30,30,-50],[1,0,0,0],[-2,0,-1,0],[9E+09,9E+09,9E+09,9E+09,9E+09,9E+09]];
CONST robtarget Target_35:=[[30,30,-798.16],[1,0,0,0],[-2,0,-1,0],[9E+09,9E+09,9E+09,9E+09,9E+09,9E+09]];
CONST robtarget Target_30:=[[30.000146555,30.000033692,-11.381966907],[0.996910434,0.002545816,-0.069559583,0.036394646],[-2,-1,0,0],[9E+09,9E+09,9E+09,9E+09,9E+09,9E+09]];
```

Para trabajar con coordenadas simples en cada zona, se definen dos sistemas de coordenadas de trabajo (`wobjdata`): `Workobject_9` sobre la banda y `Workobject_13` sobre el almacén. También se define la herramienta `MyNewToolEI`, correspondiente al electroimán montado en el efector final, especificando su punto central de herramienta (TCP) y su carga.

```
PERS tooldata MyNewToolEI:=[TRUE,[[0.05,0,41.6],[1,0,0,0]],[1,[0,0,1],[1,0,0,0],0,0,0]];
TASK PERS wobjdata Workobject_9:=[FALSE,TRUE,"",[[340.64,-9.06,-420.092],[1,0,0,0]],[[-410,-593.41,264],[0,0.707106781,0.707106781,0]]];
TASK PERS wobjdata Workobject_13:=[FALSE,TRUE,"",[[50,0,-404],[1,0,0,0]],[[600,721,702],[0,-0.707106781,0.707106781,0]]];
```

## Variables de estado, contadores y comunicación con la HMI

El proceso se apoya en variables persistentes (`PERS`) para que su valor sea visible en todo momento por la interfaz HMI y se conserve entre ciclos. Entre ellas están los contadores de inventario por tipo de componente, el estado general del proceso, el tipo de componente detectado, y un grupo de variables de confirmación (`ConfirmarClasificacion`, `ConfirmarPick`, `ConfirmarPlace`) que funcionan como semáforos entre el robot y el operador: el programa se detiene en un `WaitUntil` hasta que la HMI escribe en ellas un valor distinto de cero.

```
PERS num ContRes:=0;
PERS num ContCap:=0;
PERS num ContCon:=0;
PERS num ContCI:=0;
PERS num Total:=0;

PERS num Estado:=99;
PERS num Tipo:=4;

PERS num ConfirmarClasificacion:=0;
PERS num ConfirmarPick:=0;
PERS num ConfirmarPlace:=0;
PERS bool PlaceOK:=FALSE;
```

## Programa principal (`main`)

La rutina `main` conecta la interrupción de emergencia a la señal digital correspondiente y luego organiza el proceso en dos bucles anidados: uno externo que representa un lote completo (un almacén), y uno interno que se repite una vez por cada componente hasta alcanzar la cantidad definida. Dentro del bucle interno se invoca, en orden, cada etapa del ciclo de un componente: detección, clasificación, confirmación, Pick, confirmación de Pick, Place y confirmación de Place.

```
PROC main()

    CONNECT IntSalida WITH RevisarEmergencia;
    ISignalDO DO_01,1,IntSalida;

    WHILE TRUE DO

        InicializarSistema;
        EsperarInicio;

        WHILE Total<2 DO

            EsperarComponente;
            CapturarImagen;
            ClasificarComponente;
            ConfirmarClasificacionProc;
            RealizarPick;
            Realizaraproxpick;
            ConfirmarPickProc;
            RealizarPlace;
            ConfirmarPlaceProc;

            IF PlaceOK THEN
                ActualizarInventario;
            ENDIF

        ENDWHILE

        FinEtapa;

    ENDWHILE

ENDPROC
```

## Inicialización y manejo de la parada de emergencia

`InicializarSistema` deja el proceso en un estado conocido al inicio de cada lote: reinicia contadores, banderas y botones de la HMI, apaga los actuadores (banda y electroimán) y envía el robot a Home. La parada de emergencia por software se implementa como una interrupción (`TRAP`), lo que permite que, sin importar la instrucción que se esté ejecutando, una activación de la señal asociada desvíe inmediatamente el flujo del programa hacia `RevisarEmergencia`, apague los actuadores y deje el sistema esperando confirmación del operador para reanudar.

```
TRAP RevisarEmergencia

    MensajeEstado:="PARADA DE SOFTWARE, oprima inicio para continuar";

    Reset DO_01;
    Reset DO_05;
    Set DO_06;

    Reset FWD_Conveyor;
    Reset BWD_Conveyor;

    Estado:=99;

    WaitUntil BtnInicio;
    BtnInicio:=FALSE;

    MensajeEstado:="Reanudando proceso";

ENDTRAP
```

## Recepción, captura y clasificación del componente

Estas tres rutinas implementan la entrada de la estación. `EsperarComponente` enciende la banda y espera al sensor de presencia (`DI_04`); `CapturarImagen` dispara la cámara mediante un pulso en `DO_05` y da tiempo al sistema de visión para procesar la imagen; `ClasificarComponente` traduce el resultado del sistema de visión, leído en cuatro entradas digitales (una por categoría), a la variable `Tipo`.

```
PROC ClasificarComponente()

    Estado:=2;

    IF DI_07=0 AND DI_08=0 AND DI_09=0 AND DI_10=0 THEN
        Tipo:=0;
        Ninguno:=1;
        MensajeEstado:="Tipo: No identificado";
    ENDIF

    IF DI_07=1 THEN
        Tipo:=1;
        MensajeEstado:="Tipo: Resistencia";
    ENDIF

    IF DI_08=1 THEN
        Tipo:=2;
        MensajeEstado:="Tipo: Capacitor";
    ENDIF

    IF DI_09=1 THEN
        Tipo:=3;
        MensajeEstado:="Tipo: Conector";
    ENDIF

    IF DI_10=1 THEN
        Tipo:=4;
        MensajeEstado:="Tipo: Circuito integrado";
    ENDIF

    WaitTime 3;

ENDPROC
```

## Confirmación de clasificación

Esta rutina implementa la verificación mínima exigida antes de tomar el componente: el programa espera a que el operador confirme desde la HMI si la clasificación fue correcta. Según la respuesta, se ejecuta una de tres rutas: continuar con normalidad (`CASE 1`), retroceder el componente en la banda y repetir todo el ciclo de detección y clasificación (`CASE 2`), o descartar el componente y pasar directamente a esperar uno nuevo (`CASE 3`). Los casos 2 y 3 se resuelven mediante una llamada recursiva a la misma rutina, de modo que el programa no continúa hasta obtener una clasificación aceptada.

```
PROC ConfirmarClasificacionProc()

    MensajeEstado:="Esperando confirmación de selección";

    WaitUntil ConfirmarClasificacion<>0;

    TEST ConfirmarClasificacion

    CASE 1:
        Set FWD_Conveyor;
        Set BWD_Conveyor;
        WaitTime 1.5;
        Reset FWD_Conveyor;
        Reset BWD_Conveyor;

    CASE 2:
        Ninguno:=0;
        ConfirmarClasificacion:=0;
        Set BWD_Conveyor;
        WaitTime 3;
        Reset BWD_Conveyor;

        EsperarComponente;
        CapturarImagen;
        ClasificarComponente;
        ConfirmarClasificacionProc;

    CASE 3:
        Ninguno:=0;
        ConfirmarClasificacion:=0;

        EsperarComponente;
        CapturarImagen;
        ClasificarComponente;
        ConfirmarClasificacionProc;

    ENDTEST

    ConfirmarClasificacion:=0;

ENDPROC
```

## Operación de Pick y su verificación

El Pick se divide en dos movimientos independientes: `RealizarPick` lleva el robot a la posición de aproximación sobre el componente y libera el electroimán, mientras que `Realizaraproxpick` ejecuta el descenso final, la sujeción y el levantamiento de la pieza. `ConfirmarPickProc` implementa la verificación mínima del Pick: si el operador indica que la toma falló, se reintenta solo el movimiento de aproximación; si indica que el componente debe rechazarse, el ciclo completo (detección, clasificación y Pick) se repite desde el inicio.

```
PROC ConfirmarPickProc()

    MensajeEstado:="Esperando confirmación de PICK";

    WaitUntil ConfirmarPick<>0;

    TEST ConfirmarPick

    CASE 1:
        MensajeEstado:="PICK confirmado";

    CASE 2:
        ConfirmarPick:=0;
        MensajeEstado:="Reintentando PICK";
        Realizaraproxpick;
        ConfirmarPickProc;

    CASE 3:
        ConfirmarPick:=0;
        MensajeEstado:="Componente rechazado";

        EsperarComponente;
        CapturarImagen;
        ClasificarComponente;
        ConfirmarClasificacionProc;
        RealizarPick;
        Realizaraproxpick;
        ConfirmarPickProc;

    ENDTEST

    ConfirmarPick:=0;

ENDPROC
```

## Operación de Place, su verificación y actualización del inventario

`RealizarPlace` selecciona la trayectoria de depósito correspondiente a la celda del almacén según el `Tipo` de componente. `ConfirmarPlaceProc` implementa la verificación mínima del Place, marcando `PlaceOK` en `TRUE` o `FALSE` según la confirmación del operador; esta bandera es la que usa `main()` para decidir si el componente se contabiliza. `ActualizarInventario` incrementa el contador correspondiente al tipo almacenado y el contador global `Total`, que controla el bucle principal y alimenta el indicador de progreso de la HMI.

```
PROC RealizarPlace()

    TEST Tipo

    CASE 1:
        MensajeEstado:="Almacenando resistencia";
        Path_12;

    CASE 2:
        MensajeEstado:="Almacenando capacitor";
        Path_13;

    CASE 3:
        MensajeEstado:="Almacenando conector";
        Path_14;

    CASE 4:
        MensajeEstado:="Almacenando CI";
        Path_15;

    ENDTEST

ENDPROC
```

```
PROC ActualizarInventario()

    MensajeEstado:="Almacén actualizado";

    TEST Tipo

    CASE 1:
        ContRes:=ContRes+1;

    CASE 2:
        ContCap:=ContCap+1;

    CASE 3:
        ContCon:=ContCon+1;

    CASE 4:
        ContCI:=ContCI+1;

    ENDTEST

    Total:=Total+1;
    Progreso:=Total;

ENDPROC
```

## Fin de etapa

Cuando el contador `Total` alcanza la cantidad requerida, `FinEtapa` apaga los actuadores, notifica al operador que el almacén está listo y detiene el proceso hasta que se presiona el botón de reinicio en la HMI, momento en el cual `main()` vuelve a llamar a `InicializarSistema` para comenzar un nuevo lote.

```
PROC FinEtapa()

    Estado:=4;
    FIN:=1;

    Reset DO_01;
    Reset DO_05;
    Set DO_06;
    Reset FWD_Conveyor;
    Reset BWD_Conveyor;

    MensajeEstado:="¡Almacén listo!";
    WaitTime 3;

    MensajeEstado:="Esperando Nuevo almacén ...";
    WaitUntil BtnReset;
    BtnReset:=FALSE;

ENDPROC
```

## Trayectorias del robot (`Path_10` a `Path_15`)

Las rutinas `Path_10` a `Path_15` contienen únicamente instrucciones de movimiento (`MoveJ`, `MoveL`) y son las que ejecutan físicamente el desplazamiento del robot. `Path_10` lleva el robot a Home; `Path_11` y `Path_11_1` realizan la aproximación y la toma del componente sobre la banda; `Path_12` a `Path_15` llevan el componente ya sujetado hasta la celda del almacén correspondiente a su tipo (resistencias, capacitores, conectores y circuitos integrados, respectivamente), activan el electroimán en reversa para soltar la pieza y retiran el robot de la celda.

```
PROC Path_12()
    MoveL Target_35,v30,z10,MyNewToolEI\WObj:=Workobject_9;
    MoveJ Target_Home,v100,z10,MyNewToolEI\WObj:=wobj0;
    MoveJ Target_39,v100,z10,MyNewToolEI\WObj:=Workobject_13;
    MoveL Target_40,v30,z10,MyNewToolEI\WObj:=Workobject_13;
    Set DO_06;
    WaitTime 6;
    MoveJ Target_39,v30,z10,MyNewToolEI\WObj:=Workobject_13;
ENDPROC
```

Las rutinas `Path_13`, `Path_14` y `Path_15` siguen exactamente la misma estructura que `Path_12`, cambiando únicamente el par de posiciones de destino (`Target_49/50`, `Target_59/60` y `Target_69/70` respectivamente), ya que corresponden a la misma lógica de depósito aplicada a una celda distinta del almacén.

## Descripción de las funciones utilizadas.

| Procedimiento | Función dentro del proceso |
|---|---|
| `main` | Orquesta el ciclo completo: inicialización, espera de inicio y el bucle de recepción-clasificación-almacenamiento por componente. |
| `InicializarSistema` | Restablece variables, contadores y actuadores al inicio de cada lote. |
| `EsperarInicio` | Detiene el proceso hasta que el operador presiona Start. |
| `RevisarEmergencia` (TRAP) | Atiende la parada de emergencia por software en cualquier punto del programa. |
| `EsperarComponente` | Controla la banda hasta detectar un componente disponible para el Pick. |
| `CapturarImagen` | Dispara la cámara y espera el procesamiento del sistema de visión. |
| `ClasificarComponente` | Traduce las señales del sistema de visión a la variable `Tipo`. |
| `ConfirmarClasificacionProc` | Verificación mínima de clasificación, con reintento o descarte según respuesta del operador. |
| `RealizarPick` / `Realizaraproxpick` | Ejecutan la aproximación y la toma física del componente. |
| `ConfirmarPickProc` | Verificación mínima del Pick, con reintento o rechazo del componente. |
| `RealizarPlace` | Selecciona la celda de destino según el tipo de componente. |
| `ConfirmarPlaceProc` | Verificación mínima del Place y actualización de la bandera `PlaceOK`. |
| `ActualizarInventario` | Incrementa los contadores de inventario por tipo y el total del lote. |
| `FinEtapa` | Cierra el lote, apaga actuadores y espera el reinicio del operador. |
| `Path_10` a `Path_15` | Trayectorias físicas de movimiento del robot entre Home, banda y celdas del almacén. |
