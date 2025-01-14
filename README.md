# Laboratorio 2 - Electrónica Digital I
### Nombres: Juan Pablo López Bolívar - Laura Valentina López Vergara - Daniel Castillo Silva

### 1. **Dominio Comportamental**

1.1 **Sistema de Caja Negra**

![Caja negra](lab2%20imagenes/caja.png)

**Entradas:**

* I0: Botón de Paro: (1 si está activado, 0 si no).
* I1: Red Eléctrica: (1 si hay suministro, 0 si no).
* I2: Baterías: (1 si están cargadas, 0 si no).

**Salidas (Indicadores)**:

* Q0: Indica el estado de paro:  (1 si está activado, 0 si no).
* Q1: Relè/Energizador: (1 si está energizado, 0 si no).
* Q2: Sensor energía:(1 si hay energía disponible, 0 si no).
* Q3: Rele contactor: (1 si está activado, 0 si no).
* Q4: Sensor baterias: (1 si las baterías están cargadas, 0 si no).
* Q5: Sensor red: (1 si la red eléctrica está disponible, 0 si no).

1.2 **Tabla de Verdad**

![Tabla de verdad](lab2%20imagenes/tab.png)

1.3 **Algoritmo representado en diagrama de flujo**

![Diagrama de Flujo](lab2%20imagenes/flowchart.jpg)

**Lógica aplicada a la situación problema:**

La lógica del diseño se basó en la premisa de que la casa siempre debe estar energizada, salvo en casos de fallos externos o cuando el usuario active el botón de paro manual. La salida Q5, correspondiente al sensor de red eléctrica, permanece activa mientras la red esté disponible (I1). Si la red falla, el banco de baterías (I2) entra en acción para suplir la demanda energética, activando el relé que conmutara entre las fuentes de energía (Q1), el sensor de energìa (Q2) y el senor de las baterías (Q4).

Además, se implementó un botón de paro manual (I0) para que el usuario pudiera desenergizar la casa cuando lo desee, por ejemplo, durante tareas de mantenimiento. En caso de que tanto la red eléctrica como el banco de baterías estén disponibles (I1, I2), la casa tomará la energía de la red para preservar la carga de las baterías y utilizar su energía después.

En cuanto a las entradas, (I0) e (I1) son pulsadores normalmente abiertos, que se activan únicamente cuando son presionados. Por otro lado, (I2), correspondiente a la red eléctrica, es un pulsador normalmente cerrado, lo que asegura que la entrada esté activa a menos que ocurra un fallo en el suministro eléctrico. Esta lógica garantiza que el sistema funcione de manera confiable y eficiente, cumpliendo con las necesidades planteadas por la situación problemática.


### 2. **Dominio Físico Inicial (Circuito Eléctrico)**

Se procede a diseñar un circuito eléctrico del sistema de control con sus contactos de entrada y actuadores de salida.

![Circuito Digital](lab2%20imagenes/circuito.jpg)

**Entradas:**

* I0: Botón de Paro.
* I1: Red Eléctrica.
* I2: Baterías. 

**Salidas (Indicadores)**:

* Q0: Indica el estado de paro
* Q1: Relè/Energizador
* Q2: Sensor energía
* Q3: Rele contactor
* Q4: Sensor baterias
* Q5: Sensor red

Circuito representado a través del lenguaje ladder.

![Circuito Digital](lab2%20imagenes/circuito_ladder.png)

**Suposiciones:**

1. **Sistema de Control, no de potencia:**

Se asume que el sistema tiene un enfoque de control y no de generación de energía, por lo que no se considera que deba manejar grandes cantidades de potencia de manera directa. En consecuencia, se asume que las baterías siempre estarán en un estado ideal, es decir, completamente cargadas y disponibles para suplir la demanda energética en caso de que la red eléctrica no esté disponible.

2. **Disponibilidad de la red eléctrica:**

Se parte de la premisa de que la red eléctrica estará disponible en todo momento, excepto en situaciones de fallos o interrupciones en el suministro. Esto permite que el sistema opere bajo la suposición de que, en circunstancias normales, la casa estará energizada a partir de la red.

3. **Uso de las Baterías:**

En el caso de que la red eléctrica falle, se espera que las baterías sean capaces de asumir la función de energizar la casa. Esta medida garantiza que el suministro eléctrico a la casa no se interrumpa, asegurando que el sistema mantenga su funcionamiento y cumpla con la necesidad de energía constante para el hogar.

4. **Fallo simultáneo de la red y las baterías:**

No se contempla la situación en la que tanto la red eléctrica como las baterías fallen simultáneamente, ya que esto implicaría que la casa no estaría energizada, lo que iría en contra de la premisa fundamental del sistema de garantizar un suministro eléctrico continuo. Este caso sería crítico y no resolvería la situación problemática que el sistema busca solucionar.

5. **Acción del botón de emergencia:**

Se asume que el botón de emergencia sólo será activado por decisión del usuario y no de manera automática. Si el sistema actuará de manera inversa o automática, se podría dar el resultado no deseado de desenergizar la casa, lo cual sería contraproducente y perjudicial para el propósito del sistema.

Estas suposiciones son clave para garantizar que el diseño del sistema sea coherente con los objetivos planteados y que el funcionamiento se mantenga dentro de las condiciones esperadas, sin generar situaciones no controladas.

### 3. **Simulación en Lenguaje Ladder**



### 4. **Dominio Estructural (red de compuertas lógicas)**

4.1 **Diagrama de lenguaje ladder a red de compuertas lógicas**



4.2 **Simulación en el dominio estructural**

Se procede a usar la herramienta de Digital para realizar la simulación de la representación en compuertas lógicas. 
Es importante tener en cuenta que el proyecto se debe guardar en una carpeta con el nombre de "top.dig", esto con el propósito
de poder sintetizar esta propuesta de solución en un dispositivo lógico programable (FPGA).

**Representación en compuertas de la solución:**

![Circuito Digital](lab2%20imagenes/circuito%20digital.png)

**Tabla de verdad del comportamiento de la red de compuertas**

![Tabla de verdad](lab2%20imagenes/tabla.png)

Adicionalmente, se obtienen los mapas de Karnaugh de cada una de las salidas.

**Mapa de Karnaugh para Q0**

![Mapa de Karnaugh Q0](lab2%20imagenes/MPK_Q0.png)

**Mapa de Karnaugh para Q1**
    
![Mapa de Karnaugh Q1](lab2%20imagenes/MPK_Q1.png)

**Mapa de Karnaugh para Q2**

![Mapa de Karnaugh Q2](lab2%20imagenes/MPK_Q2.png)

**Mapa de Karnaugh para Q3**

![Mapa de Karnaugh Q3](lab2%20imagenes/MPK_Q3.png)

**Mapa de Karnaugh para Q4**

![Mapa de Karnaugh Q4](lab2%20imagenes/MPK_Q4.png)

**Mapa de Karnaugh para Q5**

![Mapa de Karnaugh Q5](lab2%20imagenes/MPK_Q5.png)

Finalmente, Digital brinda la posibilidad de obtener los siguientes circuitos equivalentes.

**Solución equivalente representada por la compuerta universal NAND**

![CD con NAND](lab2%20imagenes/CD_NAND.png)

**Solución equivalente representada por la compuerta AND**

![CD con NAND](lab2%20imagenes/CD2.png)

De este modo, se procede a exportar la simulación en verilog para su posterior implementación en la FPGA.

### 5. **Descripción en Lenguaje HDL (Hardware Description Lenguage)**

Se obtiene el siguiente archivo:

![CD con NAND](lab2%20imagenes/verilog.png)

Ante este resultado se observa lo siguiente:

* El nombre del módulo asignado es "top".

* Se cuentan con tres entradas y seis salidas, comparándolo con el planteamiento de la caja negra en el dominio comportamental se cumple.

![TOP](lab2%20imagenes/top.png)

* I2 y ~I1 pasan por una compuerta AND.
* I0 pasa por una compuerta NOT.
* En las demás líneas, las señalas de entrada (I0,I2,I1) se asignan directamente a las salidas (Q0,Q4,Q5) o al valor intermedio (Q2_temp).

### 6. **Síntesis en FPGA (dominio físico final)**

Para el desarrollo de síntesis, se siguieron detalladamente los siguientes pasos de este [video](https://youtu.be/2GnsQ3oH4YA?si=yl4A_M93jGGdeuoe) compartido por el docente Johnny Cubides, para un correcto desarrollo en la síntesis en la FPGA. Con ayuda del programa [Fritzing](https://fritzing.org/) se realizó el circuito propuesto con el objetivo de facilitar visualmente la implementación análoga de la solución a la situación problema.

![Circuito](lab2%20imagenes/Doc1.png)

Posteriormente se configura los pines de entrada y salida de la FPGA:

![Pines](lab2%20imagenes/FPGA.png)

Obteniendo el siguiente montaje:

![Montaje](lab2%20imagenes/montaje.jpg)

![Montaje](lab2%20imagenes/montaje2.jpg)

Finalmente, en el siguiente [video](https://youtu.be/qAoocXic0_c) se muestran los resultados de este apartado, junto con el análisis de las situaciones que se evidenciaron para el desarrollo de este laboratorio.

### 7. **Conclusiones**

En conclusión, el proceso de diseño y desarrollo del sistema se llevó a cabo de manera estructurada y meticulosa, garantizando la funcionalidad y coherencia del mismo en cada una de sus etapas. Desde la conceptualización inicial mediante el diagrama de flujo, que permitió visualizar la lógica del sistema en función de las condiciones de la red eléctrica, las baterías y las interacciones del usuario, hasta la implementación final en FPGA, se aseguraron tanto la validez del diseño como su adaptación a la situación problemática planteada.

La simulación en lenguaje ladder jugó un papel crucial al validar la correcta operación de las lógicas propuestas en el dominio físico, permitiendo la detección temprana de posibles errores antes de su implementación en un entorno digital. A través del análisis detallado en el dominio estructural con compuertas lógicas y la optimización mediante mapas de Karnaugh, se verificó que las operaciones lógicas fueran consistentes con las especificaciones iniciales, mejorando la eficiencia del diseño y asegurando su robustez.

El paso de describir el sistema en Verilog y sintetizarlo en FPGA no solo facilitó la transición hacia un formato estándar para sistemas digitales, sino que también permitió la emulación precisa del comportamiento físico del sistema, incluyendo la simulación de los componentes esenciales como sensores, relés y pulsadores. Este enfoque integrador resultó fundamental para garantizar que el sistema fuera funcional y capaz de responder adecuadamente ante diversas condiciones operativas.

La lógica aplicada al diseño, que se basa en asegurar que la casa permanezca siempre energizada excepto en situaciones de fallos o paradas manuales (Botón de paro), reflejó una solución eficiente y confiable. El sistema logró conmutar entre la red eléctrica y el banco de baterías de manera óptima, priorizando la red eléctrica cuando ambas fuentes de energía estaban disponibles. Esta gestión de entradas y salidas, junto con la posibilidad de control manual por parte del usuario, aseguró que el diseño cumpliera con las necesidades operativas y de seguridad requeridas por la situación problemática.

En resumen, el flujo de diseño permitió construir un sistema que no solo cumple con los requisitos funcionales, sino que también proporciona una solución flexible, eficiente y adaptable, demostrando la efectividad del enfoque empleado en cada fase del proceso.