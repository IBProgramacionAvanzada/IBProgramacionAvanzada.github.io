---
layout: post
title: Más sobre colecciones y tipos de datos
tagline: Un poco de mob programming 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Alumnos en materias

Considere los siguientes tipo de datos:

```fsharp 

type Career = 
    | Engineering of string 
    | Physics


type Student = 
    {
        Name: string 
        Career: Career 
    }

type Participant = 
    | NonRegular of string 
    | Regular of Student 

type Course = 
    {
        Name: string
        Participants: Participant list 
    }
```

Escriba funciones asociadas a estos tipos de datos que permitan:

- Crear un curso con una lista de estudiantes
- Agregar un estudiante a un curso
- Dar de baja un estudiante de un curso
- Cambiar la carrera de un estudiante 

Suponga ahora que se requiere modificar el tipo `Course`, para considerar la posibilidad de que un curso esté aprobado para una serie determinada de carreras, o
para todas ellas.

```fsharp

type AcceptedCareers = 
    // Your code here 

type Course = 
    {
        Name: string
        Participants: Participant list 
        AcceptedCareers: AcceptedCareers 
    }
```

- Complete el tipo de dato `AcceptedCareers` 
- Modifique las funciones de la primera parte, de modo tal que validen si un participante puede inscribirse en un curso particular, de acuerdo a si es estudiante 
  o no, y a la carrera que sigue. 


### Advent of Code 2022, día 1  

[Advent of Code](https://adventofcode.com/2022/about) es una serie de ejercicios de programación que se publican antes de las navidades de cada año, de allí su 
nombre de _Advenimiento_. Preparados por [Eric Wastl](https://twitter.com/ericwastl), sus protagonistas en general son los elfos que ayudan a Papá Noel con 
la preparación de los regalos. En este ejercicio corresponde al  del [día 1 del año pasado](https://adventofcode.com/2022/day/1):

Los Elfos tienen que ir al bosque a buscar alimento para los ciervos, que queda cruzando el mar, y tienen la gentileza de invitarlo en esta misión. El bosque está cubierto de maleza y es difícil de transitar en vehículos o acceder desde el aire; con lo cual la expedición de los Elfos tradicionalmente va a pie. A medida que sus barcos se acercan a tierra, los Elfos comienzan a hacer un inventario de sus suministros. Una consideración importante es la comida, en particular, la cantidad de calorías que lleva cada Elfo (que será su _input_ del ejercicio).

Los Elfos se turnan para anotar el número de calorías que contienen las distintas comidas, meriendas, raciones, etc. que han traído consigo, un elemento por línea. Cada Elfo separa su propio inventario del inventario del Elfo anterior (si lo hay) con una línea en blanco.

Por ejemplo, supongamos que los Elfos terminan de escribir las calorías de sus artículos y terminan con la siguiente lista:

```
1000
2000
3000

4000

5000
6000

7000
8000
9000

10000
```

Esta lista representa las calorías de la comida que llevan cinco Elfos:

- El primer Elfo lleva comida con 1000, 2000 y 3000 calorías, un total de 6000 calorías.
- El segundo elfo lleva un alimento con 4000 calorías.
- El tercer Elfo lleva comida con 5000 y 6000 calorías, un total de 11000 calorías.
- El cuarto Elfo lleva comida con 7000, 8000 y 9000 calorías, un total de 24000 calorías.
- El quinto Elfo lleva un alimento con 10000 calorías.

En caso de que los Elfos tengan hambre y necesiten refrigerios adicionales, necesitan saber cuántas calorías lleva el Elfo que lleva la mayor cantidad de calorías. En el ejemplo anterior, esto es 24000 (el cuarto Elfo).

- Encuentre al duende que lleva más calorías. ¿Cuántas calorías totales lleva ese elfo?
  
Al momento en que uno encuentr la respuesta a la pregunta de los Elfos, ya se han dado cuenta de que el Elfo que lleva la mayor cantidad de calorías de comida podría quedarse sin comida. Para evitar esta situación inaceptable, a los Elfos les gustaría saber el total de calorías que llevan los tres Elfos que llevan la mayor cantidad de calorías. De esa manera, incluso si uno de esos Elfos se queda sin bocadillos, todavía tienen dos copias de seguridad.

En el ejemplo anterior, los tres primeros Elfos son el cuarto Elfo (con 24000 calorías), luego el tercer Elfo (con 11000 calorías), luego el quinto Elfo (con 10000 calorías). La suma de las calorías que llevan estos tres duendes es 45000.

- Encuentre los tres Elfos principales que llevan la mayor cantidad de calorías. ¿Cuántas calorías llevan esos duendes en total?  

El archivo completo con el inventario de caloría de los Elfos se encuentra [acá](https://adventofcode.com/2022/day/1/input). 