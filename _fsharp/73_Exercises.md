---
layout: post
title: Ejercicios
tagline: Leyendo datos, y volviendo a la máquina expendedora
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Nutrientes en la alimentación

> Haga el ejercicio de producir el código **sin mirar el archivo de datos!!** 

El archivo [food.csv]() extraído de [acá](https://data.nal.usda.gov/) contiene datos de nutrientes de una gran cantidad de productos de alimentación. Los datos corresponden en cada caso a 100 g de cada 
producto alimenticio. Cada fila del archivo corresponde a un producto distinto.


- ¿Cuántas categorías de nutrientes hay?

A partir de la respuesta anterior, diseñe el código de forma tal que se pueda acceder a los datos de nutrientes _sin necesidad de usar índices_. 
>> Ayuda: los diccionarios `Map...` pueden ser sus aliados.


- Valide la estructura de la información: para ello compruebe que cada una de las filas de datos tiene la información de todos los nutrientes
- Encuentre los diez alimentos con más contenido de colesterol, y los 10 con menos contenido de colesterol.
- Encuentre los alimentos que contienen pan (_bread_) y muestre su contenido de carbohidratos en una tabla
- Entre los productos con leche, encuentre aquellos que tienen el menor contenido de grasas. 

### La máquina expendedora, toma 32768...

Para completar el modelo de la máquina expendedora, necesitamos definir los inputs y outputs. Asimismo, el fabricante de la máquina expendedora nos informa que a lo sumo habrá 7 estantes, y a lo sumo 10 productos por estante. 
La máquina acepta la máquina recibe monedas de 1 doblón (equivalente a 100 centavos), 50, 25 y 10
centavos de doblón; o en tarjeta de crédito. El vuelto (de existir) también se genera en monedas.

#### Inputs

Usando el modelo que diseño en [el ejercicio de monedas](https://ibprogramacionavanzada.github.io/fsharp/14_Exercises/#monedas). 

- Extienda los tipos de datos para agregar la posibilidad de introducir monedas de 1 doblón. 

Además, el usuario debe seleccionar el producto, con una letra (correspondiente al estante), y un
número (correspondiente al producto en el estante). 

- Cree los tipos de datos para modelar este input.
- Cree una función que valide dicho input, de acuerdo a la configuración de número de estantes y número de productos por estante.
- Cree una función que de aviso al cliente de cuánto dinero falta introducir, en caso en que
  el dinero introducido no ha sido suficiente.
  
#### Outputs  

Cree las funciones que entregan el producto al usuario (descontándolo adecuadamente del stock correspondiente), y el vuelto, si lo hubiere. 

#### Extra: un caso de borde

Un caso raro, pero que podría ocurrir, es el siguiente: el usuario introduce las monedas, suficientes
como para comprar el producto que quiere, pero la máquina no tiene monedas suficientes para dar 
el vuelto.





