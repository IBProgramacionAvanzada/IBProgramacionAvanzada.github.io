---
layout: post
title: Ejercicios
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Derivada simbólica

El [siguiente tipo de dato](https://link.springer.com/book/10.1007/978-1-4842-0740-6) representa una variable matemática (`Expr.Var`), un número (`Expr.Num`) y dos operaciones, suma (`Expr.Sum`) y producto (`Expr.Prod`):

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
- Modifique la función `diff` de modo tal que dada una expresión `Expr`, de como resultado un `string` que represente la derivada simplificada utilizando
  las funciones del item anterior.
- Explore las limitaciones de la función `show`, y analice cuáles serían las maneras de resolverlas.


### Horario de atención

Nos han encargado un programa sencillo para manejar el [horario de atención](https://github.com/christian-fei/opening-hours-kata) 
al público de un bar. Los requerimientos son los siguientes

- Los días de apertura y las horas de atención deben ser configurables. Por ejemplo, Lunes, Miércoles y Viernes de 9 a 18. 
  (El bar tiene horario corrido).
- Es necesario poder mostrar el próximo día que el bar está abierto en un cartel en la ventana del bar.
- Además es necesario proveer de una función que indique si en un determinado día y horario, el bar está abierto.

Las funciones necesarias son

- `isOpenOn`: dada una fecha, determina si el bar está abierto o no
- `nextOpeningDate`: dada una fecha, calcula el siguiente día que está abierto el bar

Hint: Para manejar fechas, se puede usar la biblioteca [`System.DateTime`], por ejemplo:
 
```fsharp
// crea la fecha con año, mes, día, hora, minutos y segundos
let date1 = DateTime(2022, 5, 11, 14, 30, 52)

// Ahora, ahora en UTC y el día de hoy
let date1 = DateTime.Now
let date2 = DateTime.UtcNow
let date3 = DateTime.Today

// Se pueden sumar o restar duraciones:
let today = DateTime.Now
let duration = TimeSpan(1, 0, 0, 0) // duración de un día, 0 horas, 0 minutos y 0 segundos
let tomorrow = today.Add duration
printfn $"{today:ddd}" // Thu
printfn $"{tomorrow:ddd}" // Fri
```

La documentación completa de la biblioteca se encuentra [acá](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0#initialization-01), con muchos ejemplos.



### The Beatles

Usando el _type provider_  de CSV que vimos en la clase, y [los datos de las canciones de
los Beatles](https://github.com/fcolavecchia/fp-course-public/blob/main/data/The%20Beatles%20songs%20dataset.csv), haga una lista de los tres años más productivos de su carrera.

