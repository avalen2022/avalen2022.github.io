---
layout: post
title: "P3-Autonomous-Parking"
date: 2025-11-07 11:00:00 +0200
categories: service-robotics
---

# **Controlador Autónomo de Estacionamiento**

Este proyecto describe la implementación de un sistema completo para estacionamiento autónomo aplicado a robots móviles terrestres. El objetivo principal es permitir al robot desplazarse por el entorno, detectar un espacio libre a su derecha, alinearse con él y ejecutar una maniobra de estacionamiento completamente automática sin intervención humana.

La arquitectura combina percepción basada en láser, control reactivo continuo sobre la orientación, y una máquina de estados encargada de seleccionar el comportamiento adecuado en cada fase de la maniobra.

## **Estrategia General**

El sistema trabaja de forma continua leyendo los sensores láser frontal, lateral derecho y posterior.  
A partir de esos datos se realiza:

- Corrección de posición lateral y orientación  
- Detección de huecos aptos en el lateral derecho  
- Clasificación del contexto de bloqueo lateral  
- Transiciones entre fases del estacionamiento según patrones geométricos

El control se ejecuta publicando velocidades lineales y angulares mediante HAL.

## **Gestión de Sensores**

Los láseres se procesan filtrando infinitos, limpiando valores inválidos y extrayendo solo el rango útil cercano al robot.  
Esto permite convertir la nube completa de valores en información relevante como:

- Distancia lateral real al obstáculo  
- Cercanía frontal o trasera  
- Ángulos con mínimos locales para detectar extremos útiles geométricamente

## **Corrección de Lateralidad**

El robot mantiene su posición lateral estable mientras circula buscando plaza.  
Se combinan diferencias entre frontal, posterior y rango derecho central para mantener la trayectoria paralela a la calle de forma continua y reactiva.

## **Clasificación del Entorno**

Cuando el sistema detecta que existe una plaza potencial a la **derecha** del robot, se analiza el contexto lateral para entender qué tipo de maniobra debe ejecutarse.

Lo que se determina es si al lado derecho existen vehículos:

- Delante del hueco  
- Detrás del hueco  
- En ambos lados del hueco

Esta clasificación permite seleccionar la variante correcta de secuencia de estacionamiento, adaptando correcciones y dirección de giro según el escenario.

## **Máquina de Estados**

La maniobra completa se estructura en varias fases:

1. **SEARCHING**  
   Circula recto y regulado lateralmente hasta encontrar una plaza apta según láser derecho.

2. **MOVE_CARS**  
   Espera estable antes de iniciar la alineación para permitir movimiento externo de coches o estabilización del entorno.

3. **ALIGN**  
   Ajuste inicial de posición antes de iniciar la maniobra.

4. **F_PARKING**  
   Primera fase de entrada hacia el interior del hueco.

5. **S_PARKING**  
   Corrección inversa y profundización del estacionamiento formando la “S”.

6. **S_ALIGN**  
   Alineación fina interna hasta estar completamente dentro.

7. **FINISHED**  
   Estacionamiento completado.

Todas las transiciones dependen exclusivamente de observación láser, no requiere mapas, ni referencia absoluta.

## **Test**

El sistema también permite validar comportamiento sin depender de detección real del entorno.  
A través de la variable interna `test_env_` se puede forzar manualmente el tipo de escenario lateral que el robot cree estar percibiendo.

De esta forma se puede probar y depurar cada caso de maniobra sin necesidad de mover coches ni cambiar la configuración de la escena:

- `[1,0]` fuerza caso donde hay un vehículo delante del hueco derecho  
- `[0,1]` fuerza caso donde hay un vehículo detrás  
- `[1,1]` indica que existen vehículos tanto delante como detrás de la plaza  

Esto acelera el proceso de verificación y permite ajustar parámetros antes de realizar validaciones en escenarios reales o más complejos.

## **Video**

**Both cars**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

**Front car**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park_1front.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

**Back car**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park_1back.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

## **Conclusión**

Este controlador demuestra un enfoque práctico para estacionamiento automático en robots móviles.  
Su diseño reactivo, basado en percepción directa y máquina de estados, permite comportamientos robustos y extensibles sin planificación global.

Su simplicidad estructural lo hace ideal para aplicaciones de AGV, robots urbanos experimentales y validación de conceptos industriales de navegación autónoma urbana.

