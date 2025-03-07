# Laboratorio 4 - Electrónica Digital I
### Nombres: Juan Pablo López Bolívar - Laura Valentina López Vergara - Daniel Castillo Silva

Para el laboratorio 4, como complemento de nuestro proyecto final, se diseñó una máquina de estados para el protocolo de comunicación I2C, es decir, la creación de un periférico para el conversor capacitivo ADS1015.

El ADC sirve para convertir la señal analógica generada por los piezoeléctricos en datos digitales que puedan ser procesados por la FPGA. Este ADC tiene un convertidor de 12 bits, que permite convertir el voltaje analógico a una representación digital con alta resolución.

Tras que la señal de salida ha sido rectificada y suavizada mediante los condensadores, estabilizando el voltaje para una medición precisa, la conversión a digital con ADS1015 da lugar. Una vez que la señal es estabilizada y rectificada, se envía al convertidor analógico-digital (ADC) ADS1015. Este ADC opera a través del protocolo de comunicación I²C, lo que permite su fácil integración con dispositivos como la FPGA, ya que utiliza solo dos líneas de comunicación: SDA (data) y SCL (clock). El ADS1015 convierte la señal analógica de voltaje en valores digitales que representan la intensidad del voltaje en formato binario, con una resolución de 12 bits. 

1. **Diseño del protocolo I2C**

Para diseñar el protocolo I2C se consideró la información suministrada por la hoja de datos del ADS1015. En la figura 6 se puede observar el comportamiento correspondiente a las señales SCL y SDA cuando se desea realizar una operación de escritura y lectura. 

El protocolo I2C parte de tener ambas señales en un valor lógico 1. Cuando se desea iniciar una nueva comunicación se coloca la señal SDA en un valor lógico 0, lo que pone en funcionamiento la señal de reloj SCL. Tras colocar SDA en 0, enviamos la dirección del esclavo con el que nos queremos comunicar, seguido de un bit para indicar si vamos a realizar una operación de escritura (R/W = 0) o de lectura (R/W = 1), y si la dirección es correcta, el dispositivo nos responderá con un bit de confirmación ACK = 0. 

- Operación de escritura:  Si R/W es cero (0), a continuación enviamos la dirección del registro al cual queremos apuntar. El registro con dirección 8b’00000001 se denomina registro de configuración y aquel con dirección 8b’00000000 se le conoce como registro de conversión. El registro de configuración nos permite modificar la funcionalidad del conversor. Por su parte, el registro de conversión almacena el valor de la señal analógica en formato digital.
  - Registro de configuración: Podemos configurar el ADS1015 enviando dos bytes de información, de acuerdo con los parámetros y las posibilidades presentadas en su hoja de datos. Así como con el registro de apuntador, enviamos los primeros 8 bits, a los que el esclavo responderá con un 0, luego enviamos el segundo conjunto de bits, y finalizamos la transmisión colocando la señal SDA en 0.
  - Registro apuntador: Solo podemos acceder a este registro cuando queremos realizar una operación de escritura. A partir del valor que le asignemos, en una futura operación de lectura podremos recuperar un cierto conjunto de datos. En otras palabras, podemos leer tanto el registro de configuración como el registro de conversión. 
- Operación de lectura: Si R/W es uno (1), habilitamos la señal SDA como entrada para recibir datos a través de ella. Posteriormente recibimos 8 bits desde el esclavo, luego enviamos un bit de confirmación ACK = 0, recibimos los siguientes 8 bits, y finalizamos la comunicación con otro bit de confirmación y colocando la señal SDA en 0.

![Figura 1](lab04%20imagenes/figura1.png)

***Figura 1.*** *Comportamiento de señales SDA y SCL del protocolo I2C.*

Otros aspectos importantes para considerar:

* La señal SCL tiene una frecuencia de 0.1 MHz; valor que se encuentra dentro del rango indicado en la hoja de datos del ADS1015.
* La señal SCL permanece en 1 mientras se transmite un dato. Los tiempos mínimos de estado en alto y bajo se pueden observar en la hoja de datos.
* Cada transmisión de datos finaliza colocando la señal SCL en 1, seguido de la señal SDA en 1.  

**2. Máquina de Estados en el Módulo I2C:**

A continuación, se presenta la explicación de la máquina de estados realizada para ejecutar el protocolo de comunicación I²C.

El código usa una máquina de estados finitos (FSM) para manejar la comunicación I2C. Esta FSM se define con el registro S, que puede tomar los siguientes valores:

* Estado 0: Inicio de transmisión
* Estado 1: Escritura de datos
* Estado 2: Lectura de datos
* Estado 3: Final de transmisión

La FSM cambia de estado según condiciones específicas, y cada estado ejecuta ciertas acciones.

![Figura 2](lab04%20imagenes/figura2.png)

***Figura 2.*** *Máquina de estados I2C.* 

A continuación, se presenta la explicación del código de la máquina de estados realizada en verilog:

* Estado 0: Inicio de transmisión

Este estado prepara la línea SDA para comenzar la transmisión.

case(S)

`    `0: begin    // Inicio de transmisión

`        `if (SCL\_en == 0) begin

`            `SDA\_en <= 1;

`            `SDA\_out <= 1;

`        `end



`        `if (c == 10 && fw == 0)begin

`            `SDA\_en <= 1;

`            `SDA\_out <= 0; // Se genera la condición de START (SDA pasa de 1 a 0 mientras SCL sigue en 1)

`            `S <= 1; // Se pasa al estado de escritura

`        `end

`    `end

**¿Qué está pasando aquí?**

* Se asegura de que SDA esté en alto (SDA\_out = 1).
* Cuando el contador c llega a 10, SDA\_out cambia a 0, lo que indica el inicio de la comunicación I2C.
* Se pasa al Estado 1 para comenzar la escritura de datos.

**Estado 1: Escritura**
Aquí se envían los datos al esclavo I2C. Se usa otra variable llamada sw para dividir este estado en 4 sub-estados:

1: begin    // Estado de escritura

`    `case (sw)

`        `2'b00: begin // Envío de dirección del esclavo

`            `bc <= 1;  // Se activa la transmisión de bits

`            `if (cf <= 8) begin

`                `SDA\_en  <= 1;

`                `SDA\_out <= dir\_msj[8 - cf]; // Se envía bit por bit la dirección del esclavo

`            `end

`            `else if (cf == 9) begin // Después del 9º bit (ACK)

`                `SDA\_en  <= 0; // Liberamos SDA para que el esclavo pueda responder

`                `if (RW == 1)

`                    `S  <= 2;  // Si se está en modo lectura, pasar a estado 2

`                `else

`                    `sw <= 2'b01; // Si se está en modo escritura, pasar al siguiente subestado

`            `end

`        `end

**¿Qué está pasando aquí?**

* Se envía la dirección del dispositivo esclavo en dir\_msj (7 bits de dirección + 1 bit de lectura/escritura).
* Se cuenta cf hasta 9 (8 bits + 1 bit ACK).
* Si RW == 1, se cambia al estado 2 (lectura).
* Si RW == 0, se pasa a sw = 01 para enviar más datos.

***Sub-estado 2'b01: Envío del Registro Apuntador:***

2'b01: begin // Envío del registro de apuntador

`            `if (cf <= 8) begin

`                `SDA\_en  <= 1;

`                `SDA\_out <= reg\_ap[8 - cf]; // Se envía el byte de registro

`            `end

`            `else if (cf == 9) begin // Se espera el ACK

`                `SDA\_en  <= 0;

`                `if (ap == 1'b1)

`                    `sw <= 2'b10; // Si `ap == 1`, continuar con más datos

`                `else begin

`                    `fw <= 1;

`                    `S <= 3; // Si no hay más datos, ir al estado final

`                `end

`            `end

`        `end

**¿Qué está pasando aquí?**

* Se envía un byte (reg\_ap) que indica qué registro se quiere leer/escribir en el esclavo.
* Si ap == 1, se pasa a sw = 10 para enviar más datos.
* Si ap == 0, se finaliza la comunicación (estado S = 3).

***Sub-estado 2'b10 y 2'b11: Envío de Configuración***

        2'b10: begin // Envío de configuración 1

`            `if (cf <= 8) begin

`                `SDA\_en  <= 1;

`                `SDA\_out <= conf1[8 - cf]; // Se envía el primer byte de configuración

`            `end

`            `else if (cf == 9) begin

`                `SDA\_en  <= 0;

`                `sw <= 2'b11; // Ir al siguiente dato

`            `end

`        `end

`        `2'b11: begin // Envío de configuración 2

`            `if (cf <= 8) begin

`                `SDA\_en  <= 1;

`                `SDA\_out <= conf2[8 - cf]; // Se envía el segundo byte de configuración

`            `end

`            `else if (cf == 9) begin

`                `SDA\_en  <= 0;

`                `fw <= 1;

`                `S <= 3; // Ir al estado final

`            `end

`        `end

**¿Qué está pasando aquí?**

* Se envían los datos conf1 y conf2 al esclavo.
* Luego se va al estado 3 para finalizar.

## Estado 2: Lectura de Datos

Si RW == 1, se entra en este estado. Se lee la información enviada por el esclavo.

 begin    // Estado de lectura

`    `if (sw == 0) begin

`        `if (cf <= 8)

`            `msb[8 - cf] <= SDA\_in; // Se almacena el byte alto

`        `else if (cf == 9) begin

`            `SDA\_en <= 1;

`            `SDA\_out <= 0; // Se envía ACK

`            `sw <= 1;

`        `end

`    `end

`    `else begin

`        `if (cf <= 8) begin

`            `SDA\_en <= 0;

`            `lsb[8 - cf] <= SDA\_in; // Se almacena el byte bajo

`        `end

`        `else if (cf == 9) begin

`            `SDA\_en <= 1;

`            `SDA\_out <= 0;

`            `S <= 3; // Ir al estado final

`        `end

`    `end

end

**¿Qué está pasando aquí?**

* Se lee el primer byte (msb).
* Luego se envía un ACK (SDA\_out = 0).
* Se lee el segundo byte (lsb).
* Se pasa al estado 3 para finalizar.

## **Estado 3: Final de transmisión**

3: begin    // Final de transmisión

`    `bc <= 0;

`    `S  <= 1'b0;

`    `sw <= 2'b00;

end

**¿Qué está pasando aquí?**

* Se limpian las variables (bc, S, sw).
* La FSM vuelve al estado 0 para empezar otra transmisión.

**¿La maquina de estados funciona?**

Para observar su funcionamiento, se utilizo un osciloscopio y asì observar el funcionamiento de la señal SCL (azul) y SDA (amarilla), en donde: Se realizaron los ajustes necesarios para el correcto funcionamiento del sistema.

La señal amarilla corresponde a SDA y la azul a SCL. Como se puede observar, la transmisión de datos comienza enviando primero la dirección del esclavo, que en este caso es **1001000**. Cada vez que SCL está en **1**, significa que se está enviando un dato.

Durante la configuración, se transmiten 7 bits de dirección seguidos por el bit de lectura/escritura (**RW**). Se envía un **1**, y posteriormente, el esclavo responde con un bit de confirmación (**ACK**) enviando un **0**, indicando que existe un dispositivo en esa dirección. Luego, se procede con el envío de los datos.

Inicialmente, en el primer ciclo:

- **SCL** = 1
- **SDA** = 0

Después, en los siguientes cuatro flancos de subida de **SCL**, **SDA** se mantiene en **1**.
` `Tras la lectura de los 8 bits de datos, se envía un bit de confirmación (**ACK**).

Este proceso se resume en que, en el primer caso, la señal es enviada por el esclavo, mientras que en el segundo caso, la transmite el maestro, que en este caso es la **FPGA**.

A continuación, se muestra la segunda secuencia, donde se observan los siguientes 8 bits de datos transmitidos después del bit de confirmación. De esta manera, se completa el envío de datos.

En nuestro proyecto, se requiere el uso de 8 bits. Sin embargo, nuestro conversor **ADS1015** tiene una resolución de 12 bits, por lo que priorizamos los 8 bits más significativos. Los últimos 4 bits, en teoría, deberían ser **0**, pero en la práctica pueden tomar valores aleatorios, lo que genera cierta variabilidad y los hace menos estables. Aun así, los primeros 8 bits cumplen con la estabilidad esperada.

![Figura 3](lab04%20imagenes/figura3.png)

***Figura 1.*** *Comprobación del I2C con el osciloscopio.*

**Diagrama RTL:**

Debido a la magnitud del diagrama rtl del i2c, este va a ser divido en partes para mostar su diseño. Para realizar este, se implemento el comando: Make rtl top=I2C. 

![Figura 4](lab04%20imagenes/figura4.png)

***Figura 2***. *Parte superior del diseño del I2C.*

![Figura 5](lab04%20imagenes/figura5.png)

***Figura 3.*** *Parte inferior del diseño del I2C.*


**Simulacion Iverilog:**

**Registro de sìntesis y place & route donde:** 

![Figura 1](lab04%20imagenes/figura6.png)

En la implementación del sistema en la FPGA iCE40, se observó un consumo eficiente de recursos. El diseño ocupa 358 de 7680 celdas lógicas (LUTs), lo que representa un 4% del total disponible. Además, no se hace uso de la memoria RAM interna (0 de 32 bloques, 0%).

Por otro lado, se observa un uso completo del único PLL disponible (1 de 1, 100%), lo cual indica que el sistema depende de este recurso para la gestión de frecuencia. También se emplea un 87% de los buffers globales de señal (SB\_GB: 8 de 8), lo que sugiere un alto uso de redes de distribución de reloj o señales críticas en el diseño.

Estos valores reflejan una utilización moderada de los recursos lógicos, permitiendo futuras expansiones, aunque el uso del PLL y los buffers globales podría requerir optimización en versiones futuras del proyecto.