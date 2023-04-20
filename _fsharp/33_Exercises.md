---
layout: post
title: Ejercicios
tagline: Primeros pasos en colecciones
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

[The Programmer's Brain, Felienne Hermans](https://www.youtube.com/watch?v=FJn6_dCp-BM&ab_channel=InfoQ)

### El _Kata_ del semáforo

Considere un semáforo vehicular que tiene tres luces de colores Rojo, Verde y Amarillo. Estas luces se encienden durante un cierto número de segundos
en un orden específico: 🔴 ➡ 🟡 ➡ 🟢 ➡ 🟡 ➡ 🔴. Además siempre hay una sola luz encendida (no es posible que haya varias luces
encendidas al mismo tiempo).
A modo de ejemplo, asuma que la luz roja permanece encendida durante 45 segundos, la luz amarilla durante 2 segundos, y la luz verde durante 66 segundos,
y que inicialmente el semáforo se prende en rojo. 
En este ejercicio se propone escribir un conjunto de tipos y funciones que me permitan describir el estado del semáforo a un tiempo t dado en segundos
(esto es, no interesa por el momento modelar la evolución real del sistema en el tiempo.)

- Como primer paso, defina un tipo de dato adecuado para describir el semáforo, y analice la conveniencia de definir un tipo de dato para el tiempo.
- Analice cuáles son las transiciones de los estados de las luces, y proponga la o las funciones que permitan cambiar de estado, y las modificaciones 
  necesarias (o no) al tipo de dato que describe el semáforo.
- Escriba un tipo de dato que pueda resumir el estado del semáforo y las transiciones. 
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