---
layout: post
title: Advent of Code 2022, día 2 
tagline: Advent of code  
categories: 
- F# as your first functional programming language
tags:
- fsharp
---



[Advent of Code](https://adventofcode.com/2022/about) es una serie de ejercicios de programación que se publican antes de las navidades de cada año, de allí su 
nombre de _Advenimiento_. Preparados por [Eric Wastl](https://twitter.com/ericwastl), sus protagonistas en general son los elfos que ayudan a Papá Noel con 
la preparación de los regalos. En este ejercicio corresponde al  del [día 2 del año pasado](https://adventofcode.com/2022/day/2):

Los Elfos comienzan a establecer un campamento en la playa. Para decidir qué carpa estará más cerca del almacén de refrigerios, ya está en marcha un torneo gigante de piedra, papel o tijera.

Piedra, Papel o Tijera es un juego entre dos jugadores. Cada juego contiene muchas rondas; en cada ronda, los jugadores eligen simultáneamente piedra, papel o tijera usando una forma de mano. Luego, se selecciona un ganador para esa ronda: Piedra derrota a Tijera, Tijera derrota a Papel y Papel derrota a Piedra. Si ambos jugadores eligen la misma forma, la ronda termina en empate.

Agradecido por tu ayuda [en el ejercicio anterior](https://ibprogramacionavanzada.github.io/fsharp/63_Exercises/), un Elfo te da una guía de estrategia encriptada (tu entrada de rompecabezas)  que seguramente te ayudará a ganar. "La primera columna es lo que tu oponente va a jugar: A para Piedra, B para Papel y C para Tijera. La segunda columna..." 

De repente, el Elfo es llamado para ayudar con la tienda de alguien.

Supongamos entonces que la segunda columna debe ser la jugada en respuesta: X para Piedra, Y para Papel y Z para Tijera. Ganar cada vez sería sospechoso, por lo que las respuestas deben haber sido cuidadosamente elegidas.

El ganador de todo el torneo es el jugador con la puntuación más alta. Su puntaje total es la suma de sus puntajes para cada ronda. El puntaje de una sola ronda es el puntaje de la forma que seleccionó (1 para Piedra, 2 para Papel y 3 para Tijera) más el puntaje para el resultado de la ronda (0 si perdió, 3 si la ronda fue un empate , y 6 si se gana).

Como no se puede estar seguro de si el Elfo está tratando de ayudar o no, hay que calcular la puntuación que se obtendría si se siguiera la guía de estrategia.

Por ejemplo, dada la siguiente guía de estrategia:

A Y  
B X  
C Z  

Esta guía de estrategia predice y recomienda lo siguiente:

- En la primera ronda, tu oponente elegirá Piedra (A), por lo que se debe elegir Papel (Y). Esto termina en una victoria para uno con una puntuación de 8 (2 porque elegiste Papel + 6 porque ganaste).
- En la segunda ronda, tu oponente elegirá Papel (B), de modo tal que se debe elegir Piedra (X). Esto termina en una derrota propia con una puntuación de 1 (1 + 0).
- La tercera ronda es un empate con ambos jugadores eligiendo Tijera, lo que le da una puntuación de 3 + 3 = 6.
En este ejemplo, si siguiera la guía de estrategia, obtendría una puntuación total de 15 (8 + 1 + 6).

¿Cuál sería su puntaje total si todo sale exactamente de acuerdo con su guía de estrategia?

Luego de un rato, el elfo termina de ayudar con la tienda y vuelve a ti sigilosamente. "De todos modos, la segunda columna dice cómo debe terminar la ronda: X significa que debes perder, Y significa que debes terminar la ronda en un empate y Z significa que debes ganar. ¡Buena suerte!"

La puntuación total todavía se calcula de la misma manera, pero ahora debes averiguar qué forma elegir para que la ronda termine como se indica.

El ejemplo anterior ahora es así:

- En la primera ronda, tu oponente elegirá Piedra (A), y se necesita que la ronda termine en tablas (Y), así que también eliges Piedra. Esto le da una puntuación de 1 + 3 = 4.
- En la segunda ronda, tu oponente elegirá Papel (B) y tú eliges Piedra, por lo que pierde (X) con una puntuación de 1 + 0 = 1.
- En la tercera ronda, se deberá ganar a la Tijera de tu oponente con Piedra por una puntuación de 1 + 6 = 7.
Ahora con la guía de estrategia ultrasecreta correctamente descifrada, se obtendría una puntuación total de 12.

Siguiendo las nuevas instrucciones del duende para la segunda columna, ¿cuál sería tu puntaje total si todo sale exactamente de acuerdo con tu guía de estrategia?

El archivo completo con el inventario de caloría de los Elfos se encuentra [acá](https://adventofcode.com/2022/day/2/input).

