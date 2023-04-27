---
layout: post
title: Dos Katas clásicos
tagline: Un poco de mob programming 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

[Railway oriented programming, Scott Wlaschin](https://www.youtube.com/watch?v=fYo3LN9Vf_M&ab_channel=TechTrain)

### El _Kata_ del tenis

El propósito de este _kata_ es modelar el puntaje durante un partido de tenis. El puntaje se cuenta de la siguiente 
forma:

- Un juego lo gana el primer jugador que ha ganado al menos cuatro puntos en total y al menos dos puntos más que el oponente.
- La puntuación actual de cada juego se describe de una manera peculiar del tenis: las puntuaciones de cero a tres puntos se describen como "cero" (_love_), "15", "30" y "40", respectivamente.
- Si cada jugador ha anotado al menos tres puntos y los puntajes son iguales, el puntaje es "iguales" (_deuce_).
- Si cada lado ha anotado al menos tres puntos y un jugador tiene un punto más que su oponente, el puntaje del juego se dice que es "ventaja" (_advantage_) para el jugador que va en cabeza.


