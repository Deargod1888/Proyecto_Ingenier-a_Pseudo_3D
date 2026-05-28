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

---

## 🎯 Objetivos del Proyecto

El desarrollo de este motor escuela persigue una serie de metas técnicas y pedagógicas orientadas a la ingeniería de software de bajo nivel:

*   **Comprender el Software Rendering:** Aprender a renderizar entornos complejos interactivos directamente en un arreglo bidimensional de píxeles (*Framebuffer*), prescindiendo por completo de pipelines gráficos modernos (como sombreadores o *shaders* de vértices y fragmentos).
*   **Optimización Algorítmica Crítica:** Estudiar cómo matemáticos y programadores de los años 90 sustituyeron operaciones costosas de punto flotante ($float$) por aritmética de punto fijo (*fixed-point arithmetic*) y tablas de búsqueda precalculadas (*Look-Up Tables - LUTs*).
*   **Abstracción de Dimensiones:** Dominar la traslación de datos estructurados en matrices $2D$ (mapas lógicos) hacia proyecciones angulares que simulan una tercera dimensión en tiempo real.

---

## 💻 Ejemplos Prácticos de Implementación

Para entender cómo se traduce la teoría matemática a código estructurado, a continuación se presentan los núcleos algorítmicos de los dos motores incluidos en este repositorio.

### 🧱 Ejemplo 1: El Núcleo del Raycaster (Algoritmo DDA en Pseudocódigo/Python)

Este fragmento calcula la trayectoria de un rayo a través de la cuadrícula del mapa hasta colisionar con un muro, evitando el costoso muestreo paso a paso.

```python
def calcular_raycast(pos_x, pos_y, ray_dir_x, ray_dir_y, mapa):
    # Celda actual del mapa en la que se encuentra el jugador
    map_x = int(pos_x)
    map_y = int(pos_y)
    
    # Longitud del rayo desde una línea de la cuadrícula a la siguiente línea
    delta_dist_x = abs(1 / ray_dir_x) if ray_dir_x != 0 else float('inf')
    delta_dist_y = abs(1 / ray_dir_y) if ray_dir_y != 0 else float('inf')
    
    # Calcular el paso (dirección) y la distancia inicial del rayo
    if ray_dir_x < 0:
        step_x = -1
        side_dist_x = (pos_x - map_x) * delta_dist_x
    else:
        step_x = 1
        side_dist_x = (map_x + 1.0 - pos_x) * delta_dist_x

    if ray_dir_y < 0:
        step_y = -1
        side_dist_y = (pos_y - map_y) * delta_dist_y
    else:
        step_y = 1
        side_dist_y = (map_y + 1.0 - pos_y) * delta_dist_y

    # Ejecución del DDA (Bucle de salto de cuadrícula)
    impacto_muro = False
    while not impacto_muro:
        # Saltar a la siguiente celda del mapa, ya sea en dirección X o Y
        if side_dist_x < side_dist_y:
            side_dist_x += delta_dist_x
            map_x += step_x
            lado_impacto = 0 # Impacto en cara Este/Oeste
        else:
            side_dist_y += delta_dist_y
            map_y += step_y
            lado_impacto = 1 # Impacto en cara Norte/Sur
            
        # Verificar si el rayo colisionó con un muro
        if mapa[map_x][map_y] > 0:
            impacto_muro = True

    # Calcular la distancia proyectada al plano de la cámara (evita ojo de pez)
    if lado_impacto == 0:
        distancia_pared = (side_dist_x - delta_dist_x)
    else:
        distancia_pared = (side_dist_y - delta_dist_y)
        
    return distancia_pared, lado_impacto, mapa[map_x][map_y]
EJEMPLO 2 :
def proyectar_carretera(pantalla_ancho, pantalla_alto, camara_alto, horizon_y):
    lineas_carretera = {}
    
    for y in range(horizon_y, pantalla_alto):
        # Escalar la perspectiva de manera no lineal de abajo hacia arriba
        # 0.0 en el horizonte, 1.0 en la base de la pantalla
        proporcion = (y - horizon_y) / (pantalla_alto - horizon_y)
        
        # Distancia analítica basada en la altura de la cámara
        distancia = camara_alto / proporcion if proporcion > 0 else float('inf')
        
        # El ancho del camino se vuelve exponencialmente más grande cerca del jugador
        ancho_camino_real = 200 # Metros o unidades virtuales
        ancho_pantalla_linea = ancho_camino_real * (pantalla_ancho / distancia)
        
        lineas_carretera[y] = {
            "distancia": distancia,
            "ancho_render": ancho_pantalla_linea
        }
        
    return lineas_carretera
