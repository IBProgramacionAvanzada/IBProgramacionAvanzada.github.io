---
layout: post
title: Una introducción a la criptografía
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

# Criptografía con clave pública-privada

El algoritmo para encriptar una serie de datos a través de una clave pública-privada consiste en encontrar dos números primos al azar (usualmente grandes) $p$ y $q$ de modo tal que $n = p \times q$.

Luego se busca $d$ como un número entero coprimo de $(p-1)(q-1)$, esto es, el máximo común divisor entre $d$ y $(p-1)(q-1)$ es 1. 

Finalmente, se encuentra $e$ que es el _inverso multiplicativo_ de $d$ módulo $(p-1)(q-1)$, esto es:

$e \times d = 1 \mod ((p-1)(q-1))$



```fsharp
let p = 13
let q = 17
let n = p*q 
let d = 181 
let e = 157 
let b = (p-1) * (q-1)

n
```


<div class="dni-plaintext"><pre>221</pre></div><style>
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
printfn $"{e} * {d} mod ({p-1} * {q-1}) = {e*d} mod({(p-1) * (q-1)}) = {e*d % b}"
```

    157 * 181 mod (12 * 16) = 28417 mod(192) = 1


```fsharp
let publicKey = (n,d)
let privateKey = (n,e)
```

Encriptar el mensaje M 

$C = M^e (\mod n)$

Desencriptar C

$M = C^d (\mod n)$ 

Notar que esto será válido siempre que $M \le n$.

```fsharp
let pow (b: bigint) (e:int) =
    let rec loop b i = 
        if i = 0 then 
            b
        else 
            b * (loop b (i-1))

    loop b (e-1)         

```

```fsharp
pow (bigint 2) 2
```


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


```fsharp
let m = bigint 143
let C = (pow m e) % (bigint n) 

let M = (pow C d) % (bigint n) 

printfn $"Encriptar {m}: {C} "
printfn $"Desencriptar {C}: {M} "

```

    Encriptar 143: 91 
    Desencriptar 91: 143 


```fsharp
pow m e % (bigint n) 
```


<div class="dni-plaintext"><pre>91</pre></div><style>
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


La seguridad del algoritmo se debe a que es extremadamente difícil desde el punto de vista matemático encontrar los factores primos de un número grande, esto es, encontrar p y q a partir del valor de n. La única forma es prueba y error. Sin embargo, para una clave de 128 bits, por ejemplo, el número entero máximo correspondiente es 2^128-1

```fsharp
let maxInt128bits = pow (bigint 2) 128 - (bigint 1)

printfn $"Max int representable en 128 bits: {maxInt128bits}"
```

    Max int representable en 128 bits: 340282366920938463463374607431768211455


Por otro lado, el número de primos hasta un valor $n$ es $\frac{n}{\log n}$. Para 128 bits tendríamos:

```fsharp
(maxInt128bits + bigint 1)/(bigint 128)
```


<div class="dni-plaintext"><pre>2658455991569831745807614120560689152</pre></div><style>
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


Que es lo mismo que 

$\frac{2^{128}}{128} = \frac{2^{128}}{2^{7}} = 2^{121} = 10^{(121 \log_{10}2)} \approx 10^{36} $

```fsharp
Math.Log10(2)*121.0
```


<div class="dni-plaintext"><pre>36.424629475341725</pre></div><style>
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


## Implementaciones

RSA (Rivest, Shamir, and Adleman) es el algoritmo usual para generar claves público-privadas. Se utilizan longitudes de bits de 1024, 2048 y 4096. Este algoritmo es _asimétrico_, en el sentido en el que se usan distintas claves para encriptar y desencriptar.

AES (_Advanced Encription Standard_) es un algoritmo _simétrico_, por lo tanto, posee una única clave para encriptar y desencriptar. 

La ventaja de AES es que es más rápido en el proceso de encriptar/desencriptar, dado que utiliza menos bits. Sin embargo, posee la desventaja de que tanto el emisor como el receptor del mensaje deben tener la misma clave, lo cual lleva al problema de compartirla en forma segura. La forma usual de resolver esta dificultad es utilizar una clave público privada tipo RSA para encriptar/desencriptar la clave AES.
