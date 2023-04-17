Manejando por las ramas con `if`, _pattern matching_ y recursión.

Uno de los conceptos más importantes en programación es el control de flujo. Éste se refiere a la posibilidad del código de manejar diferentes caminos de ejecución de acuerdo a condiciones que aparecen al procesar datos. Por ejemplo, el usuario de la máquina expendedora puede pagar con billetes, monedas o tarjeta de crédito, y el sistema de software debe poder acomodar todos esos casos.

En la mayoría de los lenguajes existe la sentencia `if condición then ... else`, que permite administrar el código de acuerdo a si la condición es verdadera o falsa. 

Sin embargo, en F#, no tenemos sentencias sino expresiones, con lo cual el `if` también es evaluado como una expresión y debe devolver un valor. Por ejemplo, podemos evaluar la función $|x|$:

```fsharp
let absoluteValue x =
    let v = 
        if x < 0 then
            -x 
        else 
            x
    v

printfn "%A" (absoluteValue 4)
printfn "%A" (absoluteValue -5)
```

    4
    5


En este ejemplo, el resultado de la evaluación de la expresión `if` se guarda en el identificador `v` que es el valor de retorno de la función. Se puede escribir el `if` en una sola línea:

```fsharp
let absoluteValue2 x =
    let v = if x < 0 then -x else x 
    v
            
printfn "%A" (absoluteValue2 2)
printfn "%A" (absoluteValue2 -8)
```

    2
    8


E incluso no es necesario usar `v`, ya que el condicional se evalúa y su resultado es el que devuelve la función:

```fsharp
let absoluteValueOneliner x =
    if x < 0 then -x else x 
    
            
printfn "%A" (absoluteValueOneliner 2)
printfn "%A" (absoluteValueOneliner -8)
```

    2
    8


La expresión `if` no es más que un _edulcorante sintáctico_ para una expresión de pattern matching:

```fsharp
let absoluteValuePM x =
    match x < 0 with 
    | true -> -x 
    | false -> x 
    
            
printfn "%A" (absoluteValuePM 2)
printfn "%A" (absoluteValuePM -8)
```

    2
    8


En efecto, la expresión `x < 0` es de tipo `bool`, que tiene dos casos posibles, `true` y `false`.  

Obsérvese también que ambas ramas del condicional deben evaluarse al mismo tipo de dato, en el ejemplo anterior, `int`. 

El lenguaje no provee otra variante del condicional `if`, porque para eso está...


### Pattern Matching

Una de las fortalezas del lenguaje radica en la construcción de _pattern matching_. Hasta ahora la hemos usado para seleccionar los casos posibles en una unión discriminada. Sin embargo, el _pattern matching_ es más general y, a diferencia del `if`, permite crear múltiples ramas. Para ello se usa la palabra reservada `when`, de la siguiente manera:

```fsharp
let votingAge = 16
let buyingOHAge = 18 

let checkAge (age: int) =
    match age with 
    | a when a < votingAge -> printfn "No vota ni puede comprar vino"
    | a when a >= votingAge && a < buyingOHAge -> printfn "Puede votar, pero no comprar vino"
    | a when a >= buyingOHAge -> printfn "Puede votar y comprar vino" 


checkAge 15
checkAge 17
checkAge 50
```

    No vota ni puede comprar vino
    Puede votar, pero no comprar vino
    Puede votar y comprar vino


En este caso el _pattern matching_ nos permite decidir entre tres opciones de la edad. Notar que la función `checkAge` retorna el tipo `unit`. Otro ejemplo sería:

```fsharp
type WeatherWarning = 
    | Freezing 
    | YellowAlertHot
    | RedAlertHot 
    | NoAlarm 

let emitAlarm warning = 
    match warning with 
    | Freezing -> printfn "%A" "Alarm: Freezing"
    | YellowAlertHot-> printfn "%A" "Alarm: Yellow"
    | RedAlertHot -> printfn "%A" "Alarm: Red"
    | NoAlarm  -> printfn "%A" "It's fine "

let checkTempForWarning temp =
    match temp with 
    | t when t <= 0 -> Freezing 
    | t when t > 33 && t < 39 -> YellowAlertHot
    | t when t >= 39 -> RedAlertHot
    | _ -> NoAlarm     

emitAlarm (checkTempForWarning 43)

checkTempForWarning 37
|> emitAlarm 

-3 
|> checkTempForWarning 
|> emitAlarm 


```

    "Alarm: Red"
    "Alarm: Yellow"
    "Alarm: Freezing"


Un ejemplo más para ver cómo es la sintaxis del _pattern matching_ en la situación en que se quieren agrupar casos:

```fsharp
type Animals =
    | Cat
    | Dog 
    | Eagle  

let howManyLegs animal =
    match animal with
    | Animals.Cat | Animals.Dog -> 4  // grouping two cases
    | Animals.Eagle -> 2

printfn "%A" (howManyLegs Animals.Cat)
printfn "%A" (howManyLegs Animals.Dog)
printfn "%A" (howManyLegs Animals.Eagle)

```

    4
    4
    2


Esto es posible en el caso en que el _pattern matching_ sea contra expresiones que son o representan constantes. 

## Reemplazando el `for` por recursión

Dado que el loop `for` habitual requiere (ya sea explicita o implícitamente) algún tipo de contador que se va actualizando, esto es, es una _variable_, es necesario contar con una forma de poder hacer loops sin el `for`. Para ello se utiliza la recursión en todos los lenguajes funcionales. 

Por ejemplo, si uno tiene el loop

```c
    for(int i = 0; i < n; i++)
    {
        printf("%d\n",i);
    }
```

Se reemplazará por una función recursiva:

```fsharp
let printNumbers n = 

    let rec loop i n =
        if i < n then
            printfn "%d" i
            loop (i + 1) n
        else 
            printfn "done"

    loop 0 3

printNumbers 3    
```

    0
    1
    2
    done


El primer punto a notar es que, dado que en el lenguaje hay solo expresiones, los loops `for` van a aparecer representados por funciones, ya no es posible tener un loop como un conjunto de sentencias que se ejecutan. En general los loops como sentencias implican también una aplicación sobre variables de lo que sucede dentro del loop. 



Veamos el clásico ejemplo de la serie de Fibonacci:
$$
a_n = a_{n-1} + a_{n-2}
$$
con $a_0=0$ y $a_1=1$.

Este ejemplo, escrito en C sería:
```c
int fib(int n) {
    int prev1 = 0, prev2 = 1, curr = 0;
    if (n == 0)
        return prev1;
    else if (n == 1)
        return prev2;
    else {
        for (int i = 2; i <= n; i++) {
            curr = prev1 + prev2;
            prev1 = prev2;
            prev2 = curr;
        }
        return curr;
    }
}
```

La versión no recursiva implica guardar el estado de los dos valores previos de la serie dentro de la función, que se realiza al usar `prev1=0` y `prev2=1`. 

La versión recursiva en C sería 
```c
int fib(int n) {
    if (n < 2)
        return n;
    else
        return fib(n-1) + fib(n-2);
}
```

En F# la recursión funciona en forma similar, aunque se puede utilizar _pattern matching_:

```fsharp
let rec fib n  = 
    match n with
    | 0 | 1 -> n
    | n -> fib (n-1) + fib (n - 2)
    
fib 10 |> printfn "%i" 
```

    55


Uno podría estar interesado en sumar una serie de números, por ejemplo calcular la serie
$$$
1 + \frac{1}{2} + \frac{1}{3} + \cdots + \frac{1}{n}
$$$
hasta un cierto valor de $n$:

```fsharp
let rec sumSeries n =
    match n with
    | 0 -> 0.0
    | n -> 1.0/(float n) + sumSeries(n-1)

printfn "%A" (sumSeries 100)    
```

    5.187377518


En la práctica, la forma más pura de programar una recursión no es la más adecuada. Por ejemplo, en el caso de la sucesión de Fibonacci, hay llamados a la función recursiva que recalculan valores que efectivamente ya fueron calculados. Por eso se utiliza frecuentemente el método conocido como _tail recursion_. En este caso, se previene el llamado innecesario a la función recursiva introduciendo nuevos valores que van llevando la información del estado anterior de la recursión al cálculo presente:

```fsharp
let fib n =
    let rec loop acc1 acc2 n =
        match n with
        | 0 -> acc1
        | 1 -> acc2
        | _ ->
            loop acc2 (acc1 + acc2) (n - 1)
    loop 0 1 n
```

En este caso la función exterior `fib` no es recursiva en sí misma, pero utiliza una función recursiva en su interior, denominada `loop`. Los valores `acc1` y `acc2` son los encargados de llevar la información de los elementos previos de la serie necesarios para calcular el elemento presente.
