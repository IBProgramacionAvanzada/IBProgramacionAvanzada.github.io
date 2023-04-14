---
layout: post
title: Ejercicios
tagline: Manejando el flujo
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Volviendo a `FizzBuzz` 

Ahora puede volver al [ejercicio de FizzBuzz]({% link _fsharp/03_Exercises.md %}### FizzBuzz _Kata_) y comenzar
a resolverlo en F#. Construir una función que dado un número, imprima Fizz para múltiplos de tres, Buzz para números múltiplos de cinco,
y además, si el número es múltiplo de tres y cinco, debe imprimir FizzBuzz.

### Puntos cardinales

Construya un tipo de dato que represente los puntos cardinales principales (N,S,E,O) y los intermedios (NE,NO,SE,SO).
Defina una función que permita determinar hacia qué punto cardinal un vehículo se está dirigiendo, dado el rumbo del mismo,
definido como un ángulo en el rango  0 y 359 grados.

### Volviendo al truco

Retomando el [ejercicio del truco]({% link _fsharp/14_Exercises.md %}### Poker (o Truco)):

El _envido_  en el truco requiere en tener dos cartas del mismo palo, de las tres cartas de la mano. 
Asumiendo que se tienen 2 cartas del mismo palo, se suma su valor + 20 (de ahí la importancia de ligar dos cartas del mismo palo). La puntuación más alta del envido es 33 (7+6+20=33).
Las figuras o cartas negras (sota, caballo y rey) valen 0 puntos para el envido. Si se tienen 2 figuras del mismo palo la puntuación de envido es 20.
Recuerde que en el truco, los ochos y nueves no forman parte del mazo.

- Escriba una función que dadas tres cartas, determine si es posible formar un envido.
- Escriba una función que dadas dos cartas, retorne el valor del envido, o algun tipo de error si no tiene envido con dichas cartas.

