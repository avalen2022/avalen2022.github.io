---
layout: post
title:  "P2-Rescue-drone"
date:   2025-10-23 11:00:00 +0200
categories: service-robotics
---

# **Autonomous Search and Rescue Drone**

Este proyecto presenta la implementación de un sistema de **búsqueda y rescate autónomo** para un dron, diseñado para explorar un área definida, localizar personas mediante visión artificial y regresar automáticamente a su base al finalizar la misión o ante condiciones críticas.  

El objetivo principal es garantizar una exploración sistemática y eficiente del entorno, combinando planificación geométrica, control de vuelo y detección visual mediante procesamiento de imágenes.

<div style="text-align: center;">
    <img src="/assets/images/drone_sweep.png" alt="Rescue Drone Pattern" />
</div>

## **Estrategia General**

El sistema integra varios módulos que trabajan de forma coordinada:

- **Planificación de Ruta**: Generación de una trayectoria en forma de barrido cuadrado sobre el área de búsqueda.
- **Control de Vuelo Autónomo**: Seguimiento de puntos de referencia con control de posición y orientación.
- **Visión Artificial**: Detección de rostros humanos utilizando un clasificador en cascada.
- **Gestión de Estados**: Transición automática entre fases de despegue, exploración, retorno y aterrizaje.
- **Supervisión Temporal**: Retorno automático al punto base tras un tiempo máximo de operación.

## **Planificación del Barrido**

El área de búsqueda se estructura como un cuadrado definido por una coordenada inicial y un tamaño lateral.  
El dron genera un conjunto de puntos equidistantes que cubren toda la superficie, alternando la dirección de exploración entre filas para optimizar la trayectoria y evitar desplazamientos innecesarios.  

El patrón de vuelo garantiza cobertura completa con una altura constante y un ángulo de orientación calculado hacia cada nuevo objetivo. Cada punto se considera alcanzado cuando la distancia con respecto al dron es inferior a un umbral de tolerancia.

## **Conversión Geográfica**

Las coordenadas de los puntos clave, como la posición del barco base y la zona de rescate, se definen en formato de grados, minutos y segundos, y son convertidas a coordenadas cartesianas sobre una proyección local.  
Esta conversión permite calcular distancias y direcciones precisas mediante trigonometría esférica, tomando como radio de referencia el terrestre.

## **Estados del Sistema**

El dron opera de forma secuencial según un conjunto de estados definidos:

1. **TAKEOFF**  
   El dron despega hasta una altura de operación predefinida. Dependiendo del estado de carga o si se ha activado una interrupción, pasa a planificar o a retomar la búsqueda.

2. **PLANIFICATE**  
   Se calcula la posición del área de rescate a partir de coordenadas geográficas y se genera el patrón cuadrado de exploración.

3. **SEARCH_IT**  
   El dron recorre la trayectoria planificada. Durante el vuelo, analiza las imágenes de su cámara ventral para identificar posibles personas mediante un modelo de detección de rostros.  
   Se utilizan rotaciones de la imagen y filtrados en el espacio de color HSV para reducir falsos positivos y mejorar la robustez ante variaciones de iluminación.

4. **RETURN**  
   Una vez completada la búsqueda o si se activa el temporizador de misión, el dron regresa automáticamente al punto base, siguiendo la dirección inversa de vuelo y ajustando su orientación hacia la base.

5. **LAND**  
   El dron inicia el procedimiento de aterrizaje controlado. Tras verificar la posición y mantener la estabilidad durante un tiempo mínimo, se ejecuta el aterrizaje final.

6. **DONE**  
   La misión se da por concluida cuando el número de personas detectadas alcanza el objetivo preestablecido o cuando se completa el ciclo de vuelo.  

<div style="text-align: center;">
    <img src="/assets/images/drone_states.png" alt="Drone States Diagram" />
</div>

## **Detección de Personas**

La identificación de personas se realiza mediante un clasificador Haar Cascade entrenado para detección de rostros.  
Durante la búsqueda, el dron analiza múltiples orientaciones de la imagen captada para aumentar la probabilidad de detección en distintos ángulos.  
Cada detección se valida con respecto a las posiciones ya registradas para evitar duplicados en áreas cercanas.  

Las posiciones detectadas se almacenan en una lista interna que asocia las coordenadas espaciales del dron con los puntos donde se han identificado rostros.

## **Gestión de Interrupciones y Retorno Automático**

El sistema dispone de un temporizador de seguridad que limita la duración de la misión.  
Si se supera el tiempo establecido o se detecta un evento crítico, se activa una interrupción que fuerza la transición al estado de retorno.  
Esta medida garantiza que el dron conserve suficiente batería para completar el aterrizaje con seguridad.

## **Visualización y Supervisión**

Durante la ejecución, las cámaras ventral y frontal se muestran en tiempo real mediante la interfaz WebGUI, lo que permite monitorizar tanto la navegación como la detección de personas.  
Este sistema visual facilita la validación del algoritmo, la comprobación del comportamiento del dron y la revisión de su cobertura espacial.

<div style="text-align: center;">
    <img src="/assets/images/drone_cam_feed.png" alt="Drone camera feed" />
</div>

## **Conclusiones**

El dron de búsqueda y rescate combina planificación autónoma, control de vuelo, visión artificial y supervisión en tiempo real para llevar a cabo misiones de exploración en entornos delimitados.  
Su arquitectura basada en estados facilita la extensibilidad del sistema y permite incluir nuevas condiciones o sensores sin modificar la estructura principal.  

La integración de estrategias como la planificación por barrido, el retorno automático y la detección multicriterio de rostros constituye una base sólida para futuras aplicaciones en misiones de rescate y vigilancia autónomas.