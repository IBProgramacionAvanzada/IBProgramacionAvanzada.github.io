---
layout: post
title: Más sobre Colecciones 
tagline: Juntando datos
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Hasta ahora hemos visto el tipo de colección `list`, que es esencialmente una lista simplemente enlazada. La definición de `list` es similar a la que propusimos con `MyList`, pero 
- es general para cualquier tipo de dato
- utiliza símbolos adecuados en lugar de etiquetas

```fsharp
type List<'T> = 
       | ([])  
       | ( :: )  of Head: 'T * Tail: 'T list
```

Como una lista es una unión discriminada, puede usarse tal cual cualquiera de ellas. Por ejemplo, se puede escribir [una función para imprimir los elementos de una lista](https://fsharpforfunandprofit.com/posts/match-expression/):

```fsharp
// loop through a list and print the values
let rec loopAndPrint aList =
    match aList with
    // empty list means we're done.
    | [] ->
        printfn "empty"

    // binding to head::tail.
    | x::xs ->
        printfn "element=%A," x
        // do all over again with the
        // rest of the list
        loopAndPrint xs

loopAndPrint []        
```

    empty


```fsharp
let l = [1;2;3]

l |> loopAndPrint

```

    element=1,
    element=2,
    element=3,
    empty


Se puede acceder a distintos elementos de la lista a través de índices, aunque su uso no es generalizado. Si se utilizan las funciones `Head` y `Tail` para encontrar el primer elemento y el resto de la lista:

```fsharp
printfn "%A" l.Head
printfn "%A" (l |> List.head)
printfn "%A" l.Tail 
printfn "%A" (l |> List.tail) 
```

    1
    1
    [2; 3]
    [2; 3]


Acá podemos ver dos variantes de estas funciones:
- Aquellas del tipo `List.` que provienen del módulo de listas de F#,
- _Métodos_  que un tipo de dato `list`. 

En el caso de los métodos, vemos cómo F# provee también características asociadas a la programación orientada a objeto. 

Hay que prestar atención a que una lista pueda ser vacía, porque en ese caso estos métodos devuelven una excepción:

```fsharp
printfn "%A" [].Head 
```


    System.InvalidOperationException: The input list was empty.


       at Microsoft.FSharp.Collections.FSharpList`1.get_Head() in D:\a\_work\1\s\src\FSharp.Core\prim-types.fs:line 4072


       at <StartupCode$FSI_0010>.$FSI_0010.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


```fsharp
printfn "%A" [].Tail
```


    System.InvalidOperationException: The input list was empty.


       at Microsoft.FSharp.Collections.FSharpList`1.get_Tail() in D:\a\_work\1\s\src\FSharp.Core\prim-types.fs:line 4077


       at <StartupCode$FSI_0011>.$FSI_0011.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


Usando la notación de listas, se pueden crear así:

```fsharp
let l = 3 :: [6;7]
printfn "%A" l
```

    [3; 6; 7]


o concatenando dos listas:

```fsharp
let l2 = l @ [8;9]
printfn "%A" l2
```

    [3; 6; 7; 8; 9]


### Procesando listas

Como venimos insistiendo, la idea de las colecciones es poder procesarlas a través de un función, o una serie concatenada de funciones (usando _piping_) de forma tal de poder obtener el resultado adecuado. 

Vimos que las funciones sobre colecciones se pueden clasificar de acuerdo al tipo de inputs y outputs de cada una de ellas. Detallando esta idea, en la siguiente imagen se ven algunos ejemplos de ellas, y qué tipos de inputs y outputs poseen (del libro [Stylish F#, de Kit Easton](https://link.springer.com/book/10.1007/978-1-4842-7205-3)):

<img src="img/KEaston-Table4-1.png" alt="" width="400"/>


La mayoría de estas funciones reciben como argumento una función y una lista, y devuelven una lista nueva con la transformación correspondiente. La función en general suele escribirse como una función anónima:

```fsharp
fun arguments -> expression
```

también conocidas como funciones _lambda_. 

Trabajemos con un ejemplo para ir incorporando este concepto. Para ello, usaremos un par de tipos de datos `Position` y `Player` que describen algunas características de un jugador de fútbol: 

```fsharp
type Position = GoalKeeper | Defender | Midfielder | Forward 

type Player =
    {
        Number: int 
        Name : string
        Team: string 
        Position: Position 
        Age: uint8 
    }
```

Obviamente, el ejemplo seguirá usando los siguientes ⭐️⭐️⭐️ : 

```fsharp
let champions2022 = [
    // Los 3 arqueros de Argentina en Qatar 2022
    {Number = 23; Name = "Emiliano Martínez" ;  Team = "Aston Villa"; Position = GoalKeeper; Age = 30uy};
    {Number = 12; Name = "Gerónimo Rulli" ;  Team = "Villarreal"; Position = GoalKeeper; Age = 30uy};
    {Number = 1; Name = "Franco Armani" ;  Team = "River"; Position = GoalKeeper; Age = 36uy};
    // Los 9 defensores de Argentina en Qatar 2022
    {Number = 26; Name = "Nahuel Molina" ;  Team = "Atlético de Madrid"; Position = Defender; Age = 24uy};
    {Number = 4; Name = "Gonzalo Montiel" ;  Team = "Sevilla"; Position = Defender; Age = 25uy};
    {Number = 13; Name = "Cristian Romero" ;  Team = "Tottenham"; Position = Defender; Age = 24uy};
    {Number = 6; Name = "Germán Pezzella" ;  Team = "Betis"; Position = Defender; Age = 31uy};
    {Number = 19; Name = "Nicolás Otamendi" ;  Team = "Benfica"; Position = Defender; Age = 34uy};
    {Number = 25; Name = "Lisandro Martínez" ;  Team = "Manchester United"; Position = Defender; Age = 24uy};
    {Number = 8; Name = "Marcos Acuña" ;  Team = "Sevilla"; Position = Defender; Age = 31uy};
    {Number = 3; Name = "Nicolás Tagliafico" ;  Team = "Olympique de Lyon"; Position = Defender; Age = 30uy};
    {Number = 2; Name = "Juan Foyth" ;  Team = "Villarreal"; Position = Defender; Age = 24uy};
    // Los 8 mediocampistas de Argentina en Qatar 2022
    {Number = 7; Name = "Rodrigo De Paul" ;  Team = "Atlético de Madrid"; Position = Midfielder; Age = 28uy};
    {Number = 5; Name = "Leandro Paredes" ;  Team = "Juventus"; Position = Midfielder; Age = 28uy};
    {Number = 20; Name = "Alexis Mac Allister" ;  Team = "Brighton"; Position = Midfielder; Age = 23uy};
    {Number = 18; Name = "Guido Rodríguez" ;  Team = "Betis"; Position = Midfielder; Age = 28uy};
    {Number = 17; Name = "Alejandro Gómez" ;  Team = "Sevilla"; Position = Midfielder; Age = 34uy};
    {Number = 24; Name = "Enzo Fernández" ;  Team = "Benfica"; Position = Midfielder; Age = 21uy};
    {Number = 14; Name = "Exequiel Palacios" ;  Team = "Bayer Leverkusen"; Position = Midfielder; Age = 24uy};
    {Number = 16; Name = "Thiago Almada" ;  Team = "Atlanta United"; Position = Midfielder; Age = 21uy};
    // Los 6 delanteros de Argentina en Qatar 2022
    {Number = 11; Name = "Ángel Di María" ;  Team = "Juventus"; Position = Forward; Age = 34uy};
    {Number = 22; Name = "Lautaro Martínez" ;  Team = "Inter"; Position = Forward; Age = 25uy};
    {Number = 9; Name = "Julián Álvarez" ;  Team = "Manchester City"; Position = Forward; Age = 22uy};
    {Number = 21; Name = "Paulo Dybala" ;  Team = "Roma"; Position = Forward; Age = 29uy};
    {Number = 15; Name = "Ángel Correa" ;  Team = "Atlético Madrid"; Position = Forward; Age = 27uy};
    {Number = 10; Name = "Lionel Messi" ;  Team = "París Saint-Germain"; Position = Forward; Age = 35uy};
]
```

Definamos una función que nos permite recorrer la lista e imprimirla. Para eso usamos `List.iter` que, justamente, itera la lista y devuelve `unit`: 

```fsharp
let almostPrettyPrintList l = 
    l 
    |> List.iter (fun elem -> printfn "%A" elem)
```

```fsharp
champions2022 
|> almostPrettyPrintList
```

    { Number = 23
      Name = "Emiliano Martínez"
      Team = "Aston Villa"
      Position = GoalKeeper
      Age = 30uy }
    { Number = 12
      Name = "Gerónimo Rulli"
      Team = "Villarreal"
      Position = GoalKeeper
      Age = 30uy }
    { Number = 1
      Name = "Franco Armani"
      Team = "River"
      Position = GoalKeeper
      Age = 36uy }
    { Number = 26
      Name = "Nahuel Molina"
      Team = "Atlético de Madrid"
      Position = Defender
      Age = 24uy }
    { Number = 4
      Name = "Gonzalo Montiel"
      Team = "Sevilla"
      Position = Defender
      Age = 25uy }
    { Number = 13
      Name = "Cristian Romero"
      Team = "Tottenham"
      Position = Defender
      Age = 24uy }
    { Number = 6
      Name = "Germán Pezzella"
      Team = "Betis"
      Position = Defender
      Age = 31uy }
    { Number = 19
      Name = "Nicolás Otamendi"
      Team = "Benfica"
      Position = Defender
      Age = 34uy }
    { Number = 25
      Name = "Lisandro Martínez"
      Team = "Manchester United"
      Position = Defender
      Age = 24uy }
    { Number = 8
      Name = "Marcos Acuña"
      Team = "Sevilla"
      Position = Defender
      Age = 31uy }
    { Number = 3
      Name = "Nicolás Tagliafico"
      Team = "Olympique de Lyon"
      Position = Defender
      Age = 30uy }
    { Number = 2
      Name = "Juan Foyth"
      Team = "Villarreal"
      Position = Defender
      Age = 24uy }
    { Number = 7
      Name = "Rodrigo De Paul"
      Team = "Atlético de Madrid"
      Position = Midfielder
      Age = 28uy }
    { Number = 5
      Name = "Leandro Paredes"
      Team = "Juventus"
      Position = Midfielder
      Age = 28uy }
    { Number = 20
      Name = "Alexis Mac Allister"
      Team = "Brighton"
      Position = Midfielder
      Age = 23uy }
    { Number = 18
      Name = "Guido Rodríguez"
      Team = "Betis"
      Position = Midfielder
      Age = 28uy }
    { Number = 17
      Name = "Alejandro Gómez"
      Team = "Sevilla"
      Position = Midfielder
      Age = 34uy }
    { Number = 24
      Name = "Enzo Fernández"
      Team = "Benfica"
      Position = Midfielder
      Age = 21uy }
    { Number = 14
      Name = "Exequiel Palacios"
      Team = "Bayer Leverkusen"
      Position = Midfielder
      Age = 24uy }
    { Number = 16
      Name = "Thiago Almada"
      Team = "Atlanta United"
      Position = Midfielder
      Age = 21uy }
    { Number = 11
      Name = "Ángel Di María"
      Team = "Juventus"
      Position = Forward
      Age = 34uy }
    { Number = 22
      Name = "Lautaro Martínez"
      Team = "Inter"
      Position = Forward
      Age = 25uy }
    { Number = 9
      Name = "Julián Álvarez"
      Team = "Manchester City"
      Position = Forward
      Age = 22uy }
    { Number = 21
      Name = "Paulo Dybala"
      Team = "Roma"
      Position = Forward
      Age = 29uy }
    { Number = 15
      Name = "Ángel Correa"
      Team = "Atlético Madrid"
      Position = Forward
      Age = 27uy }
    { Number = 10
      Name = "Lionel Messi"
      Team = "París Saint-Germain"
      Position = Forward
      Age = 35uy }


#### Interpolated strings

No es muy compacto... Podemos crear una función que nos permita imprimir el dato de cada jugador en forma más elegante. Vamos a usar _interpolated strings_ para hacerlo. Un _interpolated string_  es una cadena de caracteres que comienza con el símbolo `$`, y tiene el texto entre comillas dobles. En su interior se pueden usar los valores entre llaves `{}`. 

```fsharp
let saludo = "Hola"

printfn "{saludo} Mundo"
printfn $"{saludo} Mundo"
```

    {saludo} Mundo
    Hola Mundo


```fsharp
let toStringPlayer player = 
    $"{player.Number}: {player.Name} ({player.Age}), {player.Position}, juega en {player.Team}"


let prettyPrintList l = 
    l
    |> List.iter (fun p -> printfn "%s" (toStringPlayer p))  

prettyPrintList champions2022       
```

    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    1: Franco Armani (36), GoalKeeper, juega en River
    26: Nahuel Molina (24), Defender, juega en Atlético de Madrid
    4: Gonzalo Montiel (25), Defender, juega en Sevilla
    13: Cristian Romero (24), Defender, juega en Tottenham
    6: Germán Pezzella (31), Defender, juega en Betis
    19: Nicolás Otamendi (34), Defender, juega en Benfica
    25: Lisandro Martínez (24), Defender, juega en Manchester United
    8: Marcos Acuña (31), Defender, juega en Sevilla
    3: Nicolás Tagliafico (30), Defender, juega en Olympique de Lyon
    2: Juan Foyth (24), Defender, juega en Villarreal
    7: Rodrigo De Paul (28), Midfielder, juega en Atlético de Madrid
    5: Leandro Paredes (28), Midfielder, juega en Juventus
    20: Alexis Mac Allister (23), Midfielder, juega en Brighton
    18: Guido Rodríguez (28), Midfielder, juega en Betis
    17: Alejandro Gómez (34), Midfielder, juega en Sevilla
    24: Enzo Fernández (21), Midfielder, juega en Benfica
    14: Exequiel Palacios (24), Midfielder, juega en Bayer Leverkusen
    16: Thiago Almada (21), Midfielder, juega en Atlanta United
    11: Ángel Di María (34), Forward, juega en Juventus
    22: Lautaro Martínez (25), Forward, juega en Inter
    9: Julián Álvarez (22), Forward, juega en Manchester City
    21: Paulo Dybala (29), Forward, juega en Roma
    15: Ángel Correa (27), Forward, juega en Atlético Madrid
    10: Lionel Messi (35), Forward, juega en París Saint-Germain


La lista se puede ordenar:

```fsharp
champions2022 
|> List.sort 
|> prettyPrintList
```

    1: Franco Armani (36), GoalKeeper, juega en River
    2: Juan Foyth (24), Defender, juega en Villarreal
    3: Nicolás Tagliafico (30), Defender, juega en Olympique de Lyon
    4: Gonzalo Montiel (25), Defender, juega en Sevilla
    5: Leandro Paredes (28), Midfielder, juega en Juventus
    6: Germán Pezzella (31), Defender, juega en Betis
    7: Rodrigo De Paul (28), Midfielder, juega en Atlético de Madrid
    8: Marcos Acuña (31), Defender, juega en Sevilla
    9: Julián Álvarez (22), Forward, juega en Manchester City
    10: Lionel Messi (35), Forward, juega en París Saint-Germain
    11: Ángel Di María (34), Forward, juega en Juventus
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    13: Cristian Romero (24), Defender, juega en Tottenham
    14: Exequiel Palacios (24), Midfielder, juega en Bayer Leverkusen
    15: Ángel Correa (27), Forward, juega en Atlético Madrid
    16: Thiago Almada (21), Midfielder, juega en Atlanta United
    17: Alejandro Gómez (34), Midfielder, juega en Sevilla
    18: Guido Rodríguez (28), Midfielder, juega en Betis
    19: Nicolás Otamendi (34), Defender, juega en Benfica
    20: Alexis Mac Allister (23), Midfielder, juega en Brighton
    21: Paulo Dybala (29), Forward, juega en Roma
    22: Lautaro Martínez (25), Forward, juega en Inter
    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    24: Enzo Fernández (21), Midfielder, juega en Benfica
    25: Lisandro Martínez (24), Defender, juega en Manchester United
    26: Nahuel Molina (24), Defender, juega en Atlético de Madrid


Vemos que la lista aparece ordenada primero por número. En este caso, `List.sort` utiliza un comparador genérico para saber qué elemento está antes que otro. Sin embargo, la lista se puede ordenar de acuerdo a algún criterio usando `List.sortBy`:

```fsharp
champions2022 
|> List.sortBy (fun p -> p.Age)
|> prettyPrintList
```

    24: Enzo Fernández (21), Midfielder, juega en Benfica
    16: Thiago Almada (21), Midfielder, juega en Atlanta United
    9: Julián Álvarez (22), Forward, juega en Manchester City
    20: Alexis Mac Allister (23), Midfielder, juega en Brighton
    26: Nahuel Molina (24), Defender, juega en Atlético de Madrid
    13: Cristian Romero (24), Defender, juega en Tottenham
    25: Lisandro Martínez (24), Defender, juega en Manchester United
    2: Juan Foyth (24), Defender, juega en Villarreal
    14: Exequiel Palacios (24), Midfielder, juega en Bayer Leverkusen
    4: Gonzalo Montiel (25), Defender, juega en Sevilla
    22: Lautaro Martínez (25), Forward, juega en Inter
    15: Ángel Correa (27), Forward, juega en Atlético Madrid
    7: Rodrigo De Paul (28), Midfielder, juega en Atlético de Madrid
    5: Leandro Paredes (28), Midfielder, juega en Juventus
    18: Guido Rodríguez (28), Midfielder, juega en Betis
    21: Paulo Dybala (29), Forward, juega en Roma
    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    3: Nicolás Tagliafico (30), Defender, juega en Olympique de Lyon
    6: Germán Pezzella (31), Defender, juega en Betis
    8: Marcos Acuña (31), Defender, juega en Sevilla
    19: Nicolás Otamendi (34), Defender, juega en Benfica
    17: Alejandro Gómez (34), Midfielder, juega en Sevilla
    11: Ángel Di María (34), Forward, juega en Juventus
    10: Lionel Messi (35), Forward, juega en París Saint-Germain
    1: Franco Armani (36), GoalKeeper, juega en River


Quizás uno no necesita imprimir todos los datos de cada jugador:

```fsharp
champions2022 
|> List.sortBy (fun p -> p.Age)
|> List.map (fun p -> $"{p.Name} tiene {p.Age} años")
|> List.iter (fun s -> printfn "%s" s)
```

    Enzo Fernández tiene 21 años
    Thiago Almada tiene 21 años
    Julián Álvarez tiene 22 años
    Alexis Mac Allister tiene 23 años
    Nahuel Molina tiene 24 años
    Cristian Romero tiene 24 años
    Lisandro Martínez tiene 24 años
    Juan Foyth tiene 24 años
    Exequiel Palacios tiene 24 años
    Gonzalo Montiel tiene 25 años
    Lautaro Martínez tiene 25 años
    Ángel Correa tiene 27 años
    Rodrigo De Paul tiene 28 años
    Leandro Paredes tiene 28 años
    Guido Rodríguez tiene 28 años
    Paulo Dybala tiene 29 años
    Emiliano Martínez tiene 30 años
    Gerónimo Rulli tiene 30 años
    Nicolás Tagliafico tiene 30 años
    Germán Pezzella tiene 31 años
    Marcos Acuña tiene 31 años
    Nicolás Otamendi tiene 34 años
    Alejandro Gómez tiene 34 años
    Ángel Di María tiene 34 años
    Lionel Messi tiene 35 años
    Franco Armani tiene 36 años


Se puede buscar algún elemento de la lista con alguna característica particular:

```fsharp
champions2022
|> List.find (fun p -> p.Age = 21uy)

```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 24\n  Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr><tr><td>Name</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details><style>
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


```fsharp
champions2022
|> List.sortBy (fun p -> p.Number)
|> List.find (fun p -> p.Age = 21uy)
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 16\n  Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>16</pre></div></td></tr><tr><td>Name</td><td>Thiago Almada</td></tr><tr><td>Team</td><td>Atlanta United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details><style>
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


```fsharp
champions2022
|> List.find (fun p -> p.Age = 18uy)
```


    System.Collections.Generic.KeyNotFoundException: An index satisfying the predicate was not found in the collection.


       at Microsoft.FSharp.Collections.ListModule.Find[T](FSharpFunc`2 predicate, FSharpList`1 list) in D:\a\_work\1\s\src\FSharp.Core\list.fs:line 480


       at <StartupCode$FSI_0084>.$FSI_0084.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


Nótese que `List.find` devuelve el primer elemento que encuentra. Si uno quiere todos los elementos que comparten una cierta característica, usamos `List.filter`

```fsharp
champions2022
|> List.filter (fun p -> p.Age = 21uy)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 24\n  Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr><tr><td>Name</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 16\n  Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>16</pre></div></td></tr><tr><td>Name</td><td>Thiago Almada</td></tr><tr><td>Team</td><td>Atlanta United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


```fsharp
champions2022
|> List.filter (fun p -> p.Age > 25uy && p.Position = Defender)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 6\n  Name = "Germán Pezzella"\n  Team = "Betis"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>6</pre></div></td></tr><tr><td>Name</td><td>Germ&#225;n Pezzella</td></tr><tr><td>Team</td><td>Betis</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 19\n  Name = "Nicolás Otamendi"\n  Team = "Benfica"\n  Position = Defender\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>19</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Otamendi</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 8\n  Name = "Marcos Acuña"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>8</pre></div></td></tr><tr><td>Name</td><td>Marcos Acu&#241;a</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 3\n  Name = "Nicolás Tagliafico"\n  Team = "Olympique de Lyon"\n  Position = Defender\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>3</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Tagliafico</td></tr><tr><td>Team</td><td>Olympique de Lyon</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


```fsharp
let arqueros = 
    champions2022
    |> List.filter (fun p -> p.Position = GoalKeeper)

prettyPrintList arqueros    
```

    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    1: Franco Armani (36), GoalKeeper, juega en River


Podemos usar otros métodos para procesar los datos de la lista:

```fsharp
champions2022
|> List.averageBy (fun p -> p.Age)
```


    input.fsx (2,29)-(2,34) typecheck error The type 'uint8' does not support the operator 'DivideByInt'


Interesante, hay que convertir el dato a `float`:

```fsharp
champions2022
|> List.averageBy (fun p -> float p.Age)
```


<div class="dni-plaintext"><pre>27.76923076923077</pre></div><style>
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


Hay funciones que permiten procesar la lista, recolectando datos que poseen características similares.

```fsharp
champions2022
|> List.groupBy (fun p -> p.Team)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Aston Villa, [{ Number = 23\n  Name = "Emiliano Martínez"\n  Team = "Aston Villa"\n  Position = GoalKeeper\n  Age = 30uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Aston Villa</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 23\n  Name = "Emiliano Martínez"\n  Team = "Aston Villa"\n  Position = GoalKeeper\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>23</pre></div></td></tr><tr><td>Name</td><td>Emiliano Mart&#237;nez</td></tr><tr><td>Team</td><td>Aston Villa</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Villarreal, [{ Number = 12\n  Name = "Gerónimo Rulli"\n  Team = "Villarreal"\n  Position = GoalKeeper\n  Age = 30uy }; { Number = 2\n  Name = "Juan Foyth"\n  Team = "Villarreal"\n  Position = Defender\n  Age = 24uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Villarreal</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 12\n  Name = "Gerónimo Rulli"\n  Team = "Villarreal"\n  Position = GoalKeeper\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>12</pre></div></td></tr><tr><td>Name</td><td>Ger&#243;nimo Rulli</td></tr><tr><td>Team</td><td>Villarreal</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 2\n  Name = "Juan Foyth"\n  Team = "Villarreal"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr><tr><td>Name</td><td>Juan Foyth</td></tr><tr><td>Team</td><td>Villarreal</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(River, [{ Number = 1\n  Name = "Franco Armani"\n  Team = "River"\n  Position = GoalKeeper\n  Age = 36uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>River</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 1\n  Name = "Franco Armani"\n  Team = "River"\n  Position = GoalKeeper\n  Age = 36uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Name</td><td>Franco Armani</td></tr><tr><td>Team</td><td>River</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>36</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético de Madrid, [{ Number = 26\n  Name = "Nahuel Molina"\n  Team = "Atlético de Madrid"\n  Position = Defender\n  Age = 24uy }; { Number = 7\n  Name = "Rodrigo De Paul"\n  Team = "Atlético de Madrid"\n  Position = Midfielder\n  Age = 28uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 26\n  Name = "Nahuel Molina"\n  Team = "Atlético de Madrid"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>26</pre></div></td></tr><tr><td>Name</td><td>Nahuel Molina</td></tr><tr><td>Team</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 7\n  Name = "Rodrigo De Paul"\n  Team = "Atlético de Madrid"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>7</pre></div></td></tr><tr><td>Name</td><td>Rodrigo De Paul</td></tr><tr><td>Team</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Sevilla, [{ Number = 4\n  Name = "Gonzalo Montiel"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 25uy }; { Number = 8\n  Name = "Marcos Acuña"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 31uy }; { Number = 17\n  Name = "Alejandro Gómez"\n  Team = "Sevilla"\n  Position = Midfielder\n  ...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Sevilla</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 4\n  Name = "Gonzalo Montiel"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 25uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>4</pre></div></td></tr><tr><td>Name</td><td>Gonzalo Montiel</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 8\n  Name = "Marcos Acuña"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>8</pre></div></td></tr><tr><td>Name</td><td>Marcos Acu&#241;a</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 17\n  Name = "Alejandro Gómez"\n  Team = "Sevilla"\n  Position = Midfielder\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>17</pre></div></td></tr><tr><td>Name</td><td>Alejandro G&#243;mez</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Tottenham, [{ Number = 13\n  Name = "Cristian Romero"\n  Team = "Tottenham"\n  Position = Defender\n  Age = 24uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Tottenham</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 13\n  Name = "Cristian Romero"\n  Team = "Tottenham"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>13</pre></div></td></tr><tr><td>Name</td><td>Cristian Romero</td></tr><tr><td>Team</td><td>Tottenham</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Betis, [{ Number = 6\n  Name = "Germán Pezzella"\n  Team = "Betis"\n  Position = Defender\n  Age = 31uy }; { Number = 18\n  Name = "Guido Rodríguez"\n  Team = "Betis"\n  Position = Midfielder\n  Age = 28uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Betis</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 6\n  Name = "Germán Pezzella"\n  Team = "Betis"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>6</pre></div></td></tr><tr><td>Name</td><td>Germ&#225;n Pezzella</td></tr><tr><td>Team</td><td>Betis</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 18\n  Name = "Guido Rodríguez"\n  Team = "Betis"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>18</pre></div></td></tr><tr><td>Name</td><td>Guido Rodr&#237;guez</td></tr><tr><td>Team</td><td>Betis</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Benfica, [{ Number = 19\n  Name = "Nicolás Otamendi"\n  Team = "Benfica"\n  Position = Defender\n  Age = 34uy }; { Number = 24\n  Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Benfica</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 19\n  Name = "Nicolás Otamendi"\n  Team = "Benfica"\n  Position = Defender\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>19</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Otamendi</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 24\n  Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr><tr><td>Name</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester United, [{ Number = 25\n  Name = "Lisandro Martínez"\n  Team = "Manchester United"\n  Position = Defender\n  Age = 24uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester United</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 25\n  Name = "Lisandro Martínez"\n  Team = "Manchester United"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr><tr><td>Name</td><td>Lisandro Mart&#237;nez</td></tr><tr><td>Team</td><td>Manchester United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Olympique de Lyon, [{ Number = 3\n  Name = "Nicolás Tagliafico"\n  Team = "Olympique de Lyon"\n  Position = Defender\n  Age = 30uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Olympique de Lyon</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 3\n  Name = "Nicolás Tagliafico"\n  Team = "Olympique de Lyon"\n  Position = Defender\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>3</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Tagliafico</td></tr><tr><td>Team</td><td>Olympique de Lyon</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Juventus, [{ Number = 5\n  Name = "Leandro Paredes"\n  Team = "Juventus"\n  Position = Midfielder\n  Age = 28uy }; { Number = 11\n  Name = "Ángel Di María"\n  Team = "Juventus"\n  Position = Forward\n  Age = 34uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Juventus</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 5\n  Name = "Leandro Paredes"\n  Team = "Juventus"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>5</pre></div></td></tr><tr><td>Name</td><td>Leandro Paredes</td></tr><tr><td>Team</td><td>Juventus</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 11\n  Name = "Ángel Di María"\n  Team = "Juventus"\n  Position = Forward\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>11</pre></div></td></tr><tr><td>Name</td><td>&#193;ngel Di Mar&#237;a</td></tr><tr><td>Team</td><td>Juventus</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Brighton, [{ Number = 20\n  Name = "Alexis Mac Allister"\n  Team = "Brighton"\n  Position = Midfielder\n  Age = 23uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Brighton</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 20\n  Name = "Alexis Mac Allister"\n  Team = "Brighton"\n  Position = Midfielder\n  Age = 23uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>20</pre></div></td></tr><tr><td>Name</td><td>Alexis Mac Allister</td></tr><tr><td>Team</td><td>Brighton</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>23</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Bayer Leverkusen, [{ Number = 14\n  Name = "Exequiel Palacios"\n  Team = "Bayer Leverkusen"\n  Position = Midfielder\n  Age = 24uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Bayer Leverkusen</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 14\n  Name = "Exequiel Palacios"\n  Team = "Bayer Leverkusen"\n  Position = Midfielder\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>14</pre></div></td></tr><tr><td>Name</td><td>Exequiel Palacios</td></tr><tr><td>Team</td><td>Bayer Leverkusen</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlanta United, [{ Number = 16\n  Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atlanta United</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 16\n  Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>16</pre></div></td></tr><tr><td>Name</td><td>Thiago Almada</td></tr><tr><td>Team</td><td>Atlanta United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Inter, [{ Number = 22\n  Name = "Lautaro Martínez"\n  Team = "Inter"\n  Position = Forward\n  Age = 25uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Inter</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 22\n  Name = "Lautaro Martínez"\n  Team = "Inter"\n  Position = Forward\n  Age = 25uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>22</pre></div></td></tr><tr><td>Name</td><td>Lautaro Mart&#237;nez</td></tr><tr><td>Team</td><td>Inter</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester City, [{ Number = 9\n  Name = "Julián Álvarez"\n  Team = "Manchester City"\n  Position = Forward\n  Age = 22uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester City</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 9\n  Name = "Julián Álvarez"\n  Team = "Manchester City"\n  Position = Forward\n  Age = 22uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>9</pre></div></td></tr><tr><td>Name</td><td>Juli&#225;n &#193;lvarez</td></tr><tr><td>Team</td><td>Manchester City</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>22</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Roma, [{ Number = 21\n  Name = "Paulo Dybala"\n  Team = "Roma"\n  Position = Forward\n  Age = 29uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Roma</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 21\n  Name = "Paulo Dybala"\n  Team = "Roma"\n  Position = Forward\n  Age = 29uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr><tr><td>Name</td><td>Paulo Dybala</td></tr><tr><td>Team</td><td>Roma</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>29</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético Madrid, [{ Number = 15\n  Name = "Ángel Correa"\n  Team = "Atlético Madrid"\n  Position = Forward\n  Age = 27uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico Madrid</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 15\n  Name = "Ángel Correa"\n  Team = "Atlético Madrid"\n  Position = Forward\n  Age = 27uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>15</pre></div></td></tr><tr><td>Name</td><td>&#193;ngel Correa</td></tr><tr><td>Team</td><td>Atl&#233;tico Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>27</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>18</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(París Saint-Germain, [{ Number = 10\n  Name = "Lionel Messi"\n  Team = "París Saint-Germain"\n  Position = Forward\n  Age = 35uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Par&#237;s Saint-Germain</td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 10\n  Name = "Lionel Messi"\n  Team = "París Saint-Germain"\n  Position = Forward\n  Age = 35uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>10</pre></div></td></tr><tr><td>Name</td><td>Lionel Messi</td></tr><tr><td>Team</td><td>Par&#237;s Saint-Germain</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>35</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


La función `List.groupBy` no devuelve una lista de jugadores, sino una lista de pares ordenados (tuplas), donde el primer elemento sería la llave común del tipo de dato recolectado, y el segundo elemento es una lista de elementos que corresponden a esa llave:

```fsharp
champions2022
|> List.groupBy (fun p -> p.Team)
|> List.iter (fun (team,l) -> 
                printfn "%s" team 
                prettyPrintList l
                )
```

    Aston Villa
    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    Villarreal
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    2: Juan Foyth (24), Defender, juega en Villarreal
    River
    1: Franco Armani (36), GoalKeeper, juega en River
    Atlético de Madrid
    26: Nahuel Molina (24), Defender, juega en Atlético de Madrid
    7: Rodrigo De Paul (28), Midfielder, juega en Atlético de Madrid
    Sevilla
    4: Gonzalo Montiel (25), Defender, juega en Sevilla
    8: Marcos Acuña (31), Defender, juega en Sevilla
    17: Alejandro Gómez (34), Midfielder, juega en Sevilla
    Tottenham
    13: Cristian Romero (24), Defender, juega en Tottenham
    Betis
    6: Germán Pezzella (31), Defender, juega en Betis
    18: Guido Rodríguez (28), Midfielder, juega en Betis
    Benfica
    19: Nicolás Otamendi (34), Defender, juega en Benfica
    24: Enzo Fernández (21), Midfielder, juega en Benfica
    Manchester United
    25: Lisandro Martínez (24), Defender, juega en Manchester United
    Olympique de Lyon
    3: Nicolás Tagliafico (30), Defender, juega en Olympique de Lyon
    Juventus
    5: Leandro Paredes (28), Midfielder, juega en Juventus
    11: Ángel Di María (34), Forward, juega en Juventus
    Brighton
    20: Alexis Mac Allister (23), Midfielder, juega en Brighton
    Bayer Leverkusen
    14: Exequiel Palacios (24), Midfielder, juega en Bayer Leverkusen
    Atlanta United
    16: Thiago Almada (21), Midfielder, juega en Atlanta United
    Inter
    22: Lautaro Martínez (25), Forward, juega en Inter
    Manchester City
    9: Julián Álvarez (22), Forward, juega en Manchester City
    Roma
    21: Paulo Dybala (29), Forward, juega en Roma
    Atlético Madrid
    15: Ángel Correa (27), Forward, juega en Atlético Madrid
    París Saint-Germain
    10: Lionel Messi (35), Forward, juega en París Saint-Germain


```fsharp
champions2022
|> List.groupBy (fun p -> p.Team)
|> List.iter (fun t -> 
                printfn "%s" (fst t)
                prettyPrintList (snd t)
                )
```

    Aston Villa
    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa
    Villarreal
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    2: Juan Foyth (24), Defender, juega en Villarreal
    River
    1: Franco Armani (36), GoalKeeper, juega en River
    Atlético de Madrid
    26: Nahuel Molina (24), Defender, juega en Atlético de Madrid
    7: Rodrigo De Paul (28), Midfielder, juega en Atlético de Madrid
    Sevilla
    4: Gonzalo Montiel (25), Defender, juega en Sevilla
    8: Marcos Acuña (31), Defender, juega en Sevilla
    17: Alejandro Gómez (34), Midfielder, juega en Sevilla
    Tottenham
    13: Cristian Romero (24), Defender, juega en Tottenham
    Betis
    6: Germán Pezzella (31), Defender, juega en Betis
    18: Guido Rodríguez (28), Midfielder, juega en Betis
    Benfica
    19: Nicolás Otamendi (34), Defender, juega en Benfica
    24: Enzo Fernández (21), Midfielder, juega en Benfica
    Manchester United
    25: Lisandro Martínez (24), Defender, juega en Manchester United
    Olympique de Lyon
    3: Nicolás Tagliafico (30), Defender, juega en Olympique de Lyon
    Juventus
    5: Leandro Paredes (28), Midfielder, juega en Juventus
    11: Ángel Di María (34), Forward, juega en Juventus
    Brighton
    20: Alexis Mac Allister (23), Midfielder, juega en Brighton
    Bayer Leverkusen
    14: Exequiel Palacios (24), Midfielder, juega en Bayer Leverkusen
    Atlanta United
    16: Thiago Almada (21), Midfielder, juega en Atlanta United
    Inter
    22: Lautaro Martínez (25), Forward, juega en Inter
    Manchester City
    9: Julián Álvarez (22), Forward, juega en Manchester City
    Roma
    21: Paulo Dybala (29), Forward, juega en Roma
    Atlético Madrid
    15: Ángel Correa (27), Forward, juega en Atlético Madrid
    París Saint-Germain
    10: Lionel Messi (35), Forward, juega en París Saint-Germain


Veamos cuántos jugadores aportó cada equipo:

```fsharp
champions2022
|> List.groupBy (fun p -> p.Team)
|> List.map (fun (t,l) -> (t, l |> List.length))

```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Aston Villa, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Aston Villa</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Villarreal, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Villarreal</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(River, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>River</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético de Madrid, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Sevilla, 3)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Sevilla</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>3</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Tottenham, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Tottenham</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Betis, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Betis</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Benfica, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Benfica</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester United, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester United</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Olympique de Lyon, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Olympique de Lyon</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Juventus, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Juventus</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Brighton, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Brighton</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Bayer Leverkusen, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Bayer Leverkusen</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlanta United, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atlanta United</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Inter, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Inter</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester City, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester City</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Roma, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Roma</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético Madrid, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico Madrid</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>18</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(París Saint-Germain, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Par&#237;s Saint-Germain</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


Este último procedimiento se puede resumir con `List.countBy`:

```fsharp
champions2022
|> List.countBy (fun p -> p.Team)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Aston Villa, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Aston Villa</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Villarreal, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Villarreal</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(River, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>River</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético de Madrid, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Sevilla, 3)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Sevilla</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>3</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Tottenham, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Tottenham</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Betis, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Betis</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Benfica, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Benfica</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester United, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester United</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Olympique de Lyon, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Olympique de Lyon</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Juventus, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Juventus</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Brighton, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Brighton</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Bayer Leverkusen, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Bayer Leverkusen</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlanta United, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atlanta United</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Inter, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Inter</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Manchester City, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Manchester City</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Roma, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Roma</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Atlético Madrid, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Atl&#233;tico Madrid</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>18</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(París Saint-Germain, 1)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Par&#237;s Saint-Germain</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


Se puede construir un histograma de edades:

```fsharp
champions2022
|> List.groupBy (fun p -> p.Age/5uy * 5uy)

```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(30, [{ Number = 23\n  Name = "Emiliano Martínez"\n  Team = "Aston Villa"\n  Position = GoalKeeper\n  Age = 30uy }; { Number = 12\n  Name = "Gerónimo Rulli"\n  Team = "Villarreal"\n  Position = GoalKeeper\n  Age = 30uy }; { Number = 6\n  Name = "Germán Pezzella"\n  Team = "Betis"\n  Position = Defen...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 23\n  Name = "Emiliano Martínez"\n  Team = "Aston Villa"\n  Position = GoalKeeper\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>23</pre></div></td></tr><tr><td>Name</td><td>Emiliano Mart&#237;nez</td></tr><tr><td>Team</td><td>Aston Villa</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 12\n  Name = "Gerónimo Rulli"\n  Team = "Villarreal"\n  Position = GoalKeeper\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>12</pre></div></td></tr><tr><td>Name</td><td>Ger&#243;nimo Rulli</td></tr><tr><td>Team</td><td>Villarreal</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 6\n  Name = "Germán Pezzella"\n  Team = "Betis"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>6</pre></div></td></tr><tr><td>Name</td><td>Germ&#225;n Pezzella</td></tr><tr><td>Team</td><td>Betis</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 19\n  Name = "Nicolás Otamendi"\n  Team = "Benfica"\n  Position = Defender\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>19</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Otamendi</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 8\n  Name = "Marcos Acuña"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 31uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>8</pre></div></td></tr><tr><td>Name</td><td>Marcos Acu&#241;a</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 3\n  Name = "Nicolás Tagliafico"\n  Team = "Olympique de Lyon"\n  Position = Defender\n  Age = 30uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>3</pre></div></td></tr><tr><td>Name</td><td>Nicol&#225;s Tagliafico</td></tr><tr><td>Team</td><td>Olympique de Lyon</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 17\n  Name = "Alejandro Gómez"\n  Team = "Sevilla"\n  Position = Midfielder\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>17</pre></div></td></tr><tr><td>Name</td><td>Alejandro G&#243;mez</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 11\n  Name = "Ángel Di María"\n  Team = "Juventus"\n  Position = Forward\n  Age = 34uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>11</pre></div></td></tr><tr><td>Name</td><td>&#193;ngel Di Mar&#237;a</td></tr><tr><td>Team</td><td>Juventus</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(35, [{ Number = 1\n  Name = "Franco Armani"\n  Team = "River"\n  Position = GoalKeeper\n  Age = 36uy }; { Number = 10\n  Name = "Lionel Messi"\n  Team = "París Saint-Germain"\n  Position = Forward\n  Age = 35uy }])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>35</pre></div></td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 1\n  Name = "Franco Armani"\n  Team = "River"\n  Position = GoalKeeper\n  Age = 36uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>1</pre></div></td></tr><tr><td>Name</td><td>Franco Armani</td></tr><tr><td>Team</td><td>River</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>GoalKeeper</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>36</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 10\n  Name = "Lionel Messi"\n  Team = "París Saint-Germain"\n  Position = Forward\n  Age = 35uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>10</pre></div></td></tr><tr><td>Name</td><td>Lionel Messi</td></tr><tr><td>Team</td><td>Par&#237;s Saint-Germain</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>35</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(20, [{ Number = 26\n  Name = "Nahuel Molina"\n  Team = "Atlético de Madrid"\n  Position = Defender\n  Age = 24uy }; { Number = 13\n  Name = "Cristian Romero"\n  Team = "Tottenham"\n  Position = Defender\n  Age = 24uy }; { Number = 25\n  Name = "Lisandro Martínez"\n  Team = "Manchester United"\n  Po...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>20</pre></div></td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 26\n  Name = "Nahuel Molina"\n  Team = "Atlético de Madrid"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>26</pre></div></td></tr><tr><td>Name</td><td>Nahuel Molina</td></tr><tr><td>Team</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 13\n  Name = "Cristian Romero"\n  Team = "Tottenham"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>13</pre></div></td></tr><tr><td>Name</td><td>Cristian Romero</td></tr><tr><td>Team</td><td>Tottenham</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 25\n  Name = "Lisandro Martínez"\n  Team = "Manchester United"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr><tr><td>Name</td><td>Lisandro Mart&#237;nez</td></tr><tr><td>Team</td><td>Manchester United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 2\n  Name = "Juan Foyth"\n  Team = "Villarreal"\n  Position = Defender\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr><tr><td>Name</td><td>Juan Foyth</td></tr><tr><td>Team</td><td>Villarreal</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 20\n  Name = "Alexis Mac Allister"\n  Team = "Brighton"\n  Position = Midfielder\n  Age = 23uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>20</pre></div></td></tr><tr><td>Name</td><td>Alexis Mac Allister</td></tr><tr><td>Team</td><td>Brighton</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>23</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 24\n  Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr><tr><td>Name</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 14\n  Name = "Exequiel Palacios"\n  Team = "Bayer Leverkusen"\n  Position = Midfielder\n  Age = 24uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>14</pre></div></td></tr><tr><td>Name</td><td>Exequiel Palacios</td></tr><tr><td>Team</td><td>Bayer Leverkusen</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 16\n  Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>16</pre></div></td></tr><tr><td>Name</td><td>Thiago Almada</td></tr><tr><td>Team</td><td>Atlanta United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 9\n  Name = "Julián Álvarez"\n  Team = "Manchester City"\n  Position = Forward\n  Age = 22uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>9</pre></div></td></tr><tr><td>Name</td><td>Juli&#225;n &#193;lvarez</td></tr><tr><td>Team</td><td>Manchester City</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>22</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(25, [{ Number = 4\n  Name = "Gonzalo Montiel"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 25uy }; { Number = 7\n  Name = "Rodrigo De Paul"\n  Team = "Atlético de Madrid"\n  Position = Midfielder\n  Age = 28uy }; { Number = 5\n  Name = "Leandro Paredes"\n  Team = "Juventus"\n  Position = Mid...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 4\n  Name = "Gonzalo Montiel"\n  Team = "Sevilla"\n  Position = Defender\n  Age = 25uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>4</pre></div></td></tr><tr><td>Name</td><td>Gonzalo Montiel</td></tr><tr><td>Team</td><td>Sevilla</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Defender</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 7\n  Name = "Rodrigo De Paul"\n  Team = "Atlético de Madrid"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>7</pre></div></td></tr><tr><td>Name</td><td>Rodrigo De Paul</td></tr><tr><td>Team</td><td>Atl&#233;tico de Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 5\n  Name = "Leandro Paredes"\n  Team = "Juventus"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>5</pre></div></td></tr><tr><td>Name</td><td>Leandro Paredes</td></tr><tr><td>Team</td><td>Juventus</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 18\n  Name = "Guido Rodríguez"\n  Team = "Betis"\n  Position = Midfielder\n  Age = 28uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>18</pre></div></td></tr><tr><td>Name</td><td>Guido Rodr&#237;guez</td></tr><tr><td>Team</td><td>Betis</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 22\n  Name = "Lautaro Martínez"\n  Team = "Inter"\n  Position = Forward\n  Age = 25uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>22</pre></div></td></tr><tr><td>Name</td><td>Lautaro Mart&#237;nez</td></tr><tr><td>Team</td><td>Inter</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 21\n  Name = "Paulo Dybala"\n  Team = "Roma"\n  Position = Forward\n  Age = 29uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr><tr><td>Name</td><td>Paulo Dybala</td></tr><tr><td>Team</td><td>Roma</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>29</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Number = 15\n  Name = "Ángel Correa"\n  Team = "Atlético Madrid"\n  Position = Forward\n  Age = 27uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Number</td><td><div class="dni-plaintext"><pre>15</pre></div></td></tr><tr><td>Name</td><td>&#193;ngel Correa</td></tr><tr><td>Team</td><td>Atl&#233;tico Madrid</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>27</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


```fsharp
champions2022
|> List.groupBy (fun p -> p.Age/5uy * 5uy)
|> List.map (fun (t,l) -> (t, l.Length))
|> List.sort 
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(20, 9)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>20</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>9</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(25, 7)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>7</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(30, 8)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>8</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(35, 2)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>35</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


La función `List.partition` nos permite agrupar los elementos en dos listas con características disjuntas:

```fsharp
champions2022
|> List.map (fun p -> (p.Name,p.Age))
|> List.partition (fun (name,age) -> age < 30uy)


```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>([(Nahuel Molina, 24); (Gonzalo Montiel, 25); (Cristian Romero, 24); ... ], [(Emiliano Martínez, 30); (Gerónimo Rulli, 30); (Franco Armani, 36); ... ])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Nahuel Molina, 24)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Nahuel Molina</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Gonzalo Montiel, 25)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Gonzalo Montiel</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Cristian Romero, 24)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Cristian Romero</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Lisandro Martínez, 24)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Lisandro Mart&#237;nez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Juan Foyth, 24)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Juan Foyth</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Rodrigo De Paul, 28)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Rodrigo De Paul</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Leandro Paredes, 28)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Leandro Paredes</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Alexis Mac Allister, 23)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Alexis Mac Allister</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>23</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Guido Rodríguez, 28)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Guido Rodr&#237;guez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>28</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Enzo Fernández, 21)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Exequiel Palacios, 24)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Exequiel Palacios</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>24</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Thiago Almada, 21)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Thiago Almada</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Lautaro Martínez, 25)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Lautaro Mart&#237;nez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Julián Álvarez, 22)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Juli&#225;n &#193;lvarez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>22</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Paulo Dybala, 29)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Paulo Dybala</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>29</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Ángel Correa, 27)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>&#193;ngel Correa</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>27</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr><tr><td>Item2</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Emiliano Martínez, 30)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Emiliano Mart&#237;nez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Gerónimo Rulli, 30)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Ger&#243;nimo Rulli</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Franco Armani, 36)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Franco Armani</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>36</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Germán Pezzella, 31)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Germ&#225;n Pezzella</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Nicolás Otamendi, 34)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Nicol&#225;s Otamendi</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Marcos Acuña, 31)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Marcos Acu&#241;a</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>31</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Nicolás Tagliafico, 30)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Nicol&#225;s Tagliafico</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>30</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Alejandro Gómez, 34)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Alejandro G&#243;mez</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Ángel Di María, 34)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>&#193;ngel Di Mar&#237;a</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>34</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(Lionel Messi, 35)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>Lionel Messi</td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>35</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></td></tr></tbody></table></div></details><style>
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


```fsharp
let menoresde30, mayoresde30 = 
    champions2022
    |> List.map (fun p -> (p.Name,p.Age))
    |> List.partition (fun (name,age) -> age < 30uy)

printfn $"Los menores de 30 son {menoresde30.Length}"
printfn $"Los mayores de 30 son {mayoresde30.Length}"
```

    Los menores de 30 son 16
    Los mayores de 30 son 10


Finalmente, algunas funciones nos permiten evaluar una determinada característica de una lista:

```fsharp
champions2022
|> List.forall (fun p -> p.Age < 30uy)
```


<div class="dni-plaintext"><pre>False</pre></div><style>
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


```fsharp
menoresde30
|> List.forall (fun (_,a) -> a < 30uy)

```


<div class="dni-plaintext"><pre>False</pre></div><style>
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


```fsharp
List.findIndex
```
