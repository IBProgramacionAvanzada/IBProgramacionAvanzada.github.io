---
layout: post
title: Secuencias  
tagline: otra colección más...
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Las secuencias (`seq`) son otro tipo de colecciones que ofrece F#, y se utilizan habitualmente cuando uno tiene una gran cantidad de datos. En efecto, a diferencia de las listas, los elementos de una secuencia se computan a medida que son requeridos. Por otra parte, las secuencias se asocian al tipo ` IEnumerable<T>` del ecosistema .NET, con lo cual son adecuadas para ocasiones en las que uno tiene que interactuar con bibliotecas o interfaces escritas en otros lenguajes, como C# o VB.

Para construir una secuencia, usamos (ejem...) un constructor `for... in ...do`, como se ve en los siguientes ejemplos:

```fsharp
let l = seq { for x in 1..5 -> x }
l
```


<div class="dni-plaintext"><pre>[ 1, 2, 3, 4, 5 ]</pre></div><style>
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
let sq =  seq {for x in 1..5 -> x*x }
sq
```


<div class="dni-plaintext"><pre>[ 1, 4, 9, 16, 25 ]</pre></div><style>
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


El uso de la flecha `->` es un edulcorante sintáctico que se usa cuando la expresión que evalua cada elemento de la secuencia es simple. Sino, se utiliza el constructor `do...yield`

```fsharp
let even = seq {for x in 0..3..30 do
                if (x % 2 = 0) then 
                    yield x}
even                   
```


<div class="dni-plaintext"><pre>[ 0, 6, 12, 18, 24, 30 ]</pre></div><style>
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


Una diferencia importante respecto de las listas es que no es posible acceder a los elementos de una secuencia usando el índice en forma habitual, como por ejemplo `even[3]`

```fsharp
even[3]
```


    input.fsx (1,1)-(1,8) typecheck error The type 'IEnumerable<_>' does not define the field, constructor or member 'Item'.


Sino que se debe utilizar el método `Seq.item` o su equivalente `Seq.nth`:

```fsharp
Seq.item 3 even
```


<div class="dni-plaintext"><pre>18</pre></div><style>
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
Seq.nth 0 even
```


<div class="dni-plaintext"><pre>0</pre></div><style>
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
Seq.nth 14 even
```


    System.ArgumentException: The input sequence has an insufficient number of elements.
    seq was short by 9 elements (Parameter 'index')
       at Microsoft.FSharp.Collections.Internal.IEnumerator.nth[T](Int32 index, IEnumerator`1 e) in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 43
       at Microsoft.FSharp.Collections.SeqModule.Item[T](Int32 index, IEnumerable`1 source) in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 613
       at <StartupCode$FSI_0059>.$FSI_0059.main@()
       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)
       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


Se pueden construir secuencias complejas con cualquiera de los tipos de datos de F#:

```fsharp
type Person = { Name: string; Age: int }
```

```fsharp
let people = seq {
    { Name = "Alice"; Age = 25 }; { Name = "Bob"; Age = 30 }; { Name = "Charlie"; Age = 35 }}
let names = seq {
                    for person in people -> person.Name
                    }
printfn "%A" names 
```

    seq ["Alice"; "Bob"; "Charlie"]


Y usar el constructor `for` en un estilo imperativo para, por ejemplo, filtrar una secuencia: 

```fsharp
seq {for person in people do 
        if (person.Age < 33) then yield person.Name}
```


<div class="dni-plaintext"><pre>[ Alice, Bob ]</pre></div><style>
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


El constructor `yield` agrega un elemento a la secuencia, pero cuando queremos agregar varios elementos, utilizamos `yield!`. En el ejemplo siguiente, concatenamos dos secuencias en una tercera

```fsharp
let joined = 
    seq {
        yield! l
        yield! sq
    }

printfn $"l: %A{l}"
printfn $"sq: %A{sq}"
printfn $"joined: %A{joined}"
```

    l: seq [1; 2; 3; 4; ...]
    sq: seq [1; 4; 9; 16; ...]
    joined: seq [1; 2; 3; 4; ...]


> Nótese el uso del descriptor de formato %A en la cadena de caracteres interpolada. 

```fsharp
let anotherJoined = 
    seq {
        yield! l
        for x in 6..10 do x 
    }

anotherJoined
```


<div class="dni-plaintext"><pre>[ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]</pre></div><style>
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


### Un poco de manejo de `strings`

Empecemos a utilizar toda la potencia de las bibliotecas de .NET:

- `System.IO.Path` que maneja todo aquello que tiene que ver con _paths_, es decir, nombres completos de archivos. 
- `System.String` que contiene métodos para trabajar con `strings`

```fsharp
let filepaths = seq { "file1.txt"; "file2.exe"; "file3.txt"; "file4.doc"; "file5.pdf" }

let extensions =
    seq {
        for filepath in filepaths do
            let extension = Path.GetExtension(filepath)
            if not (String.IsNullOrEmpty(extension)) then
                yield extension.Substring(1) // Grabs substring beginning at char index 1 
    }
    |> Seq.distinct

printfn "%A" extensions 
```

    seq ["txt"; "exe"; "doc"; "pdf"]


El método `.Substring(n)` de un objeto tipo `string` permite obtener la cadena de caracteres incluída en dicho objeto que comienza en caracter n-ésimo

```fsharp
let titulo = "La bella y graciosa moza marchóse a lavar la ropa"

printfn $"{titulo}"
printfn $"{titulo.Substring(3)}"
```

    La bella y graciosa moza marchóse a lavar la ropa
    bella y graciosa moza marchóse a lavar la ropa


## Lazy evaluation

Como se mencionó arriba, los elementos de las secuencias se van construyendo a medida que se necesitan, en un proceso característico de muchos lenguajes funcionales que se denomina _lazy evaluation_ (evaluación ... perezosa?): 

```fsharp
let intSeq =
    seq { for n in 1 .. 10 do
            printfn "intSeq: %i" n
            yield n }
            
Seq.nth 3 intSeq
            
```

    intSeq: 1
    intSeq: 2
    intSeq: 3
    intSeq: 4



<div class="dni-plaintext"><pre>4</pre></div><style>
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


Esta característica permite que existan métodos como el siguiente, que "evalua" una secuencia infinita:

```fsharp
let allNumbers = Seq.initInfinite (fun i -> i * 2)
```

Sobre la cual puedo tomar una determinada cantidad de elementos con el método `Seq.take`

```fsharp
printfn "%A" (allNumbers |> Seq.take 20)
printfn "%A" (allNumbers |> Seq.take 20 |> Seq.length)
printfn "%A" (allNumbers |> Seq.take 30)
printfn "%A" (allNumbers |> Seq.take 30 |> Seq.length)
```

    seq [0; 2; 4; 6; ...]
    20
    seq [0; 2; 4; 6; ...]
    30


Pero hay que tener precaución al hacer este tipo de cosas:

```fsharp
printfn "%A" (allNumbers |> Seq.length) // Ojo!!!!
```


    System.InvalidOperationException: Enumeration based on System.Int32 exceeded System.Int32.MaxValue.


       at Microsoft.FSharp.Collections.Internal.IEnumerator.upto@323.System.Collections.IEnumerator.MoveNext() in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 336


       at Microsoft.FSharp.Collections.SeqModule.Length[T](IEnumerable`1 source) in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 870


       at <StartupCode$FSI_0070>.$FSI_0070.main@()


       at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)


       at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)


El tipo `seq` es compatible con el tipo `IEnumerable<'T>` del ecosistema .NET. Por lo tanto, cualquier uso de una biblioteca de .NET que nos devuelva un `IEnumerable<'T>`, se usa directamente como una secuencia:

```fsharp

let cwd = System.IO.Directory.GetCurrentDirectory()
printfn "%s" cwd

let files =
        System.IO.Directory.EnumerateFiles(cwd)
        |> Seq.map(fun f -> (System.IO.Path.GetFileName(f),File.GetCreationTime(f)))
        |> Seq.iter(fun (name,date) -> printfn $"{name} created at {date}")

files         
```

    /Users/flavioc/Library/Mobile Documents/com~apple~CloudDocs/Documents/Blog/drafts
    PythonExamples.ipynb created at 09/04/2023 19:36:03
    TextParser.ipynb created at 01/05/2023 12:27:00
    TheBeatles.ipynb created at 23/04/2023 19:45:04
    Semaphore.ipynb created at 17/04/2023 11:13:19
    IndexOfAny.ipynb created at 20/04/2023 08:44:59
    IO.ipynb created at 02/05/2023 10:41:05
    VendingMachine.ReadFood.ipynb created at 01/05/2023 14:35:24
    Qatar2022.ipynb created at 23/04/2023 18:12:15
    MyList.ipynb created at 19/04/2023 08:34:17
    Peano.ipynb created at 20/04/2023 14:27:04
    VendingMachine01.ipynb created at 09/04/2023 19:36:03
    SolTypeExercises.ipynb created at 13/04/2023 13:51:14
    Imaging.ipynb created at 02/05/2023 16:40:50
    YetSomeMoreOnCollections.ipynb created at 01/05/2023 17:02:39
    Emails.ipynb created at 27/04/2023 09:55:39
    Poker.ipynb created at 09/04/2023 19:36:03
    TheFizzBuzz.ipynb created at 13/04/2023 13:51:14
    CardinalPoints.ipynb created at 13/04/2023 13:51:14
    Index.ipynb created at 02/05/2023 17:15:47


Aquí utilizamos los módulos de .NET

- `System.IO.Directory` que maneja todo aquello que tiene que ver con directorios, es decir, nombres completos de archivos. 
- `System.IO.File` que contiene métodos para trabajar con archivos.

y los métodos `Seq.map` y `Seq.iter` similares a los que se utilizan con listas.

### Lists comprehension

En forma similar a la de los párrafos precedentes, donde usamos la construcción `[for x in collection do ... yield expr]` para recrear secuencias; y al igual que otros lenguajes como [Python](https://www.w3schools.com/python/python_lists_comprehension.asp) o [Haskell](https://wiki.haskell.org/List_comprehension), se lo puede utilizar en F# para realizar _compresiones de listas_, esto es, una forma imperativa de construir una lista. 

```fsharp
let l = [for x in 1..5 -> x ]
l.Head
```


<div class="dni-plaintext"><pre>1</pre></div><style>
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
let sq =  [for x in 1..5 -> x*x ]
sq
```


<div class="dni-plaintext"><pre>[ 1, 4, 9, 16, 25 ]</pre></div><style>
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
let even = [for x in 0..3..30 do
                if (x % 2 = 0) then 
                    yield x]
even                     
```


<div class="dni-plaintext"><pre>[ 0, 6, 12, 18, 24, 30 ]</pre></div><style>
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
type Person = { Name: string; Age: int }
```

```fsharp
let people = [{ Name = "Alice"; Age = 25 }; { Name = "Bob"; Age = 30 }; { Name = "Charlie"; Age = 35 }]
let names = [for person in people -> person.Name]
printfn "%A" names 
```

    ["Alice"; "Bob"; "Charlie"]


```fsharp
[for person in people do 
    if (person.Age < 33) then yield person.Name]
```


<div class="dni-plaintext"><pre>[ Alice, Bob ]</pre></div><style>
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


Nótese que las listas no se evalúan en forma perezosa:

```fsharp
let intLst =
    [ for a in 1 .. 10 do
        printfn "intLst: %i" a
        yield a ]
```

    intLst: 1
    intLst: 2
    intLst: 3
    intLst: 4
    intLst: 5
    intLst: 6
    intLst: 7
    intLst: 8
    intLst: 9
    intLst: 10

