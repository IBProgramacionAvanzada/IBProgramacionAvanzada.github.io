---
layout: post
title: Ejercicios
tagline: Primeros pasos en colecciones
categories: 
- F# as your first functional programming language
tags:
- fsharp
---



### El _Kata_ del semáforo

Considere un semáforo vehicular que tiene tres luces de colores Rojo, Verde y Amarillo. Estas luces se encienden durante un cierto número de segundos
en un orden específico: Rojo -> Amarillo -> Verde -> Amarillo -> Rojo. Además siempre hay una sola luz encendida (no es posible que haya varias luces
encendidas al mismo tiempo).
A modo de ejemplo, asuma que la luz roja permanece encendida durante 45 segundos, la luz amarilla durante 2 segundos, y la luz verde durante 66 segundos,
y que inicialmente el semáforo se prende en rojo. 
En este ejercicio se propone escribir un conjunto de tipos y funciones que me permitan describir el estado del semáforo a un tiempo t dado en segundos
(esto es, no interesa por el momento modelar la evolución real del sistema en el tiempo.)

- Como primer paso, defina un tipo de dato adecuado para describir el semáforo, y analice la conveniencia de definir un tipo de dato para el tiempo.
- Analice cuáles son las transiciones de los estados de las luces, y proponga la o las funciones que permitan cambiar de estado, y las modificaciones 
  necesarias (o no) al tipo de dato que describe el semáforo.
- Escriba un tipo de dato que pueda resumir el estado del semáforo y las trancisiones. 
- En algunos casos, típicamente por la noche en lugares de poco tránsito, el semáforo se enciende la luz amarilla en forma intermitente. Agrege este nuevo 
  caso al modelo del problema, y modifique las funciones adecuadamente.

### Números naturales

Continuando con el ejemplo que vimos en clase, donde se propone un tipo de dato para describir a los números naturales al estilo de Peano como: 

```fsharp
type Peano = 
    | Zero 
    | Succ of Peano
```

Escriba funciones que permitan sumar y multiplicar dos números naturales definidos como `Peano`. 

### Siguiendo con `MyList`

Usando el tipo de dato que vimos en clase
```fsharp 
type MyList =
    | Empty
    | Cons of int * MyList 
```

- Escriba una función `length` que obtenga la longitud de la lista
- Escriba una función `rev` que reordene la lista desde el último elemento hasta el primero
```fsharp
let exampleList = Cons (1, Cons (2, Cons (3, Empty)))
// el resultado de rev exampleList debe ser
// Cons (3, Cons (2, Cons (1, Empty)))
```  

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

### Calculando $e^x$ 

Utilice recursión para calcular el valor de $e^x$  usando la serie

$$
e^x = \sum_{n=0}^{N-1} \frac{x^n}{n!}
$$

hasta un número fijo $N$ de términos. 

Suponga ahora que se quiere calcular la serie pero en lugar de calcular un número fijo de términos, se desea evaluar la función con un error relativo dado `epsilon`. Modifique
el código anterior para obtener esta función. 




