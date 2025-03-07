# Laboratorio 3 - Electrónica Digital I / Voltímetro
### Nombres: Juan Pablo López Bolívar - Laura Valentina López Vergara - Daniel Castillo Silva

El objetivo del laboratorio 3 es realizar un voltímetro para su posterior visualización. Sin embargo, dado que nuestro proyecto tenía varias similitudes con el laboratorio 3, lo que hicimos fue basarnos en dicho proyecto para desarrollarlo. Este se fundamenta en un sistema de piezoeléctricos como fuente de corriente continua, la cual pasa por un puente rectificador 1N4148 para minimizar el consumo. Luego, la señal es procesada por un ADC capacitivo que opera con un protocolo I2C. Para su funcionamiento, se diseñó una máquina de estados. Posteriormente, la señal digital es recibida por el ESP32, que procesa los datos y los envía a Arduino Cloud, donde se visualizan en formato decimal.

### 1. **Arquitectura**

1.1 **Parte análoga:**

**Diagrama de bloques:**

A continuación, se presenta la representación gráfica del diagrama de bloques:

![Figura 1](lab03%20imagenes/figura1.png)

***Figura 1.*** *Diagrama de bloques.*

1.2 **Diseño e implementación del circuito:**

Se optó por implementar un arreglo combinado en serie y paralelo de los piezoeléctricos, con el objetivo de obtener simultáneamente valores significativos de voltaje y corriente, optimizando así la eficiencia en la generación de energía.

Para ello, la implementación de los siguientes materiales fue fundamental:

* Catorce piezoeléctricos
* Una lámina de acrílico
* Cables
* Estaño
* Silicona
* Tabla de madera
* Condensadores (10 uF - 50V)
* Diodos 1N4148

Inicialmente, se realizó la distribución de los piezoeléctricos sobre una tabla de madera con el objetivo de garantizar que la presión generada por cada pisada se distribuyera de manera uniforme entre ellos. Para asegurar este aspecto, se utilizó una lámina de acrílico junto con gomas de silicona adhesiva, lo que permitió optimizar la transmisión de la fuerza sobre cada dispositivo.

La salida generada por los piezoeléctricos se configuró en paralelo, permitiendo su uso como fuente de entrada en el arreglo circuital. Este arreglo está diseñado para almacenar el voltaje y la energía generada mediante tres condensadores conectados en paralelo, lo que imita el funcionamiento de una batería al incrementar la capacidad de almacenamiento sin alterar el nivel de voltaje.

### **2. SIMULACIÓN Y MONTAJE INICIAL DE CIRCUITO DE PIEZOELÉCTRICOS Y CIRCUITO ANALÓGICO**

![Figura 2](lab03%20imagenes/figura2.png)

***Figura 2.*** *Implementación en Proteus.*

La simulación en Proteus tiene como objetivo evaluar el comportamiento del circuito diseñado para la captación y almacenamiento de energía generada por discos piezoeléctricos. Se analizan la disposición de los componentes, la rectificación de la señal y la eficiencia del almacenamiento de energía en los condensadores.

2.1. **Disposición en la simulación**

Generación de Energía – Arreglo de Piezoeléctricos: En la parte superior de la simulación, se observa un conjunto de 14 discos piezoeléctricos organizados en un esquema serie/paralelo. Esta disposición busca equilibrar voltaje y corriente para mejorar la eficiencia del sistema. Cada disco piezoeléctrico actúa como una fuente de voltaje alterno que responde a la presión aplicada.

Rectificación de la Señal – Puente de Diodos: La energía generada por los piezoeléctricos se dirige hacia un puente de diodos 1N4148, encargado de convertir la señal alterna (AC) en continua (DC). Este puente de diodos permite una rectificación de onda completa, utilizando ambas mitades de la señal alterna y mejorando la eficiencia energética.

Filtrado y Almacenamiento – Banco de Condensadores: A la salida del puente de diodos, se conecta un banco de condensadores en paralelo compuesto por tres condensadores de 10 µF - 50V. La función principal de estos condensadores es suavizar la señal rectificada y almacenar la energía generada. La conexión en paralelo aumenta la capacidad total de almacenamiento y ayuda a reducir la fluctuación del voltaje.

2.2. **Resultado de simulación en proteus de “una pisada**

![Figura 3](lab03%20imagenes/figura3.png)

***Figura 3.*** *Simulación de una pisada.*

Medición de la Salida: En la simulación se ha colocado un voltímetro a la salida del banco de condensadores. La lectura del voltímetro muestra un valor de 0.08V AC, tras únicamente una señal de entrada interpretada como una pisada.

2.3. **Resultados Observados en la Simulación:**

* El circuito logra rectificar la señal alterna generada por los piezoeléctricos.

* Los condensadores permiten almacenar la energía y estabilizar la salida.

* Se observa una pequeña oscilación en la salida, posiblemente debido a la carga dinámica de los piezoeléctricos o la falta de un filtrado adicional.

* La configuración serie/paralelo en los piezoeléctricos parece adecuada para equilibrar voltaje y corriente.

A continuación se adjuntan imágenes de la implementación física del proyecto.

![Figura 4](lab03%20imagenes/figura4.png)

***Figura 4.*** *Implementación física circuito piezoeléctricos sobre tabla de madera.*

![Figura 5](lab03%20imagenes/figura5.png)

***Figura 5.*** *Circuito análogo - rectificador de onda y condensadores en paralelo.*

El circuito de los piezoeléctricos se conecta con el circuito análogo para que éste rectifique la onda de corriente alterna. La tensión recae sobre los condensadores para posteriormente ser medida y pasar por el ADC para poder ser procesado en valores binarios por la FPGA.

2.4. **Descripción Hardware:**

**FPGA**

La FPGA en este proyecto tiene la función de procesar los datos digitales que recibe del ADC ADS1015. Los valores binarios que llegan a la FPGA son procesados internamente y transformados en arreglos de vectores. Estos vectores representan los datos de manera organizada y eficiente, lo que permite realizar cálculos y análisis, como la cantidad de energía generada.

El proceso exacto de cómo se realiza esta transformación está pendiente de detalles adicionales, pero en términos generales, la FPGA utiliza bloques lógicos programables para manejar estos bits y manipularlos en arreglos que se pueden utilizar para el procesamiento posterior. Esto facilita el análisis y la gestión de los datos de manera efectiva en tiempo real.

En cuanto a los recursos utilizados en la FPGA, al hace urso de la función make log-syn, obtenemos que:

### 3. **PROTOCOLO I²C: ADC ADS1015**

El ADC sirve para convertir la señal analógica generada por los piezoeléctricos en datos digitales que puedan ser procesados por la FPGA. Este ADC tiene un convertidor de 12 bits, que permite convertir el voltaje analógico a una representación digital con alta resolución.

Tras que la señal de salida ha sido rectificada y suavizada mediante los condensadores, estabilizando el voltaje para una medición precisa, la conversión a digital con ADS1015 da lugar. Una vez que la señal es estabilizada y rectificada, se envía al convertidor analógico-digital (ADC) ADS1015. Este ADC opera a través del protocolo de comunicación I²C, lo que permite su fácil integración con dispositivos como la FPGA, ya que utiliza solo dos líneas de comunicación: SDA (data) y SCL (clock). El ADS1015 convierte la señal analógica de voltaje en valores digitales que representan la intensidad del voltaje en formato binario, con una resolución de 12 bits.

El código usa una máquina de estados finitos (FSM) para manejar la comunicación I2C. Esta FSM se define con el registro S, que puede tomar los siguientes valores:

* Estado 0: Inicio de transmisión

* Estado 1: Escritura de datos

* Estado 2: Lectura de datos

* Estado 3: Final de transmisión

La FSM cambia de estado según condiciones específicas, y cada estado ejecuta ciertas acciones.

![Figura 6](lab03%20imagenes/figura6.png)

***Figura 6.*** *Máquina de estados I2C.*

Este diseño se abordarà con profundidad en el laboratorio 4.

### 4. **DISEÑO Y MONTAJE DE PCB**

La implementación analógica del circuito se diseñó con el objetivo de generar una PCB con el fin de cumplir con la rúbrica en cuanto a la presentación del proyecto, para eso se hizo uso del software KiCad, el cual permitió establecer este aspecto.

![Figura 7](lab03%20imagenes/figura7.png)

***Figura 7.*** *PCB en KiCad.*

Se implementó la entrada de los piezoeléctricos como una bornera de 1x2, luego el puente rectificador de diodos con referencia al 1N4148 y los tres condensadores electrolíticos en paralelo. Finalmente, KiCad permite la implementación del conversor ADS1015, se relacionaron las entradas, las salidas, los puertos de energización y salida a la FPGA.

![Figura 8](lab03%20imagenes/figura8.png)

***Figura 8.*** *Implementación PCB.*

Finalmente este es el diseño propuesto de la pcb, con un tamaño aproximado de 5x5 cm, sin embargo, se está a la espera de la implementación del ESP32 el cual nos ayudará a la transmisión y visualización por medio de WiFi, se espera adjuntar este dispositivo al diseño de la PCB propuesta.

### 5. **MODULO ESP-32**

Una vez que los datos son procesados por la FPGA, está los transmite a un módulo ESP-32 a través de un cable UART (Universal Asynchronous Receiver-Transmitter). El UART es un protocolo de comunicación que permite la transferencia de datos entre la FPGA y el ESP-32 de manera serial. Este protocolo es ideal para la comunicación entre dispositivos a través de un canal de datos simple, utilizando dos líneas principales: una para transmitir (TX) y otra para recibir (RX).

El ESP-32 actúa como un intermediario entre la FPGA y la nube de Arduino Cloud. Este módulo tiene capacidades de conectividad Wi-Fi, lo que le permite enviar los datos procesados de la FPGA de manera inalámbrica a la nube. A través de la red Wi-Fi, el ESP-32 transmite los datos en tiempo real, facilitando su visualización y monitoreo desde cualquier dispositivo conectado a Internet.

Una vez que los datos llegan a la Arduino Cloud, pueden ser almacenados, procesados y visualizados de manera remota. La nube permite acceder a los datos de manera centralizada y proporciona herramientas para analizar el rendimiento del sistema de generación de energía en tiempo real. Esto es especialmente útil para monitorear parámetros como la cantidad de energía generada, además de proporcionar un historial de los datos recolectados.

Este flujo de datos desde la FPGA hacia la nube a través del ESP-32 asegura que la información esté siempre disponible para su análisis y permite una gestión eficiente de los recursos generados por los sensores piezoeléctricos. Además, al usar Arduino Cloud, se habilitan opciones de integración con otros dispositivos y plataformas de IoT, lo que amplía las posibilidades de expansión y control del sistema.

## 6. **ARDUINO CLOUD**

Arduino Cloud es una plataforma web que permite gestionar y monitorear proyectos de Internet de las Cosas (IoT) de manera sencilla y eficiente. A través de esta plataforma, los dispositivos conectados a la red, como el ESP-32 en este proyecto, pueden transmitir datos de manera remota, los cuales se pueden visualizar, almacenar y analizar en tiempo real desde cualquier dispositivo con acceso a Internet.

Una de las características más destacadas de Arduino Cloud es su integración con la Arduino IoT Cloud que permite a los usuarios crear Dashboard personalizados, que proporcionan una interfaz visual para controlar y monitorear los datos de los dispositivos conectados. En el contexto de tu proyecto, una vez que los datos procesados por la FPGA son enviados al ESP-32, este los transmite a la nube, donde pueden ser visualizados en tiempo real a través de estos Dashboards. Esto facilita el acceso remoto a la información generada, como la cantidad de pasos o la energía recolectada.

Además de su capacidad para monitorear en tiempo real, Arduino Cloud permite configurar alertas y notificaciones, lo que podría ser útil para señalar eventos específicos, como un incremento inusual en la energía generada o problemas técnicos en el sistema. También facilita la gestión de dispositivos de manera centralizada, lo que permite realizar actualizaciones y ajustes de forma remota sin necesidad de intervención física.

Arduino Cloud también ofrece funcionalidades para almacenar datos históricos, permitiendo un análisis a largo plazo del rendimiento del sistema, lo cual es valioso para evaluar la eficiencia del sistema de generación de energía piezoeléctrico y tomar decisiones informadas sobre futuras mejoras o ajustes. Esta integración de datos en la nube optimiza la operatividad del sistema, ya que los usuarios pueden tener acceso constante y remoto a la información, contribuyendo a una gestión más eficiente y a una mejor toma de decisiones.

Este código en Arduino se encarga de recibir datos por el puerto serial, procesarlos y calcular un valor de voltaje a partir de un número digital recibido. A continuación, te explico cada parte paso a paso:

6.1. **Definición de Variables**

![Código 1](lab03%20imagenes/codigo1.png)

* estado: almacena los datos recibidos por el puerto serial.

* msb y lsb: guardan el byte más significativo (Most Significant Byte - MSB) y el byte menos significativo (Least Significant Byte - LSB) de un número de 12 bits.

* mensaje: sirve como un indicador de estado para procesar los datos en diferentes etapas.

* v: almacena el voltaje calculado.

* num_dig: contiene el número digital de 12 bits obtenido de msb y lsb.

6.2. **Función de Complemento a 2**

![Código 2](lab03%20imagenes/codigo2.png)

* Esta función convierte un número de 12 bits con signo (que usa complemento a 2) a un número de 16 bits con signo.

* Si el bit más significativo (bit 11) es 1 (num & 0x800), significa que el número es negativo y se extiende el signo con 0xF000 para que mantenga su valor negativo en 16 bits.

* Si el número es positivo, simplemente lo devuelve sin cambios.

Ejemplo:

* num = 0x7FF (0111 1111 1111) → positivo, no cambia.

* num = 0x800 (1000 0000 0000) → negativo, se convierte en 0xF800 (1111 1000 0000 0000).

6.3. **Configuración Inicial (setup())**

![Código 3](lab03%20imagenes/codigo3.png)

* **Inicia la comunicación serial a 9600 baudios.**

* Inicializa msb, lsb y num_dig en 0.

6.4. **Bucle Principal (loop())**

![Código 4](lab03%20imagenes/codigo4.png)

* **Si hay datos en el puerto serial**, los lee y los almacena en estado.

6.4.1. **Procesamiento del Mensaje**

![Código 5](lab03%20imagenes/codigo5.png)

* El protocolo de comunicación usa un byte de inicio con el valor 253.

* Si recibe 253, se prepara para recibir los datos (mensaje = 1).

* Luego, recibe el MSB y lo almacena en msb, pasando al estado 2.

* Después, recibe el LSB y lo almacena en lsb, pasando al estado 3.

6.5. **Conversión de Datos**

![Código 6](lab03%20imagenes/codigo6.png)

* Una vez que se tienen msb y lsb, se reconstruye el número de 12 bits:

    * Se desplaza msb 4 bits a la izquierda (msb << 4).

    * Se desplaza lsb 4 bits a la derecha (lsb >> 4).

    * Se combinan usando | (OR bit a bit) para formar el número completo.

Ejemplo:

* msb = 0x12 (0001 0010), lsb = 0x34 (0011 0100).

* num_dig = (0x12 << 4) | (0x34 >> 4) = (0010 0100 0000) | (0000 0011) = 0010 0100 0011.

6.6. **Conversión a Complemento a 2**

![Código 7](lab03%20imagenes/codigo7.png)

* Se convierte el número de 12 bits en un entero con signo de 16 bits usando complemento2(num_dig).

* Se imprime el número resultante en el monitor serial.

6.7. **Cálculo del Voltaje**

![Código 8](lab03%20imagenes/codigo8.png)

* Interpola el número digital num_dig a un voltaje:

    * Cuando num_dig = -274, el voltaje es 0.7V.

    * Cuando num_dig = 1200, el voltaje es 5V.

    * Se usa la ecuación de la recta entre estos dos puntos para calcular el voltaje v

A Continuaciòn, se muestran los datos en binario recibidos por la ESP32 en arduino cloud:

![Figura 9](lab03%20imagenes/figura9.png)

***Figura 9.*** *Datos en binario recibidos por la ESP32 en arduino cloud.*

## 7. **RESULTADOS FINALES**

La solución desarrollada en este proyecto consistió en la implementación de un sistema de captación de energía basado en sensores piezoeléctricos, con la capacidad de medir en tiempo real el voltaje generado y almacenado en condensadores que simulan una batería. Para ello, se diseñó una arquitectura que integra una FPGA BlackIce 40, encargada de la adquisición de datos a través de un ADC capacitivo ADS1015, y un ESP32-S2 Mini, responsable de la transmisión de información a Arduino Cloud para su visualización remota.

Inicialmente, en la solución propuesta, se contemplaba no solo la captación de energía, sino también la medición del número de pasos realizados por el usuario. Sin embargo, por cuestiones de tiempo y optimización, esta funcionalidad fue descartada, ya que requería la integración de un circuito adicional en el puente rectificador para detectar los pulsos generados por cada paso. Esta modificación habría añadido complejidad al diseño y al procesamiento de datos, comprometiendo el desarrollo dentro del plazo establecido.

A pesar de esta diferencia con la solución inicial, el objetivo principal del proyecto se mantuvo inalterado: demostrar la viabilidad de captar energía piezoeléctrica de los pasos y visualizar en tiempo real el voltaje generado. La arquitectura implementada permite un monitoreo eficiente y sienta las bases para futuras mejoras, como la integración del conteo de pasos en versiones posteriores del sistema.

![Figura 10](lab03%20imagenes/figura10.png)
***Figura 10.*** *Datos en decimal mostrados en la pantalla de un celular.*