---
layout: post
title: El Kata de la Rosa Dorada
tagline: Tests y refactoring
categories: 
- F# as your first functional programming language
tags:
- fsharp
---


El presente _kata_ es un ejercicio ya clásico de _refactoring_: tenemos un programa que funciona, pero es necesario agregarle un caso nuevo de uso. Por razones que se explican por sí solas al leer el programa, se requiere reescribir parte del mismo.

Originalmente concebido por [@TerryHughes](https://twitter.com/TerryHughes) y [@NotMyself](https://twitter.com/NotMyself) como un ejercicio de [_refactoring_ en C#](https://github.com/NotMyself/GildedRoseCore), [fue extendido a distintos lenguajes](https://github.com/emilybache/GildedRose-Refactoring-Kata.git) e incluído en el libro [Coding Dojo](https://leanpub.com/codingdojohandbook) por Emily Bache.

En este notebook encontrará 
- La descripción original de las especificaciones del código, los requerimientos y el nuevo caso de uso.
- El código propiamente dicho como para explorar.

La misión es
- Escribir tests para probar que efectivamente el código está funcionando para los casos de uso para los que fue diseñado.
- Escribir nuevos tests para abordar el nuevo caso (artículos `conjurados`).
- Hacer el refactoring. 

para ello se provee de [este repositorio](https://github.com/fcolavecchia/GildedRose-Kata-initial-fsharp.git) que contiene tres proyectos
- `GildedRose` con el programa original.
- `GildedRose.UnitTests` donde podrá escribir los tests unitarios.
- `GildedRose.ApprovalTests` que contiene el test de aprobación del código (para los casos de uso originales.)
  
Haga el fork correspondiente y happy refactoring!  

## Especificaciones de la Rosa Dorada (Gilded Rose)

Bienvenido al equipo de **Gilded Rose**.
Como quizá sabes, somos una pequeña posada ubicada estratégicamente en una prestigiosa ciudad, atendida por la amable **Allison**.
También compramos y vendemos mercadería de alta calidad.
Por desgracia, nuestra mercadería va bajando de calidad a medida que se aproxima la fecha de venta.

Tenemos un sistema instalado que actualiza automáticamente el `inventario`.
Este sistema fue desarrollado por un muchacho con poco sentido común llamado Leeroy, que ahora se dedica a nuevas aventuras.
Tu tarea es agregar una nueva característica al sistema para que podamos comenzar a vender una nueva categoría de items.

## Descripción preliminar

Pero primero, vamos a introducir el sistema:

* Todos los artículos (`Item`) tienen una propiedad `sellIn` que denota el número de días que tenemos para venderlo
* Todos los artículos tienen una propiedad `quality` que denota cúan valioso es el artículo
* Al final de cada día, nuestro sistema decrementa ambos valores para cada artículo mediante el método `updateQuality`

Bastante simple, ¿no? Bueno, ahora es donde se pone interesante:

* Una vez que ha pasado la fecha recomendada de venta, la `calidad` se degrada al doble de velocidad
* La `calidad` de un artículo nunca es negativa
* El "Queso Brie envejecido" (`Aged brie`) incrementa su `calidad` a medida que se pone viejo
  * Su `calidad` aumenta en `1` unidad cada día
  * luego de la `fecha de venta` su `calidad` aumenta `2` unidades por día
* La `calidad` de un artículo nunca es mayor a `50`
* El artículo "Sulfuras" (`Sulfuras`), siendo un artículo legendario, no modifica su `fecha de venta` ni se degrada en `calidad`
* Una "Entrada al Backstage", como el queso brie, incrementa su `calidad` a medida que la `fecha de venta` se aproxima
  * si faltan 10 días o menos para el concierto, la `calidad` se incrementa en `2` unidades
  * si faltan 5 días o menos, la `calidad` se incrementa en `3` unidades
  * luego de la `fecha de venta` la `calidad` cae a `0`

## El requerimiento

Hace poco contratamos a un proveedor de artículos *conjurados mágicamente*.
Esto requiere una actualización del sistema:

* Los artículos `conjurados` degradan su `calidad` al doble de velocidad que los normales

Siéntete libre de realizar cualquier cambio al mensaje `updateQuality` y agregar el código que sea necesario, mientras que todo siga funcionando correctamente. Sin embargo, **no alteres el objeto `Item` ni sus propiedades** ya que pertenecen al goblin que está en ese rincón, que en un ataque de ira te va a liquidar de un golpe porque no cree en la cultura de código compartido.

## Notas finales

Para aclarar: un artículo nunca puede tener una `calidad` superior a `50`, sin embargo las Sulfuras siendo un artículo legendario posee una calidad inmutable de `80`.

## El código

```fsharp
open System.Collections.Generic

type Item = 
    {
        Name: string
        SellIn: int 
        Quality: int 
    }
```

```fsharp
let Items = new List<Item>()
Items.Add({Name = "+5 Dexterity Vest"; SellIn = 10; Quality = 20})
Items.Add({Name = "Aged Brie"; SellIn = 2; Quality = 0})
Items.Add({Name = "Elixir of the Mongoose"; SellIn = 5; Quality = 7})
Items.Add({Name = "Sulfuras, Hand of Ragnaros"; SellIn = 0; Quality = 80})
Items.Add({Name = "Sulfuras, Hand of Ragnaros"; SellIn = -1; Quality = 80})
Items.Add({Name = "Backstage passes to a TAFKAL80ETC concert"; SellIn = 15; Quality = 20})
Items.Add({Name = "Backstage passes to a TAFKAL80ETC concert"; SellIn = 10; Quality = 49})
Items.Add({Name = "Backstage passes to a TAFKAL80ETC concert"; SellIn = 5; Quality = 49})
Items.Add({Name = "Conjured Mana Cake"; SellIn = 3; Quality = 6})
```

```fsharp
type GildedRose(items:IList<Item>) =
    let Items = items

    member this.UpdateQuality() =
        for i = 0 to Items.Count - 1 do
            if Items.[i].Name <> "Aged Brie" && Items.[i].Name <> "Backstage passes to a TAFKAL80ETC concert" then
                if Items.[i].Quality > 0 then
                    if Items.[i].Name <> "Sulfuras, Hand of Ragnaros" then
                        Items.[i] <- { Items.[i] with Quality = (Items.[i].Quality - 1) } 
            else
               if Items.[i].Quality < 50 then
                    Items.[i] <- { Items.[i] with Quality = (Items.[i].Quality + 1) } 
                    if Items.[i].Name = "Backstage passes to a TAFKAL80ETC concert" then
                        if Items.[i].SellIn < 11 then
                            if Items.[i].Quality < 50 then
                                Items.[i] <- { Items.[i] with Quality = (Items.[i].Quality + 1) } 
                        if Items.[i].SellIn < 6 then
                            if Items.[i].Quality < 50 then
                                Items.[i] <- { Items.[i] with Quality = (Items.[i].Quality + 1) } 
            if Items.[i].Name <> "Sulfuras, Hand of Ragnaros" then                 
                Items.[i] <- { Items.[i] with SellIn  = (Items.[i].SellIn - 1) } 
            if Items.[i].SellIn < 0 then
                if Items.[i].Name <> "Aged Brie" then
                    if Items.[i].Name <> "Backstage passes to a TAFKAL80ETC concert" then
                        if Items.[i].Quality > 0 then
                            if Items.[i].Name <> "Sulfuras, Hand of Ragnaros" then
                                Items.[i] <- { Items.[i] with Quality   = (Items.[i].Quality  - 1) } 
                    else
                        Items.[i] <- { Items.[i] with Quality   = (Items.[i].Quality  - Items.[i].Quality) } 
                else
                    if Items.[i].Quality < 50 then
                        Items.[i] <- { Items.[i] with Quality   = (Items.[i].Quality + 1) }  
        ()

```

```fsharp
let app = new GildedRose(Items)
for i = 0 to 30 do
    printfn "-------- day %d --------" i
    printfn "name, sellIn, quality"
    for j = 0 to Items.Count - 1 do
            printfn "%s, %d, %d" Items.[j].Name Items.[j].SellIn Items.[j].Quality
    printfn ""
    app.UpdateQuality()
```

### El output

```
    -------- day 0 --------
    name, sellIn, quality
    +5 Dexterity Vest, 10, 20
    Aged Brie, 2, 0
    Elixir of the Mongoose, 5, 7
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 15, 20
    Backstage passes to a TAFKAL80ETC concert, 10, 49
    Backstage passes to a TAFKAL80ETC concert, 5, 49
    Conjured Mana Cake, 3, 6
    
    -------- day 1 --------
    name, sellIn, quality
    +5 Dexterity Vest, 9, 19
    Aged Brie, 1, 1
    Elixir of the Mongoose, 4, 6
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 14, 21
    Backstage passes to a TAFKAL80ETC concert, 9, 50
    Backstage passes to a TAFKAL80ETC concert, 4, 50
    Conjured Mana Cake, 2, 5
    
    -------- day 2 --------
    name, sellIn, quality
    +5 Dexterity Vest, 8, 18
    Aged Brie, 0, 2
    Elixir of the Mongoose, 3, 5
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 13, 22
    Backstage passes to a TAFKAL80ETC concert, 8, 50
    Backstage passes to a TAFKAL80ETC concert, 3, 50
    Conjured Mana Cake, 1, 4
    
    -------- day 3 --------
    name, sellIn, quality
    +5 Dexterity Vest, 7, 17
    Aged Brie, -1, 4
    Elixir of the Mongoose, 2, 4
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 12, 23
    Backstage passes to a TAFKAL80ETC concert, 7, 50
    Backstage passes to a TAFKAL80ETC concert, 2, 50
    Conjured Mana Cake, 0, 3
    
    -------- day 4 --------
    name, sellIn, quality
    +5 Dexterity Vest, 6, 16
    Aged Brie, -2, 6
    Elixir of the Mongoose, 1, 3
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 11, 24
    Backstage passes to a TAFKAL80ETC concert, 6, 50
    Backstage passes to a TAFKAL80ETC concert, 1, 50
    Conjured Mana Cake, -1, 1
    
    -------- day 5 --------
    name, sellIn, quality
    +5 Dexterity Vest, 5, 15
    Aged Brie, -3, 8
    Elixir of the Mongoose, 0, 2
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 10, 25
    Backstage passes to a TAFKAL80ETC concert, 5, 50
    Backstage passes to a TAFKAL80ETC concert, 0, 50
    Conjured Mana Cake, -2, 0
    
    -------- day 6 --------
    name, sellIn, quality
    +5 Dexterity Vest, 4, 14
    Aged Brie, -4, 10
    Elixir of the Mongoose, -1, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 9, 27
    Backstage passes to a TAFKAL80ETC concert, 4, 50
    Backstage passes to a TAFKAL80ETC concert, -1, 0
    Conjured Mana Cake, -3, 0
    
    -------- day 7 --------
    name, sellIn, quality
    +5 Dexterity Vest, 3, 13
    Aged Brie, -5, 12
    Elixir of the Mongoose, -2, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 8, 29
    Backstage passes to a TAFKAL80ETC concert, 3, 50
    Backstage passes to a TAFKAL80ETC concert, -2, 0
    Conjured Mana Cake, -4, 0
    
    -------- day 8 --------
    name, sellIn, quality
    +5 Dexterity Vest, 2, 12
    Aged Brie, -6, 14
    Elixir of the Mongoose, -3, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 7, 31
    Backstage passes to a TAFKAL80ETC concert, 2, 50
    Backstage passes to a TAFKAL80ETC concert, -3, 0
    Conjured Mana Cake, -5, 0
    
    -------- day 9 --------
    name, sellIn, quality
    +5 Dexterity Vest, 1, 11
    Aged Brie, -7, 16
    Elixir of the Mongoose, -4, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 6, 33
    Backstage passes to a TAFKAL80ETC concert, 1, 50
    Backstage passes to a TAFKAL80ETC concert, -4, 0
    Conjured Mana Cake, -6, 0
    
    -------- day 10 --------
    name, sellIn, quality
    +5 Dexterity Vest, 0, 10
    Aged Brie, -8, 18
    Elixir of the Mongoose, -5, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 5, 35
    Backstage passes to a TAFKAL80ETC concert, 0, 50
    Backstage passes to a TAFKAL80ETC concert, -5, 0
    Conjured Mana Cake, -7, 0
    
    -------- day 11 --------
    name, sellIn, quality
    +5 Dexterity Vest, -1, 8
    Aged Brie, -9, 20
    Elixir of the Mongoose, -6, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 4, 38
    Backstage passes to a TAFKAL80ETC concert, -1, 0
    Backstage passes to a TAFKAL80ETC concert, -6, 0
    Conjured Mana Cake, -8, 0
    
    -------- day 12 --------
    name, sellIn, quality
    +5 Dexterity Vest, -2, 6
    Aged Brie, -10, 22
    Elixir of the Mongoose, -7, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 3, 41
    Backstage passes to a TAFKAL80ETC concert, -2, 0
    Backstage passes to a TAFKAL80ETC concert, -7, 0
    Conjured Mana Cake, -9, 0
    
    -------- day 13 --------
    name, sellIn, quality
    +5 Dexterity Vest, -3, 4
    Aged Brie, -11, 24
    Elixir of the Mongoose, -8, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 2, 44
    Backstage passes to a TAFKAL80ETC concert, -3, 0
    Backstage passes to a TAFKAL80ETC concert, -8, 0
    Conjured Mana Cake, -10, 0
    
    -------- day 14 --------
    name, sellIn, quality
    +5 Dexterity Vest, -4, 2
    Aged Brie, -12, 26
    Elixir of the Mongoose, -9, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 1, 47
    Backstage passes to a TAFKAL80ETC concert, -4, 0
    Backstage passes to a TAFKAL80ETC concert, -9, 0
    Conjured Mana Cake, -11, 0
    
    -------- day 15 --------
    name, sellIn, quality
    +5 Dexterity Vest, -5, 0
    Aged Brie, -13, 28
    Elixir of the Mongoose, -10, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, 0, 50
    Backstage passes to a TAFKAL80ETC concert, -5, 0
    Backstage passes to a TAFKAL80ETC concert, -10, 0
    Conjured Mana Cake, -12, 0
    
    -------- day 16 --------
    name, sellIn, quality
    +5 Dexterity Vest, -6, 0
    Aged Brie, -14, 30
    Elixir of the Mongoose, -11, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -1, 0
    Backstage passes to a TAFKAL80ETC concert, -6, 0
    Backstage passes to a TAFKAL80ETC concert, -11, 0
    Conjured Mana Cake, -13, 0
    
    -------- day 17 --------
    name, sellIn, quality
    +5 Dexterity Vest, -7, 0
    Aged Brie, -15, 32
    Elixir of the Mongoose, -12, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -2, 0
    Backstage passes to a TAFKAL80ETC concert, -7, 0
    Backstage passes to a TAFKAL80ETC concert, -12, 0
    Conjured Mana Cake, -14, 0
    
    -------- day 18 --------
    name, sellIn, quality
    +5 Dexterity Vest, -8, 0
    Aged Brie, -16, 34
    Elixir of the Mongoose, -13, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -3, 0
    Backstage passes to a TAFKAL80ETC concert, -8, 0
    Backstage passes to a TAFKAL80ETC concert, -13, 0
    Conjured Mana Cake, -15, 0
    
    -------- day 19 --------
    name, sellIn, quality
    +5 Dexterity Vest, -9, 0
    Aged Brie, -17, 36
    Elixir of the Mongoose, -14, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -4, 0
    Backstage passes to a TAFKAL80ETC concert, -9, 0
    Backstage passes to a TAFKAL80ETC concert, -14, 0
    Conjured Mana Cake, -16, 0
    
    -------- day 20 --------
    name, sellIn, quality
    +5 Dexterity Vest, -10, 0
    Aged Brie, -18, 38
    Elixir of the Mongoose, -15, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -5, 0
    Backstage passes to a TAFKAL80ETC concert, -10, 0
    Backstage passes to a TAFKAL80ETC concert, -15, 0
    Conjured Mana Cake, -17, 0
    
    -------- day 21 --------
    name, sellIn, quality
    +5 Dexterity Vest, -11, 0
    Aged Brie, -19, 40
    Elixir of the Mongoose, -16, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -6, 0
    Backstage passes to a TAFKAL80ETC concert, -11, 0
    Backstage passes to a TAFKAL80ETC concert, -16, 0
    Conjured Mana Cake, -18, 0
    
    -------- day 22 --------
    name, sellIn, quality
    +5 Dexterity Vest, -12, 0
    Aged Brie, -20, 42
    Elixir of the Mongoose, -17, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -7, 0
    Backstage passes to a TAFKAL80ETC concert, -12, 0
    Backstage passes to a TAFKAL80ETC concert, -17, 0
    Conjured Mana Cake, -19, 0
    
    -------- day 23 --------
    name, sellIn, quality
    +5 Dexterity Vest, -13, 0
    Aged Brie, -21, 44
    Elixir of the Mongoose, -18, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -8, 0
    Backstage passes to a TAFKAL80ETC concert, -13, 0
    Backstage passes to a TAFKAL80ETC concert, -18, 0
    Conjured Mana Cake, -20, 0
    
    -------- day 24 --------
    name, sellIn, quality
    +5 Dexterity Vest, -14, 0
    Aged Brie, -22, 46
    Elixir of the Mongoose, -19, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -9, 0
    Backstage passes to a TAFKAL80ETC concert, -14, 0
    Backstage passes to a TAFKAL80ETC concert, -19, 0
    Conjured Mana Cake, -21, 0
    
    -------- day 25 --------
    name, sellIn, quality
    +5 Dexterity Vest, -15, 0
    Aged Brie, -23, 48
    Elixir of the Mongoose, -20, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -10, 0
    Backstage passes to a TAFKAL80ETC concert, -15, 0
    Backstage passes to a TAFKAL80ETC concert, -20, 0
    Conjured Mana Cake, -22, 0
    
    -------- day 26 --------
    name, sellIn, quality
    +5 Dexterity Vest, -16, 0
    Aged Brie, -24, 50
    Elixir of the Mongoose, -21, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -11, 0
    Backstage passes to a TAFKAL80ETC concert, -16, 0
    Backstage passes to a TAFKAL80ETC concert, -21, 0
    Conjured Mana Cake, -23, 0
    
    -------- day 27 --------
    name, sellIn, quality
    +5 Dexterity Vest, -17, 0
    Aged Brie, -25, 50
    Elixir of the Mongoose, -22, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -12, 0
    Backstage passes to a TAFKAL80ETC concert, -17, 0
    Backstage passes to a TAFKAL80ETC concert, -22, 0
    Conjured Mana Cake, -24, 0
    
    -------- day 28 --------
    name, sellIn, quality
    +5 Dexterity Vest, -18, 0
    Aged Brie, -26, 50
    Elixir of the Mongoose, -23, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -13, 0
    Backstage passes to a TAFKAL80ETC concert, -18, 0
    Backstage passes to a TAFKAL80ETC concert, -23, 0
    Conjured Mana Cake, -25, 0
    
    -------- day 29 --------
    name, sellIn, quality
    +5 Dexterity Vest, -19, 0
    Aged Brie, -27, 50
    Elixir of the Mongoose, -24, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -14, 0
    Backstage passes to a TAFKAL80ETC concert, -19, 0
    Backstage passes to a TAFKAL80ETC concert, -24, 0
    Conjured Mana Cake, -26, 0
    
    -------- day 30 --------
    name, sellIn, quality
    +5 Dexterity Vest, -20, 0
    Aged Brie, -28, 50
    Elixir of the Mongoose, -25, 0
    Sulfuras, Hand of Ragnaros, 0, 80
    Sulfuras, Hand of Ragnaros, -1, 80
    Backstage passes to a TAFKAL80ETC concert, -15, 0
    Backstage passes to a TAFKAL80ETC concert, -20, 0
    Backstage passes to a TAFKAL80ETC concert, -25, 0
    Conjured Mana Cake, -27, 0
```