---
layout: post
title:  "P1-Vending-Machine"
date:   2024-11-30 12:10:00 +0200
categories: sistemas-empotrados
---

### **Maquina Expendedora En Arduino UNO**

Este blog se centra en la descripción de una maquina expendedora realizada con Arduino UNO, concretamente con el kit Elegoo UNO R3.
La maquina se centra en el funcionamiento de maquina expendedora de café, pero cuenta con una estructura general compatible con ampliaciones,
o modificaciones de su uso, ya que se basa en un FSM(Finite State Machine).
### **Hardware**

<div style="text-align: center;">
    <img src="/assets/images/Maquina_expendedora.jpg" alt="Imagen de ejemplo">
</div>

### **Introducción**

En vez de realizar el código de manera estricta e intrínseca para una maquina expendedora de café, se ha estructurado de manera generalizada para que
solamente realizando cambios en sus estados la maquina cambie de comportamiento. Para ello todas las funciones de los sensores están encapsuladas en funciones
intrínsecas a su comportamiento, o threads dedicados a ellos, por ello si queremos ampliar los estados de la máquina y en ellos usar distintos sensores se puede realizar, llamando
a las funciones pertinentes o los threads pertinentes.

- **Threads:**
    - **LedThread**
        - getCounter()
        - resetCounter()
    - **IncrementLedThread**
        - resetBrightness()
    - **UltraSound**
        - getPulseTime()
        - getDistance()
        - resetDistance()
    - **Dht11**
        - getHumidity()
        - getTemperature()
- **Functions:**
    - joystick_x()
    - joystick_y()

- **Interruptions:**
    - jy_button()
    - pressed_button()

### **Estados de FSM**

<div style="text-align: center;">
    <img src="/assets/images/Vending_machine.jpg" alt="Imagen de ejemplo">
</div>

1. **START**: Estado inicial donde la máquina se está cargando.
   - **Transición**: Después de mostrar el mensaje de carga y cuando el contador de LedThread llegue a 3, pasa a `SERVICE`.

2. **SERVICE**: La máquina espera la presencia de un cliente.
   - **Transición**: Si el sensor ultrasónico detecta un cliente a una distancia menor a `CM_DISTANCE`, la máquina pasa a `MEASURES`.

3. **MEASURES**: Estado donde se muestran las medidas (temperatura y humedad).
   - **Transición**: Después de mostrar las medidas, pasa a `ATTENDING` cuando pasa `S_MEASURES*1000`.

4. **ATTENDING**: Estado donde se muestra el menú de café para que el cliente elija.
   - **Transición**: El cliente selecciona el café con el joystick (arriba o abajo), si presiona el joystick la máquina transita a `SERVING`.

5. **SERVING**: Estado en el que la máquina prepara la bebida seleccionada.
   - **Transición**: Después de un tiempo aleatorio de preparación (entre 4-8 segundos), la máquina pasa a `ATTENDED`.

6. **ATTENDED**: Estado final donde se indica que la bebida está lista para ser retirada.
   - **Transición**: Después de que el cliente retire la bebida, cuando pasa `S_DRINK*1000`, la máquina vuelve a `SERVICE`.

7. **ADMIN**: El menú de administración donde se pueden ver los datos de los sensores, los segundos de la placa o modificar precios.
   - **Transición de llegada**: Manteniendo el botón más de `MIN_S_ADMIN * 1000`.
   - **Transiciónes**: Dependiendo de la opción seleccionada en el menú, puede pasar a `ADMIN_I`, `ADMIN_II`, `ADMIN_III`, o `ADMIN_IV`, seleccionando con joystick,
   - **Transición de salida**: Manteniendo el botón más de `MIN_S_ADMIN * 1000`.
8. **ADMIN_I a ADMIN_IV**: Sub-estados del menú de administración donde se pueden ver o modificar configuraciones.
   - **Transición**: De vuelta a `ADMIN` si se navega hacia atrás, o a `SERVICE` si se mantiene el botón más de `MIN_S_ADMIN * 1000`.

9. **EXIT**: Estado de salida, sin uso.

### **Caracteristicas Destacadas**

#### **1. Estado START**

En el estado de arranque nos encontramos una función `start_machine()`, el cual realiza la representación, en el LCD, de CARGANDO y una serie de puntos que
dan dinamismo a la representación. Lo más característico de este estado es que controlamos el tiempo que tenemos que cargar mediante el parpadeo de un led.
Por ello hemos arrancado un thread `LedThread` que en `run()` solamente hace la labor de cambio de estado de HIGH a LOW, y cada vez que hace una transición cuenta 1
a su contador interno, y llamando a su método `ledThread1->getCounter()` vemos cuantas veces a parpadeado y si tenemos que cambiar de estado.

<div style="text-align: center;">
    <img src="/assets/Cargando_empotrados(1).gif" alt="Animación de ejemplo">
</div>

#### **2. Movimientos Joystick**

El joystick cumple un papel principal en la máquina, la comunicación cliente maquina, gracias a el el cliente puede "comunicarse" con la maquina. Al ser un rol tan importante
he tratado de darle el máximo dinamismo posible, por ello tenemos dos funciones principales `joystick_x()`/ `joystick_y()` las cuales marcan la dirección del movimiento siendo
arriba y abajo gracias al eje **y**, y derecha e izquierda gracias al eje **x**.

<div style="display: flex; justify-content: space-between;">
    <div style="flex: 0 48%;">
        <pre><code>
// 1 is up, -1 is down, 0 no moved
int joystick_y() {
  int index = 0;
  int vy = analogRead(joy_y_pin);
  if (vy > 800) {
    return 1;
  } else if (vy < 300) {
    return -1;
  }
  return 0;
}
        </code></pre>
    </div>
    <div style="flex: 0 48%;">
        <pre><code>
// -1 is left, +1 is right, 0 no moved
int joystick_x() {
  int index = 0;
  int vx = analogRead(joy_x_pin);
  if (vx > 800) {
    return 1;
  } else if (vx < 300) {
    return -1;
  }
  return 0;
}
        </code></pre>
    </div>
</div>

Además para darle el dinamismo deseado podemos scrollear hacia arriba y abajo manteniendo el joystick en una dirección, pero para ello hay que 
realizar esperas entre movimiento y movimiento ya que si no al iterar tan rápido no seriamos capaces de controlarlo. Por ello cuando usamos
el joystick, principalmente en nuestro caso arriba y abajo `joystick_y()`, hacemos realizamos esperas definidas en `S_JOY_REACT*1000`.

```c
  if (millis() - time_ >= (S_JOY_REACT*1000)) {
    m_index = joystick_y();
    time_ = millis();
  }
```

<div style="text-align: center;">
    <img src="/assets/Joystick_empotrados.gif" alt="Animación de ejemplo">
</div>

#### **3. Sensor De Humedad/Temperatura**

Para tratar este sensor hemos tenido que importar una librería, `DHT sensor library` dedicada a nuestro sensor, el sensor **DHT11**. Esta librería nos facilita el uso
del sensor mediante un tipo de dato `DHT(DHTPIN, DHTYPE)`, que al pasarle el pin y el tipo de sensor, en nuestro caso DHT11, ya nos establece el sensor. Cuando llamamos
a su método `begin()` lo configuramos de la manera correcta, permitiéndonos su uso. A partir de ahí podemos realizar las llamadas sus métodos  `dht->readHumidity()` y 
`dht->readTemperature()`.

Para facilitar su uso, esta integrado todo en una clase Dht11, que se trata de un thread, el cual al arrancarlo y seleccionar su intervalo de recopilación de datos 
el sensor guardará los datos en unos atributos de su clase, y para extraer su valores están los métodos:
- **getHumidity()**
- **getTemperature()**

<div style="text-align: center;">
    <img src="/assets/DHT11_empotradis.gif" alt="Animación de ejemplo">
</div>

#### **4. Sensor De UltraSonido**

El funcionamiento del sensor ultrasónico **HC-SR04** se basa en dos pines principales: **Trigger** y **Echo**, que se encargan de emitir y recibir las ondas ultrasónicas, respectivamente. Para su operación, se envía un valor `HIGH` al pin **Trigger** durante 10 microsegundos para emitir una onda ultrasónica. Luego, se cambia a `LOW` para dejar de emitir. Posteriormente, se utiliza la función `pulseIn(ECHOPIN, HIGH)` para medir el tiempo que tarda la onda en regresar, lo que corresponde al ancho de pulso.

Con este valor, se puede calcular la distancia al objeto utilizando la fórmula:

**distancia = (t_pulso * 0.034) / 2**

Donde:
- **t_pulso** es el tiempo medido en microsegundos.
- **0.034** es la velocidad del sonido en el aire (en cm/µs).
- El factor **2** se debe a que el tiempo medido es el de ida y vuelta de la señal.

<div style="text-align: center;">
    <img src="/assets/UltraSonido_empotrados.gif" alt="Animación de ejemplo">
</div>


#### **5. Modificación De Precios**

Cuando se arranca el programa hay un precios predefinidos pero estos precios pueden ser modificados en el estado `ADMIN_IV`, al cual se accede desde `ADMIN`.
En este **"subestado"** del **ADMIN**, se hace uso de todos los ejes del **joystick**: 
- **x**: Para movernos hacia atrás(izq), ya sea volver a `ADMIN` o volver a la selección del café que queremos modificar su precio, o hacia adelante(drch) para seleccionar el apartado precio y modificarlo.

- **y**: Para scrollear arriba y abajo, y así elegir el café correspondiente o modificar subiendo bajando su precio.

Además cuenta con dinamismo de imagen, ya que al seleccionar el precio este parpadea como símbolo de elección.

<div style="text-align: center;">
    <img src="/assets/Modificar_precio.gif" alt="Animación de ejemplo">
</div>

#### **6. Interrupciones De Botones**

La maquina cuenta con dos interrupciones, tanto el botón del **joystick** como el **botón** común. Ambos gestionados mediante circuito PULL_UP, nos marcan
si se ha presionado o no los respectivos botones. Estos botones solo tienen uso en distintos estados, ya sea:
- **Joystick button**: Solo tiene uso para seleccionar el café deseado, y seleccionar la información deseada del `ADMIN`.
- **Common button**: Su uso es durante todo el código, y dicta si se accede a la función de `ADMIN`, o se sale, o si has avanzado más del estado `SERVICE` para poder salir y esperar a un cliente.

### **Videos**

- **Vending Machine**:
<div style="text-align: center;">
<iframe width="560" height="315" src="https://www.youtube.com/embed/roQuHYqQfU8" frameborder="0" allowfullscreen></iframe>
</div>

### **Conclusión**

El uso de *threads* para organizar las tareas de manera más limpia y estructurada facilita el entendimiento del código y distribuye las funciones de forma eficiente. Aunque las operaciones de los *threads* sean secuenciales, la concurrencia que ofrecen da la impresión de que se ejecutan simultáneamente, mejorando la percepción de fluidez del sistema.

Además, el uso de interrupciones en los botones permite que la máquina nunca esté detenida ni realizando espera activa para comprobar estados, ya que las propias interrupciones se encargan de gestionar los cambios necesarios de manera eficiente.

Finalmente, el empleo de un *watchdog* proporciona una solución robusta para evitar bloqueos de la máquina. Gracias a la facilidad que ofrecen los *threads* y su funcionamiento concurrente, es posible utilizar tiempos de comprobación más cortos, minimizando el uso de funciones como `delay()` y mejorando la reactividad del sistema.

```c
  wdt_disable();
  wdt_enable(WDTO_1S);
  ...
  ...
  wdt_reset();
```
