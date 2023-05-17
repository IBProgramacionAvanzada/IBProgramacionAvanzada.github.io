---
layout: post
title: Ejercicios de tipos 
tagline: Unos ejercicios livianos para empezar a trabajar con dominios.
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Unos ejercicios livianos para empezar a trabajar con dominios.

### El video de la semana

[_The Art of Code_, Dylan Beattie](https://youtu.be/6avJHaC3C2U)

### Monedas

En algún momento, un cliente ingresará cierta cantidad de cada moneda para comprar un producto en la máquina expendedora de alimentos. La máquina recibe monedas de 10, 25 y 50 centavos únicamente.

- Modelar un tipo `Monedas` que dé cuenta del número de cada una de las monedas que recibe la máquina.
- Crear una función `cantidad` que reciba un valor de `Monedas` y devuelva la cantidad en $\$$ que el cliente introdujo en la máquina.

> 💡 La función `float` se puede usar para convertir un valor entero a un tipo flotante

### Lugares en la máquina expendedora

El vendedor de la máquina expendedora de alimentos proporciona a la máquina diferentes lugares en cada estante para acomodar productos que tienen diferentes tamaños. El vendedor ofrece tres tamaños diferentes, simple, doble y triple.

- Modelar los tamaños de los lugares en un tipo `SlotSize`.
- Extienda el modelo `FoodMachineItem` para agregar el tamaño del producto medido por el tamaño de la lugar que ocupa.

### Maquina de café

Nuestro cliente de máquinas expendedoras quiere agregar una máquina de café justo al lado de la máquina expendedora de alimentos y quiere que preparemos el software para administrarla. Como conversación de primera ronda, nos han informado que la cafetera servirá café y té negro. A ambos se les puede agregar azúcar y crema. También tendrá chocolate y capuchino, con la única opción de agregar azúcar. Finalmente, también servirá un saludable té verde o blanco que se puede cubrir solo con leche. Comience a modelar la máquina de café introduciendo los tipos para describir todas estas bebidas.

### Poker (o Truco)

- Usando uniones discriminadas y/o records, modelar un mazo de cartas francesas (poker) o españolas (truco), sin comodines.
- Cómo cambiaría el modelo anterior si se incluyen dos comodines por mazo?