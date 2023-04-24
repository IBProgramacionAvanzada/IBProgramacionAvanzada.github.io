---
layout: post
title: Ejercicios
tagline: De todo un poco
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Los trenes ingleses

> Este es un dominio de verdad, tratado por [Kit Easton en su libro Stylish F#] (https://doi.org/10.1007/978-1-4842-4000-7_2), cuya lectura recomiendo. En particular, el primer capítulo es de lectura imprescindible para comprender qué significa un estilo de programación, más allá
del lenguaje usado.

Los trenes británicos, antes de pasar al sistema decimal, usaban como medidas la milla y la yarda. Una milla tiene 1760 yardas, y una 
yarda es igual a 0.9144 metros. Como si esto fuera poco, el sistema que se utilizaba para escribir las distancias era un número decimal, 
donde la parte entera es el número de millas, _y la parte fraccional el número de yardas_... Entonces, una milla y media se escribe como 
1.0880, puesto que 880 es exactamente 1760 yardas dividido dos. La parte fraccionaria entonces toma valores desde 0.0 hasta 0.1759. Siguiendo
a Easton, llamaremos a esta notación _millas.yardas_.

- Escriba funciones para convertir de la notación millas.yardas a millas decimales, y viceversa.

No contentos con esto, a veces no se utiliza las millas.yardas, sino que se usa la notación _millas y cadenas_, donde una cadena se define
como 22 yardas. 

- Escriba las funciones para convertir esta nueva notación a las anteriores.

### Manejando ventanas

En cualquier sistema operativo con interface gráfica existe el concepto de _ventanas_. Una ventana es un rectángulo de un dado ancho y un 
dado alto, medidos en pixeles, que se posiciona en algún lugar de la ventana principal que brinda el sistema, comunmente denominada _escritorio_. 
El escritorio tiene un tamaño dado también en píxeles, determinado por la resolución del monitor. Para fijar ideas, asumamos que esta 
resolución corresponde a _Full HD_, es decir, 1920 x 1080 pixeles. La esquina superior izquierda del escritorio es el origen de coordenadas,
mientras que la esquina inferior derecha corresponde a x=1919, y=1079.
La posición de la ventana se refiere a la coordenada (x,y) de su esquina superior izquierda, referida al origen de coordenadas del escritorio.

- Defina un tipo de dato para representar una ventana, y su posición en el escritorio.
- Escriba funciones para manejar las ventanas de los programas abiertos, esto es:
  
  - Crear una ventana al abrir un programa y agregarla a la colección actual de ventanas. 
  - Cerrar una ventana y removerla de la colección actual de ventanas.
  - Maximizar una ventana.
  - Minimizar una ventana.

- Las ventanas pueden estar superpuestas unas con otras, de modo tal que es necesario saber cuál ventana está más arriba que otra. Agregue
este requerimiento a los tipos de datos y funciones anteriores, modificando lo necesario.

- Para mejorar el funcionamiento, el sistema operativo no dibuja aquellas partes de las ventanas que se encuentran ocultas. Escriba funciones
  que le permitan al sistema operativo calcular la _oclusión_ entre dos ventanas, esto es, determinar el área superpuesta entre esas dos
  ventanas.
