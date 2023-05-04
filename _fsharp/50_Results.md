---
layout: post
title: El tipo Result 
tagline: Cuando las cosas no salen bien
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Hemos visto que las excepciones que emite un programa pueden alterar el flujo de trabajo del mismo. En el caso en que las excepciones no son capturadas, el programa simplemente termina, con los consiguientes perjucios para el usuario.

Vimos también que tenemos la posibilidad de emitir nuestras propias excepciones, sin utilizar las propias del sistema operativo. 

En el ejemplo previo, teníamos una función `divide` que emitía cierto tipo de excepciones de acuerdo a casos que son errores (ya sea porque se trata de realizar una operación prohibida), o casos en los cuales el caso de uso no está permitido por el sistema (como dividir por uno).  




```fsharp
exception DivideByOne of string 

let divide x y =
    match y with 
    | 0.0 -> invalidOp "Trying to divide by zero!!" 
    | 1.0 -> raise (DivideByOne "Can't believe you are trying to divide by one")
    | _ -> x/y 

```

Por otra parte, teníamos la función `annoyedDivide` que es quien llama a `divide` y es la encargada de capturar los posibles errores a través de excepciones:

```fsharp
let annoyedDivide x y =
    try 
        divide x y 
    with 
    | DivideByOne s -> 
        printfn "%A" (s)
        -1.0
    | :? System.InvalidOperationException as ex -> 
        printfn "Message: %A" (ex.Message)
        -1.0          

        
```

Qué pasaría si además de dividir dos números, tenemos que calcular la raíz cuadrada del resultado? Tendríamos que usar la función `sqrt`

```fsharp
printfn $"Sqrt 4: {sqrt 4.0}"
printfn $"Sqrt 4: {sqrt -4.0}"
```

    Sqrt 4: 2
    Sqrt 4: NaN


Pero tenemos que validar que el input de la función no sea negativo:

```fsharp
exception CannotSqrtOfNegative of string 


let squareRoot (x: float) = 
    if x < 0 then 
        raise (CannotSqrtOfNegative "Cannot compute sqrt of a negative number")
    else 
        sqrt x         
```

```fsharp
printfn "%A" (squareRoot 4.0)
printfn "%A" (squareRoot -4.0)
```

    2.0



    FSI_0012+CannotSqrtOfNegative: CannotSqrtOfNegative "Cannot compute sqrt of a negative number"


       at FSI_0012.squareRoot(Double x)


       at <StartupCode$FSI_0046>.$FSI_0046.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


```fsharp
let annoyedSqrt x =
    try 
        squareRoot x 
    with 
    | CannotSqrtOfNegative s -> 
        printfn "Message: %A" s
        -1 

```

Una vez más nos encontramos con el problema sobre qué retornar cuando se captura la excepción.

```fsharp
let x = 5.0
let y = 3.0 

sqrt (x/y)
```


<div class="dni-plaintext"><pre>1.2909944487358056</pre></div><style>
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
let x = 5
let y = 0 

sqrt  (annoyedDivide x y)
```

    Message: "Trying to divide by zero!!"



<div class="dni-plaintext"><pre>NaN</pre></div><style>
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
let x = -5
let y = 3 

annoyedSqrt  (annoyedDivide x y)
```

    Message: "Cannot compute sqrt of a negative number"



<div class="dni-plaintext"><pre>-1</pre></div><style>
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
let x = -5
let y = 3 

annoyedDivide x y
|> annoyedSqrt
```

    Message: "Cannot compute sqrt of a negative number"



<div class="dni-plaintext"><pre>-1</pre></div><style>
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


### El tipo `Result`

Para solucionar los inconvenientes que presenta esta aproximación al manejo de errores, F# provee un tipo de dato a tal efecto. Observemos que tanto `divide` como `squareRoot` tienen dos salidas posibles, dependiendo de si el cálculo se puede efectuar o si hay algún error. En ningún caso uno puede estar en ambas situaciones al mismo tiempo, es decir, son situaciones _disjuntas_. Para eso están las uniones discriminadas!

El lenguaje define un tipo de unión particular, denominado `Result`:

```fsharp
type Result<'T,'TError> =
    | Ok of 'T
    | Error of 'TError
```

`Result es un tipo genérico que puede encontrarse en alguno de los dos estados posibles
- `Ok 'T` cuando la operación se pudo realizar
- `Error 'TError` cuando existe un error.

```fsharp
let divideR x y =
    match y with 
    | 0.0 -> Error "Trying to divide by zero!!" 
    | 1.0 -> Error "Can't believe you are trying to divide by one"
    | _ -> Ok (x/y) 

printfn "%A" (divideR 4 2)
printfn "%A" (divideR 4 1)
printfn "%A" (divideR 4 0)
```

    Ok 2.0
    Error "Can't believe you are trying to divide by one"
    Error "Trying to divide by zero!!"


```fsharp
let squareRootR (x: float) = 
    if x < 0 then 
        "Cannot compute sqrt of a negative number" |> Error 
    else 
        sqrt x |> Ok       
```

```fsharp
printfn "%A" (squareRootR 4.0)
printfn "%A" (squareRootR -4.0)
```

    Ok 2.0
    Error "Cannot compute sqrt of a negative number"


#### Railway programming

Evidentemente, el uso del tipo `Result` aclara significativamente el código. Pero ahora sucede que:

```fsharp
divideR:
   x: float ->
   y: float
   -> Result<float,string>
```

mientras que 

```fsharp 
squareRootR:
   x: float
   -> Result<float,string>
```

Entonces no me es posible componer ambas funciones, dado que el output de `divideR` es de tipo `Result`, mientras que el input de `squareRootR` es un `float`.  




```fsharp
let x = 4
let y = 6 

divideR x y 
|> Result.bind squareRootR
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>0.816496580927726</pre></div></td></tr><tr><td>ErrorValue</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details><style>
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
let x = 4
let y = 0 

divideR x y 
|> Result.bind squareRootR
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>0</pre></div></td></tr><tr><td>ErrorValue</td><td>Trying to divide by zero!!</td></tr></tbody></table></div></details><style>
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


El método `bind` de `Result` se ocupa de emparejar los inputs y outputs para poder conectar ambas funciones:

```fsharp 
bind:
   binder: ('T -> Result<'U,'TError>) ->
   result: Result<'T,'TError>
        -> Result<'U,'TError>
bind f inp evaluates to match inp with Error e -> Error e | Ok x -> f x
```

Supongamos ahora que además queremos sumarle un valor al resultado de $\sqrt{x/y}$. Afortunadamente la suma es una operación menos restrictiva que las anteriores, así que no es necesario tanta ceremonia:

```fsharp
let add10 y =
    y + 10.0 
```

Pero ahora tenemos otro problema, porque nuestra operación completa nos da como output `Result<float,string>`, que no es el input de `add10`....

Para eso existe el método `map` de `Result`: 


```fsharp
let x = 8
let y = 2 

divideR x y 
|> Result.bind squareRootR
|> Result.map add10 
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>12</pre></div></td></tr><tr><td>ErrorValue</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details><style>
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
let x = 8
let y = -2 

divideR x y 
|> Result.bind squareRootR
|> Result.map add10 
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>0</pre></div></td></tr><tr><td>ErrorValue</td><td>Cannot compute sqrt of a negative number</td></tr></tbody></table></div></details><style>
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
let x = 8
let y = 0 

divideR x y 
|> Result.bind squareRootR
|> Result.map add10 
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>0</pre></div></td></tr><tr><td>ErrorValue</td><td>Trying to divide by zero!!</td></tr></tbody></table></div></details><style>
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
let x = 8
let y = 0 

let res = 
    divideR x y 
    |> Result.bind squareRootR
    |> Result.map add10 

res
|> Result.mapError (fun e -> $"Hubo un error: {e}")    
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Core.FSharpResult`2[System.Double,System.String]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>ResultValue</td><td><div class="dni-plaintext"><pre>0</pre></div></td></tr><tr><td>ErrorValue</td><td>Hubo un error: Trying to divide by zero!!</td></tr></tbody></table></div></details><style>
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

