# Proyecto_Ingenier-a_Pseudo_3D
# 🎮 Ingeniería Pseudo-3D: Fundamentos, Algoritmos y Arquitectura de Motores Retro

¡Bienvenido al repositorio definitivo de **Ingeniería Pseudo-3D**! Este espacio está dedicado al estudio, documentación e implementación desde cero (*from scratch*) de las técnicas geométricas y matemáticas que permitieron la revolución de los gráficos tridimensionales en las computadoras de los años 80 y 90, mucho antes de la llegada de las GPUs modernas y las APIs de hardware como OpenGL o DirectX.

Aquí exploramos cómo "engañar" al ojo humano proyectando mundos bidimensionales ($2D$) en pantallas planas a través de la manipulación analítica de píxeles, simulando profundidad, perspectiva y movimiento espacial.

---

## 🗺️ Tabla de Contenidos
1. [¿Qué es la Ingeniería Pseudo-3D? (2.5D)](#-qué-es-la-ingeniería-pseudo-3d-25d)
2. [El Motor de Raycasting (Estilo Wolfenstein 3D)](#-el-motor-de-raycasting-estilo-wolfenstein-3d)
3. [El Motor de Carreras (Estilo OutRun)](#-el-motor-de-carreras-estilo-outrun)
4. [Fundamentos Matemáticos Detallados](#-fundamentos-matemáticos-detallados)
5. [Texturizado y Técnicas de Escaneo (Scanlines)](#-texturizado-y-técnicas-de-escaneo-scanlines)
6. [Estructura del Repositorio](#-estructura-del-repositorio)
7. [Cómo Empezar y Requisitos](#-cómo-empezar-y-requisitos)

---

## 🎨 ¿Qué es la Ingeniería Pseudo-3D? (2.5D)

A diferencia de un motor 3D moderno (donde los objetos poseen coordenadas tridimensionales complejas $[X, Y, Z]$ y sufren transformaciones de matrices en un espacio virtual), un motor **Pseudo-3D** opera bajo un conjunto estricto de restricciones matemáticas para mantener el rendimiento al límite. 

### El Gran Truco: Reducción de Dimensiones
La mayoría de estos entornos se definen internamente en **dos dimensiones ($2D$)**, pero se renderizan aplicando leyes de perspectiva lineal. El procesador ($CPU$) dibuja la pantalla línea por línea o columna por columna de manera puramente bidimensional.

```text
  [ Mundo Real 3D ]                 [ Mundo Pseudo-3D (2.5D) ]
   X, Y, Z libres                    Base 2D + Altura Fija/Proyectada
       
       /\   / \                         +---+---+---+---+   <- Matriz 2D
      /  \_/   \                        | . |   | # |   |      (Eje X, Y)
     / _/   \_  \                       +---+---+---+---+
    /_/       \_ \                      |   | P |   | # |   P = Jugador
   /____________\ \                     +---+---+---+---+   # = Muro
