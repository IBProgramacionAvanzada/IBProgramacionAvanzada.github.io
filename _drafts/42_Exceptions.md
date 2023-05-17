---
layout: post
title: Excepciones 
tagline: Cuando las cosas no salen bien
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Todos los lenguajes de programación proveen alguna forma de trabajar con errores inesperados. Ya sea un archivo que no existe, una página web que no se puede alcanzar, una división por cero, todos estos son errores que _a priori_ podrían suceder y que el desarrollador debiera considerar al momento del diseño del código. No hay experiencia de usuario más exasperante que un programa cerrándose solo sin ninguna explicación o posibilidad de intervención del usuario.

Si bien más adelante veremos algunas formas más adecuadas de situaciones de errores, la forma tradicional es a través del manejo de *excepciones*. La excepción es la señal de una situación anómala que interrumpe el flujo normal del programa. En este caso, se procura capturar la excepción y proceder en consecuencia. 

```fsharp
let getHead (l: int list) = 
    l.Head 

getHead []
```


    System.InvalidOperationException: The input list was empty.


       at Microsoft.FSharp.Collections.FSharpList`1.get_Head() in D:\a\_work\1\s\src\FSharp.Core\prim-types.fs:line 4072


       at <StartupCode$FSI_0016>.$FSI_0016.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


En este caso el sistema emite la excepción `InvalidOperationException`, y nos dice la causa `The input list was empty`. 

Cómo capturamos la excepción? Con un bloque `try...with`:

```fsharp
let getHeadExn (l: int list) = 
    try 
        l.Head 
    with
    | :? System.InvalidOperationException  -> 
        printfn "Exception!" 
        -1 

getHeadExn []
```

    Exception!



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


Nuevamente F# utiliza _pattern matching_ para verificar el _tipo_ de excepción, a través del símbolo `:?`. El bloque `try...with` no deja de ser una expresión, por lo tanto ambas ramas (`try` y `with`) tienen que retornar el mismo tipo de dato. De allí el valor `-1` en la rama `with`, para indicar que no es posible obtener el valor `Head` de la lista.

Es posible obtener más datos de la excepción, de la siguiente forma:


```fsharp
let getHeadExnCustomMsg (l: int list) = 
    try 
        l.Head 
    with
    | :? System.InvalidOperationException as ex  -> 
        printfn "Exception!: %A" (ex.Message)
        -1 

getHeadExnCustomMsg []
```

    Exception!: "The input list was empty."



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


Se pueden crear excepciones en lugar de utilizar las del sistema:

```fsharp
exception ExceptionForList of string 

let getHeadExn2 (l: int list) = 
    try 
        match l with 
        | [] -> raise (ExceptionForList "List is empty")
        | _ -> l.Head 
    with
    | ExceptionForList s  -> 
        printfn "%s" s 
        -1 
        

getHeadExn2 []
```

    List is empty



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


En este caso es el programador que decide emitir una excepción personalizada, a través de la función `raise`. 

```fsharp
exception DivideByOne of string 

let divide x y =
    match y with 
    | 0 -> invalidOp "Trying to divide by zero!!" 
    | 1 -> raise (DivideByOne "Can't believe you are trying to divide by one")
    | _ -> x/y 

printfn "%A" (divide 4 2)
```

    2


```fsharp
printfn "%A" (divide 4 1)

```


    FSI_0020+DivideByOne: DivideByOne "Can't believe you are trying to divide by one"


       at FSI_0020.divide(Int32 x, Int32 y)


       at <StartupCode$FSI_0021>.$FSI_0021.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


```fsharp
printfn "%A" (divide 4 0)
```


    System.InvalidOperationException: Trying to divide by zero!!


       at FSI_0020.divide(Int32 x, Int32 y)


       at <StartupCode$FSI_0022>.$FSI_0022.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


```fsharp
let annoyedDivide x y =
    try 
        divide x y 
    with 
    | DivideByOne s -> 
        printfn "%A" (s)
        -1
    | :? System.InvalidOperationException as ex -> 
        printfn "Message: %A" (ex.Message)
        -1          

        
```

```fsharp
annoyedDivide 4 2 

```


<div class="dni-plaintext"><pre>2</pre></div><style>
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
annoyedDivide 4 1 
```

    "Can't believe you are trying to divide by one"



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
annoyedDivide 4 0
```

    Message: "Trying to divide by zero!!"



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


La función `annoyedDivide` captura las excepciones, y en esos casos devuelve `-1`.

> Obsérvese que es cuanto menos discutible que una función que divide retorne -1 como un valor en caso de error, puesto que -1 es efectivamente un valor válido al hacer una división. Si bien el programa continuará corriendo dado que se capturó la excepción, el problema ahora lo tiene la función que llama a `annoyedDivide`, ya que tiene que distinguir entre el caso de -1 como valor válido o -1 como signo de error. Veremos más adelante una forma robusta de proceder en este caso.

