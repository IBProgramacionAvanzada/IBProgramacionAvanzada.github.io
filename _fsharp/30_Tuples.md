---
layout: post
title: Tuplas 
tagline: Un tipo conveniente sin mucho adorno
categories: 
- F# as your first functional programming language
tags:
- fsharp
---




Las _tuplas_ son agregados ordenados de (unos muy pocos) datos, posiblemente de distinto tipo, que en algunos casos son más convenientes que definir _records_. 

```fsharp
let messi = ("Messi",10)
let paredes = ("Paredes",5)
```

En este caso definimos una tupla donde la primer componente es de tipo `string`, mientras que la segunda componente es de tipo `int`. 

Se pueden extraer la primer o segunda componente para el caso de tuplas de dos elementos
a través de las funciones `fst` y `snd`.

```fsharp
let nombreMessi = fst messi 
let numeroMessi = snd messi

printfn "%s juega con la casaca %A" nombreMessi numeroMessi
```

    Messi juega con la casaca 10


Para tuplas de más elementos, cada uno de ellos puede extraerse con una variante de _pattern matching_:

```fsharp
let cookies = ("Opera", 200, 4.15)
let choco = ("Wonka", 35, 12.40) 

let operaName, operaWeight, operaPrice = cookies 

printfn "El producto %s pesa %d y cuesta %f" operaName operaWeight operaPrice
```

    El producto Opera pesa 200 y cuesta 4.150000


Si uno está interesado en sólo uno de los componentes, se puede usar `_` como comodín:

```fsharp
let _,_,chocoPrice = choco

printfn "El chocolate Wonka cuesta %f $" chocoPrice
```

    El chocolate Wonka cuesta 12.400000 $


En los dos ejemplos anteriores el uso de los paréntesis `(` y `)` es opcional.


Así como es posible hacer _pattern matching_ para controlar el flujo del código con distintas condiciones, también es posible utilizar tuplas. 

```fsharp
let isExpensive product = 
    match product with 
    | (brand,_,price) when price > 10.0 -> 
        printfn "%A is Expensive!" brand 
    | _ -> 
        let (brand,_,_) = product
        printfn "%A is cheap" brand 

cookies |> isExpensive 
choco |> isExpensive 
```

    "Opera" is cheap
    "Wonka" is Expensive!


Hay que recordar que las tuplas es otro tipo de dato, así que se puede mezclar con records, uniones, etc. Se pueden definir tipos de datos a partir de tuplas:

```fsharp
type Point = 
    | Point of float * float

let xOf p = 
    let (Point (x,_)) = p
    x
let yOf p = 
    let (Point (_,y)) = p
    y
```

```fsharp
let p = Point (2,3)

printfn "la coordenada x de p es %A" (xOf p)
printfn "la coordenada y de p es %A" (yOf p)

```

    la coordenada x de p es 2.0
    la coordenada y de p es 3.0


Y las tuplas podrían contener otros tipos de datos, como tuplas de tuplas:

```fsharp
type Segment =  Point * Point

type Triangle = Point * Point * Point 

```

```fsharp
let lengthOf segment =
    let  (p1,p2) = segment 
    let sx = xOf p1 - xOf p2 
    let sy = yOf p1 - yOf p2 
    sqrt (sx*sx + sy*sy)


```

```fsharp
let s =  (Point (1,4), Point (2,5))
printfn "%A" (lengthOf s)
```

    1.414213562

