---
layout: post
title: Ejercicios
tagline: Leyendo datos, y volviendo a la máquina expendedora
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Horario de atención

Nos han encargado un programa sencillo para manejar el [horario de atención](https://github.com/christian-fei/opening-hours-kata) 
al público de un bar. Los requerimientos son los siguientes

- Los días de apertura y las horas de atención deben ser configurables. Por ejemplo, Lunes, Miércoles y Viernes de 9 a 18. 
  (El bar tiene horario corrido).
- Es necesario poder mostrar el próximo día que el bar está abierto en un cartel en la ventana del bar.
- Además es necesario proveer de una función que indique si en un determinado día y horario, el bar está abierto.

Los prototipos de las funciones son

```fsharp
isOpenOn: System.DateTime -> bool
nextOpeningDate: System.DateTime -> date 
```

> Las funciones usan la biblioteca [`System.DateTime`](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0#initialization-01) para representar los días y horarios. Por ejemplo:






### Derivada simbólica

El siguiente tipo de dato representa una variable matemática (`Expr.Var`), un número (`Expr.Num`) y dos operaciones, suma (`Expr.Sum`) y producto (`Expr.Prod`):

```fsharp
type Expr =
    | Num of int 
    | Var
    | Sum of Expr * Expr
    | Prod of Expr * Expr
```

Por ejemplo, la expresión $3 + 5 x$ se expresaría como

```fsharp
Sum (Num 3, Prod (Num 5, Var))
```


- Escriba una función `diff` que recibe una expresión `Expr` y calcula su derivada.

Considere la siguiente función

```fsharp
let rec show expr = 
    match expr with 
    | Var -> "x"
    | Num i -> i.ToString()
    | Sum (e1,e2) -> 
        let sum = show e1 + "+" + show e2 
        sum 
    | Prod (e1,e2) ->
        show e1 + "*" + show e2 
```

que dada una expresión, la imprime en una forma más legible. Por ejemplo, si tenemos

```fsharp
let q = Sum (Num 3, Prod (Num 5, Var))
```

la aplicación de `show` a `q` nos da

```fsharp
3+5*x
```

Sin embargo, esta función no es capaz de simplificar las expresiones. Por ejemplo:

```fsharp
let t = Sum (Num 3, Num 1)
show t
```

da como resultado `3+1`, en lugar de `4` como quisiéramos. 

- Escriba funciones para simplificar los casos más habituales (suma con 0, multiplicación con 1, etc.)
- Escriba una nueva función `simplifyDiff` que dada una expresión `Expr`, de como resultado `string` que represente la derivada.
- La función `show` tiene limitaciones, cuáles son? Explore las formas en que se podrían superar esos problemas.
  
