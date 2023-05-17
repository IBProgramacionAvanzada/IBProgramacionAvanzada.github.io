---
layout: post
title: Colecciones 
tagline: Juntando datos
categories: 
- F# as your first functional programming language
tags:
- fsharp
---
La vida del programador sería muy aburrida si sólo se pudiera trabajar con entidades únicas. En la enorme mayoría de los casos, uno tiene colecciones de datos con los que tiene que desarrollar código. Si bien es posible construir agregados de datos usando los tipos algebraicos que posee F#, el lenguaje provee de distintas opciones para manejar colecciones de datos. F# no cuenta con una, sino con varios tipos de datos para manejar colecciones, que iremos viendo en las próximas clases. 

En F# las colecciones siempre son homogéneas, esto es, contienen el mismo tipo de dato. Por otra parte, el lenguaje provee de tipos de colecciones _inmutables_ (`list`, `seq`, `map`, `set`), excepto `Array` que puede ser mutable. 

El lenguaje provee de una variedad de métodos para trabajar con las colecciones como un todo, sin necesidad de tener que iterar para sacar provecho de las mismas.

### Tipos y recursión

Hemos visto cómo la recursión permite reemplazar el tradicional loop `for`. También es posible definir tipos recursivos. 

Por ejemplo, uno puede modelar los números naturales al [estilo de Peano](https://en.wikipedia.org/wiki/Peano_axioms):

```fsharp
type Peano = 
    | Zero 
    | Succ of Peano
```

```fsharp
let one = Succ Zero 
let two = Succ(Succ(Zero))

printfn "%A" one
printfn "%A" two

```

    Succ Zero
    Succ (Succ Zero)


Al igual que la recursión en algoritmos, los tipos recursivos siempre tienen uno o más casos _base_, y luego uno o más casos recursivos. 

Con esta posibilidad, podemos construir un tipo de dato recursivo que corresponda a una lista enlazada (_linked list_), donde cada nodo de la lista contiene un elemento y el resto de la lista:

```fsharp
type MyList =
    | Empty
    | Cons of int * MyList 
```

```fsharp
let emptyList  = Empty

let singletonList = Cons (3, Empty)

let exampleList = Cons (1, Cons (2, Cons (3, Empty)))

printfn "%A" exampleList
```

    Cons (1, Cons (2, Cons (3, Empty)))


Por supuesto que uno necesitaría de algunas funciones como para poblar esta lista. Por ejemplo, `replicate x n` crea una lista de longitud `n` con el mismo elemento `x`: 

```fsharp
let rec replicate x n =
    if n = 0 then
        Empty 
    else
        Cons(x, replicate x (n - 1))
```

```fsharp
replicate 1 4
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Cons (1, Cons (1, Cons (1, Cons (1, Empty))))</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Cons (1, Cons (1, Cons (1, Empty)))</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Cons (1, Cons (1, Empty))</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Cons (1, Empty)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Empty</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>


O también se podría crear una lista de n elementos desde el 0 hasta el n-1:

```fsharp
let initList n =

    let rec loop i n = 
        if n = 0 then
            Empty
        else
            if (i<n) then 
                Cons(i, loop (i+1) n )
            else 
                Cons(n,Empty)


    loop 0 (n-1) 
```

```fsharp
printfn "%A" (initList 2)
printfn "%A" (initList 3)
printfn "%A" (initList 4)
```

    Cons (0, Cons (1, Empty))
    Cons (0, Cons (1, Cons (2, Empty)))
    Cons (0, Cons (1, Cons (2, Cons (3, Empty))))


Así es posible entonces crear colecciones de elementos del mismo tipo en forma recursiva. 

## Operaciones sobre listas

En esencia, hay unas pocas operaciones que se pueden realizar sobre colecciones de entidades de un mismo tipo. Por ejemplo, uno podría aplicar una función a cada elemento de la lista, para convertirlo en otro elemento. O se podría obtener un valor a partir de un cómputo sobre todos los elementos de una lista (calcular el máximo o el mínimo, obtener el 
promedio, etc). Al utilizar un estilo funcional, es importante enfocar la programación de
código que usa colecciones teniendo en cuenta estas operaciones. En el caso de F#, existe una gran variedad de métodos sobre los distintos tipos de colecciones que provee el lenguaje. En la mayoría de los casos es posible combinar estos métodos para obtener el resultado requerido, sin tener que utilizar recursión. 

Siguiendo con el ejemplo de `MyList`, vamos a definir dos tipos de operaciones sobre colecciones que son muy importantes.

El primer caso es el de `map`, que permite aplicar una función a cada elemento de la lista:

```fsharp
let rec map f list =
    match list with 
    | Empty -> Empty 
    | Cons (n, ls) -> 
        Cons(f n, map f ls)
```

```fsharp
let square i = 
    i*i 

let l = initList 4

let lsq = map square l 

printfn "%A" l
printfn "%A" lsq
```

    Cons (0, Cons (1, Cons (2, Cons (3, Empty))))
    Cons (0, Cons (1, Cons (4, Cons (9, Empty))))


El siguiente es un ejemplo de la operación reducción, que consiste en aplicar una función que combina dos elementos de la lista sucesivamente:

```fsharp
let fold f list =

    let rec loop acc f list = 
        match list with 
        | Empty -> acc 
        | Cons (n, ls) -> 
            let a = f acc n 
            loop a f ls 

    loop 0 f list             
```

```fsharp
let add i j : int = 
    i + j 

let l2 = initList 5
let sum  = fold add l2 

printfn "%A" sum 
```

    10


Estos métodos pueden componerse adecuadamente ya sea a través del _piping_:

```fsharp
l2
|> map square
|> fold add
```


<div class="dni-plaintext"><pre>30</pre></div><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>


O definiendo una composición de funciones:

```fsharp
let squareAndAdd = (map square) >> (fold add) 

printfn "%A" (squareAndAdd l2)
```

    30


### Listas

F# provee del tipo `list` para manejar listas tal como vimos en el ejemplo anterior, con una sintaxis clara: se utilizan corchetes para definir el comienzo y final de la lista, que contiene elementos separados por `;`.

```fsharp
let l3 = [0; 2; 3]

let singleton = ["Hola"]

let empty = List.empty 

printfn "%A" l3

```

    [0; 2; 3]


Se pueden inicializar las listas:

```fsharp
// A range list
let range = [0..4]

// A range list with steps 
let rangeby3 = [0..3..17]

// All the same
let constants = List.replicate 4 1

// A range 
let rangeByInit = List.init 4 id 

printfn "%A" range
printfn "%A" rangeby3
printfn "%A" constants
printfn "%A" rangeByInit

```

    [0; 1; 2; 3; 4]
    [0; 3; 6; 9; 12; 15]
    [1; 1; 1; 1]
    [0; 1; 2; 3]


Y provee de las funciones `map` y `fold` correspondientes.

```fsharp
let l4 = List.init 5 id 

let l5 = List.map square l4 

let sum  = List.fold (fun a elem -> a + elem ) 0 l4  

printfn "%A" l5 
printfn "%A" sum 
```

    [0; 1; 4; 9; 16]
    10


Pero mucho más, porque la función `sum` hace lo que uno supone:

```fsharp
l4
|> List.map square
|> List.sum 
```


<div class="dni-plaintext"><pre>30</pre></div><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>

Recomiendo la página [Choosing between collection functions](https://fsharpforfunandprofit.com/posts/list-module-functions/) para consultar ejemplos y uso de métodos de colecciones.