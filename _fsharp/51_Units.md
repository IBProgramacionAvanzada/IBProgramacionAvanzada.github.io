---
layout: post
title: Unidades de medida 
tagline: Cuando las cosas no salen bien
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Al igual que otros lenguajes, los lenguajes de .NET aceptan decoradores o atributos sobre los tipos de datos, que se expresan usando `[<AttributeName>]`. Uno de los más convenientes el `[<Measure>]` que permite definir unidades de medida:

```fsharp
[<Measure>]
type J 

[<Measure>]
type s 

[<Measure>]
type m 

[<Measure>]
type nm 

let nmTom (a: float<nm>) = a*1e-9<m/nm>  

let c = 299792458.0<m/s>

let h = 6.6261E-34<J*s> 

printfn "%A" h
```

    6.6261e-34


Planck formula
$$
E = \frac {h c}{\lambda}
$$

```fsharp
let lambda = 100.0<nm>

printfn $"lambda[m]: {nmTom lambda}"

let energy (lambda: float<nm>) = 
    h*c/(nmTom lambda) 

printfn $"E[J]: {energy lambda}"

```

    lambda[m]: 1,0000000000000001E-07
    E[J]: 1,9864548059538E-18


```fsharp
[<Measure>]
type eV 

let joulesToeV (a:float<J>) = 6.242e+18<eV/J>*a
```

```fsharp
printfn $"E[eV]: {energy lambda |> joulesToeV}"

```

    E[eV]: 12,39945089876362


```fsharp
[<Measure>] type C // temperature, Celsius/Centigrade
[<Measure>] type F // temperature, Fahrenheit

let convertCtoF ( temp : float<C> ) = 9.0<F> / 5.0<C> * temp + 32.0<F>
let convertFtoC ( temp: float<F> ) = 5.0<C> / 9.0<F> * ( temp - 32.0<F>)

// Define conversion functions from dimensionless floating point values.
let degreesFahrenheit temp = temp * 1.0<F>
let degreesCelsius temp = temp * 1.0<C>

[0..10..100]
|> List.map float 
|> List.map degreesCelsius 
|> List.map (fun t -> (t,convertCtoF t))
|> List.iter (fun (tc,tf)-> printfn $"%3.0f{tc} C son %3.0f{tf} F")
```

      0 C son  32 F
     10 C son  50 F
     20 C son  68 F
     30 C son  86 F
     40 C son 104 F
     50 C son 122 F
     60 C son 140 F
     70 C son 158 F
     80 C son 176 F
     90 C son 194 F
    100 C son 212 F


```fsharp
[0..10..100]
|> List.map float 
|> List.map degreesFahrenheit 
|> List.map (fun t -> (t,convertFtoC t))
|> List.iter (fun (tf,tc)-> printfn $"%3.0f{tf} F son %3.0f{tc} C")
```

      0 F son -18 C
     10 F son -12 C
     20 F son  -7 C
     30 F son  -1 C
     40 F son   4 C
     50 F son  10 C
     60 F son  16 C
     70 F son  21 C
     80 F son  27 C
     90 F son  32 C
    100 F son  38 C


```fsharp
let t1 = 44<F>
let t2 = 32<C>

t1 + t2 
```


    input.fsx (4,6)-(4,8) typecheck error The unit of measure 'C' does not match the unit of measure 'F'


    input.fsx (4,4)-(4,5) typecheck error The unit of measure 'C' does not match the unit of measure 'F'


```fsharp
let t1 = 44<F>
let t2 = 32<F>

t1 + t2 
```


<div class="dni-plaintext"><pre>76</pre></div><style>
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


Uno puede querer usar los valores sin la unidad de medida, para lo cual es necesario _castearlos_  usando `float`.
