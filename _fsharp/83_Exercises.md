---
layout: post
title: Ejercicios
tagline: Leyendo datos, y volviendo a la máquina expendedora
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

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
  
