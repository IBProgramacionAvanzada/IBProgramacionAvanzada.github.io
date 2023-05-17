---
layout: post
title: Más sobre funciones 
tagline: Repasando algunas cuestiones sobre funciones
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Hemos presentado las bases sobre las cuales se asienta el lenguaje en términos de funciones: 

- Funciones como _ciudadanos de primera clase_ en el lenguaje
- Composición de funciones
- Aplicación parcial

Veamos algunos ejemplos más como para ir progresando

```fsharp
let fullName firstName lastName =
    let s = firstName + lastName 
    s 

let john = fullName "John" "Lennon"     

printfn "%A" john    

```

    "JohnLennon"


Vemos que se pueden definir otros identificadores dentro de las funciones, tal como esperábamos. En este caso, aparentemente, la función concatenaría los strings `firstName` y `lastName` en un solo string, que devuelve como resultado a través de `s`. 

Sin embargo, un punto interesante a notar con la función `fullName` es que hasta tanto la función no se aplica sobre dos strings, el compilador no tiene manera de saber qué tipo de datos quiero usar. Esto se debe a que el operador `+` en F# se usa tanto para sumar números como para concatenar _strings_. Si bien el nombre de la función induce a pensar que se van a usar _strings_, el compilador no hace un análisis semántico... 


```fsharp
let fullNameS firstName lastName =
    let s = firstName + " " +  lastName 
    s 
```

En el caso de `fullNameS` el compilador infiere correctamente los tipos como _strings_, debido a que claramente el operador `+` se está usando para concatenar, al utilizarlo con ` + "  " +`. 

> Acá hay que aclarar una particularidad de los _Notebooks_: si defino la función en una celda, y la utilizo en otra, la función va a quedar asociada a inputs (y outputs) de tipo entero. Para que el compilador infiera correctamente los tipos de datos, debo utilizar la función en la misma celda. A partir de allí, quedarán definidos los tipos de datos para todo el _Notebook_.

Por supuesto se pueden llamar funciones dentro de otras funciones:

```fsharp
let prettyPrint s =
    printfn "%A" s 

let hola name =
    prettyPrint ("Hola " + name + " !")
```

```fsharp
let paul = fullNameS "Paul" "McCartney"

prettyPrint paul
```

    "Paul McCartney"


```fsharp
hola paul
```

    "Hola Paul McCartney !"


Usamos los paréntesis para separar los llamados a funciones que llaman a funciones:

```fsharp
hola (fullNameS "George" "Harrison")
```

    "Hola George Harrison !"


Podemos también usar el operador _pipe_:

```fsharp
paul 
|> hola 
```

    "Hola Paul McCartney !"


Pero atención cuando uno usa varios inputs, porque el _piping_ sólo acepta el último argumento cuando hay más de uno:

```fsharp
"George" "Harrison"
|> fullNameS 
|> hola 
```


    input.fsx (1,1)-(1,9) typecheck error This value is not a function and cannot be applied.


    input.fsx (3,4)-(3,8) typecheck error Type mismatch. Expecting a


        '(string -> string) -> obj'    


    but given a


        'string -> unit'    


    The type 'string -> string' does not match the type 'string'


```fsharp
"Harrison"
|> fullNameS "George" 
|> hola 
```

    "Hola George Harrison !"


Veamos un ejemplo un poco más claro. Volviendo a nuestra máquina expendedora, supongamos que se conoce el costo de un paquete de galletitas marca ACME, que es el precio que paga el vendedor. A ese costo le aplica un porcentaje de ganancia, y además otro porcentaje de impuestos que traslada al consumidor final. Veamos cómo calcular el precio del paquete:

```fsharp
let priceAfterTax tax value = 
    let price = value * (1.0 + tax/100.0)
    price 

let priceAfterEarning earning value = 
    let price = value * (1.0 + earning/100.0)
    price 

let price tax earning value = 
    let p = priceAfterTax tax value 
    let q = priceAfterEarning earning p 
    q  


```

```fsharp
let cost = 45.0 

let tax = 21 
let earning = 10 

let finalPrice = price tax earning cost 

prettyPrint finalPrice
```

    59.895


O uno podría usar pipe:

```fsharp
cost
|> priceAfterEarning earning 
|> priceAfterTax tax 

```


<div class="dni-plaintext"><pre>59.89500000000001</pre></div><style>
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


Otra posibilidad es generalizar y crear una sola función para calcular el porcentaje

```fsharp
let applyPercentage per value = 
    value * (1.0 + per/100.0)

let priceAfterEarning2 = applyPercentage earning
let priceAfterTax2 = applyPercentage tax     
```

```fsharp
cost
|> priceAfterEarning2
|> priceAfterTax2 
```


<div class="dni-plaintext"><pre>59.89500000000001</pre></div><style>
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


Que es equivalente a componer las funciones:

```fsharp
let finalPrice = priceAfterEarning2 >> priceAfterTax2 
```

```fsharp
printfn "%A" (finalPrice cost)
```

    59.895

