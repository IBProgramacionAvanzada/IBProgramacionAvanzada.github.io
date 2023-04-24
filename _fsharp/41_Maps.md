---
layout: post
title: Diccionarios 
tagline: Juntando datos
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Un `Map` es un diccionario, de modo tal que se accede a los datos a través de una llave. 

```fsharp
let m1 = Map [1, "Apples" ; 2, "Bananas"]

printfn "%A" m1 
printfn "%A" m1[1] 

```

    map [(1, "Apples"); (2, "Bananas")]
    "Apples"


```fsharp
printfn "%A" m1.[5]
```


    System.Collections.Generic.KeyNotFoundException: The given key was not present in the dictionary.


       at Microsoft.FSharp.Collections.MapTreeModule.throwKeyNotFound[a]() in D:\a\_work\1\s\src\FSharp.Core\map.fs:line 187


       at Microsoft.FSharp.Collections.MapTreeModule.find[TKey,TValue](IComparer`1 comparer, TKey k, MapTree`2 m) in D:\a\_work\1\s\src\FSharp.Core\map.fs:line 196


       at <StartupCode$FSI_0042>.$FSI_0042.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


A diferencia de las listas, es posible crear nuevos diccionarios usando funciones para agregar o cambiar ciertos valores:

```fsharp
let m2 = m1.Add(3,"Oranges")
printfn "%A" m2

```

    map [(1, "Apples"); (2, "Bananas"); (3, "Oranges")]


```fsharp
let m3 = Map.add 2 "Peaches" m2 
printfn "%A" m2
printfn "%A" m3

```

    map [(1, "Apples"); (2, "Bananas"); (3, "Oranges")]
    map [(1, "Apples"); (2, "Peaches"); (3, "Oranges")]


Veamos algunas funciones similares con el ejemplo de los ⭐️⭐️⭐️:

```fsharp
type Position = GoalKeeper | Defender | Midfielder | Forward 

type Player =
    {
        Name : string
        Team: string 
        Position: Position 
        Age: uint8 
    }
```

```fsharp
let champions2022 = Map [// Los 3 arqueros de Argentina en Qatar 2022
     23, { Name = "Emiliano Martínez" ;  Team = "Aston Villa"; Position = GoalKeeper; Age = 30uy};
     12, { Name = "Gerónimo Rulli" ;  Team = "Villarreal"; Position = GoalKeeper; Age = 30uy};
     1, { Name = "Franco Armani" ;  Team = "River"; Position = GoalKeeper; Age = 36uy};
    // Los 9 defensores de Argentina en Qatar 2022
     26, { Name = "Nahuel Molina" ;  Team = "Atlético de Madrid"; Position = Defender; Age = 24uy};
     4, { Name = "Gonzalo Montiel" ;  Team = "Sevilla"; Position = Defender; Age = 25uy};
     13, { Name = "Cristian Romero" ;  Team = "Tottenham"; Position = Defender; Age = 24uy};
     6, { Name = "Germán Pezzella" ;  Team = "Betis"; Position = Defender; Age = 31uy};
     19, { Name = "Nicolás Otamendi" ;  Team = "Benfica"; Position = Defender; Age = 34uy};
     25, { Name = "Lisandro Martínez" ;  Team = "Manchester United"; Position = Defender; Age = 24uy};
     8, { Name = "Marcos Acuña" ;  Team = "Sevilla"; Position = Defender; Age = 31uy};
     3, { Name = "Nicolás Tagliafico" ;  Team = "Olympique de Lyon"; Position = Defender; Age = 30uy};
     2, { Name = "Juan Foyth" ;  Team = "Villarreal"; Position = Defender; Age = 24uy};
    // Los 8 mediocampistas de Argentina en Qatar 2022
     7, { Name = "Rodrigo De Paul" ;  Team = "Atlético de Madrid"; Position = Midfielder; Age = 28uy};
     5, { Name = "Leandro Paredes" ;  Team = "Juventus"; Position = Midfielder; Age = 28uy};
     20, { Name = "Alexis Mac Allister" ;  Team = "Brighton"; Position = Midfielder; Age = 23uy};
     18, { Name = "Guido Rodríguez" ;  Team = "Betis"; Position = Midfielder; Age = 28uy};
     17, { Name = "Alejandro Gómez" ;  Team = "Sevilla"; Position = Midfielder; Age = 34uy};
     24, { Name = "Enzo Fernández" ;  Team = "Benfica"; Position = Midfielder; Age = 21uy};
     14, { Name = "Exequiel Palacios" ;  Team = "Bayer Leverkusen"; Position = Midfielder; Age = 24uy};
     16, { Name = "Thiago Almada" ;  Team = "Atlanta United"; Position = Midfielder; Age = 21uy};
    // Los 6 delanteros de Argentina en Qatar 2022
     11, { Name = "Ángel Di María" ;  Team = "Juventus"; Position = Forward; Age = 34uy};
     22, { Name = "Lautaro Martínez" ;  Team = "Inter"; Position = Forward; Age = 25uy};
     9, { Name = "Julián Álvarez" ;  Team = "Manchester City"; Position = Forward; Age = 22uy};
     21, { Name = "Paulo Dybala" ;  Team = "Roma"; Position = Forward; Age = 29uy};
     15, { Name = "Ángel Correa" ;  Team = "Atlético Madrid"; Position = Forward; Age = 27uy};
     10, { Name = "Lionel Messi" ;  Team = "París Saint-Germain"; Position = Forward; Age = 35uy};
]
```

```fsharp
let toStringPlayer number player = 
    $"{number}: {player.Name} ({player.Age}), {player.Position}, juega en {player.Team}"


let prettyPrintMap m = 
    m
    |> Map.iter (fun n p -> printfn "%s" (toStringPlayer n p))  

prettyPrintMap champions2022       
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


```fsharp
champions2022 
|> Map.toList 
|> List.sortBy (fun (n,p) -> n)
|> Map.ofList
|> prettyPrintMap
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


Quizás uno no necesita imprimir todos los datos de cada jugador:

```fsharp
champions2022 
|> Map.map (fun _ p -> $"{p.Name} tiene {p.Age} años")
|> Map.iter (fun _ s -> printfn "%s" s)
```

    Franco Armani tiene 36 años
    Juan Foyth tiene 24 años
    Nicolás Tagliafico tiene 30 años
    Gonzalo Montiel tiene 25 años
    Leandro Paredes tiene 28 años
    Germán Pezzella tiene 31 años
    Rodrigo De Paul tiene 28 años
    Marcos Acuña tiene 31 años
    Julián Álvarez tiene 22 años
    Lionel Messi tiene 35 años
    Ángel Di María tiene 34 años
    Gerónimo Rulli tiene 30 años
    Cristian Romero tiene 24 años
    Exequiel Palacios tiene 24 años
    Ángel Correa tiene 27 años
    Thiago Almada tiene 21 años
    Alejandro Gómez tiene 34 años
    Guido Rodríguez tiene 28 años
    Nicolás Otamendi tiene 34 años
    Alexis Mac Allister tiene 23 años
    Paulo Dybala tiene 29 años
    Lautaro Martínez tiene 25 años
    Emiliano Martínez tiene 30 años
    Enzo Fernández tiene 21 años
    Lisandro Martínez tiene 24 años
    Nahuel Molina tiene 24 años


Se puede buscar algún elemento del diccionario a través de la llave:

```fsharp
champions2022
|> Map.find 22

```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Name = "Lautaro Martínez"\n  Team = "Inter"\n  Position = Forward\n  Age = 25uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Name</td><td>Lautaro Mart&#237;nez</td></tr><tr><td>Team</td><td>Inter</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Forward</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>25</pre></div></td></tr></tbody></table></div></details><style>
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
|> Map.find 31
```


    System.Collections.Generic.KeyNotFoundException: The given key was not present in the dictionary.


       at Microsoft.FSharp.Collections.MapTreeModule.throwKeyNotFound[a]() in D:\a\_work\1\s\src\FSharp.Core\map.fs:line 187


       at Microsoft.FSharp.Collections.MapTreeModule.find[TKey,TValue](IComparer`1 comparer, TKey k, MapTree`2 m) in D:\a\_work\1\s\src\FSharp.Core\map.fs:line 196


       at Microsoft.FSharp.Collections.MapModule.Find[TKey,T](TKey key, FSharpMap`2 table) in D:\a\_work\1\s\src\FSharp.Core\map.fs:line 1148


       at <StartupCode$FSI_0029>.$FSI_0029.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


Nótese que `Map.find` devuelve obviamente el único elemento que encuentra. Si uno quiere todos los elementos que comparten una cierta característica, usamos `Map.filter`

```fsharp
champions2022
|> Map.filter (fun _ p -> p.Age = 21uy)
```


<table><thead><tr><th><i>key</i></th><th>value</th></tr></thead><tbody><tr><td><div class="dni-plaintext"><pre>16</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Name = "Thiago Almada"\n  Team = "Atlanta United"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Name</td><td>Thiago Almada</td></tr><tr><td>Team</td><td>Atlanta United</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td><div class="dni-plaintext"><pre>24</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Name = "Enzo Fernández"\n  Team = "Benfica"\n  Position = Midfielder\n  Age = 21uy }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Name</td><td>Enzo Fern&#225;ndez</td></tr><tr><td>Team</td><td>Benfica</td></tr><tr><td>Position</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Midfielder</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr><tr><td>Age</td><td><div class="dni-plaintext"><pre>21</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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
    |> Map.filter (fun _ p -> p.Position = GoalKeeper)

prettyPrintMap arqueros    
```

    1: Franco Armani (36), GoalKeeper, juega en River
    12: Gerónimo Rulli (30), GoalKeeper, juega en Villarreal
    23: Emiliano Martínez (30), GoalKeeper, juega en Aston Villa


Podemos usar otros métodos para procesar los datos del diccionario, pero puede ser necesario convertirlas a una la lista con `Map.ofList`:

```fsharp
champions2022
|> Map.toList 
|> List.averageBy (fun (_,p) -> float p.Age)
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


La función `Map.partition` nos permite agrupar los elementos en dos diccionarios con características disjuntas:

```fsharp
let menoresde30, mayoresde30 = 
    champions2022
    |> Map.map (fun _ p -> (p.Name,p.Age))
    |> Map.partition (fun _ (name,age) -> age < 30uy)

printfn $"Los menores de 30 son {menoresde30.Count}"
printfn $"Los mayores de 30 son {mayoresde30.Count}"
```

    Los menores de 30 son 16
    Los mayores de 30 son 10


Finalmente, algunas funciones nos permiten evaluar una determinada característica de un diccionario:

```fsharp
champions2022
|> Map.forall (fun _ p -> p.Age < 30uy)
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

