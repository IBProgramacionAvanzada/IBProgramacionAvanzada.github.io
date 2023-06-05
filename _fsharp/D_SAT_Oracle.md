---
layout: post
title: Grover en acción
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

## El problema a resolver

Vamos a resolver el problema 3SAT dado por 

$\lnot \left(\lnot x \land \lnot y \right) \land \lnot y$

usando el algoritmo de Glover. Para ello necesitamos crear el Oráculo que define la acción de
las cláusulas lógicas anteriores sobre los qubits. Vamos a representar
$x$ e $y$ por los cubits `q[0]` y `q[1]` respectivamente.

### El oráculo

Empecemos por ver qué hace el siguiente circuito:

<img src="/img/SAT Clause 1.svg" alt="" width="600"/>

cuya implementación es [SAT Oracle Clause 1](https://quantum-computing.ibm.com/composer/files/new?initial=N4IgdghgtgpiBcIDKBBAKgAgPICcIGMAbGDAYUIgFcBnEgRhABoQATGa-HASwAcAXLgHswCEExABHCNSiisABQCiAOQCKKJAFkMAJgB0ABgDcAHTBcwRSmwwnJMQlwBGdPRfx3TYMxJwwA5hgSANoALAC6XpwBGPhhkWZmAB5BwQYJYCkhdBn4%2BFlp4YypOcUhOhkF6V4FOV5OEDjcMDipFV6w0pR%2BbeEYALQAfLHB7YlgAPQTGAAC%2BIKElFBg1BjBdIwbOowAzLv7ESAAvkA). 

Efectivamente, este circuito hace la operación AND: $\lnot x \land \lnot y$ y
si el cubit 2 está en el estado 0, se  guarda el resultado de dicha operación en el mismo. Finalmente los NOT sobre los cubit 0 y 1 los devuelven
a su estado original. 

De la misma forma se puede seguir operando sobre los cubits, 
negando ahora el resultado de la operación anterior para obtener
$\lnot \left(\lnot x \land \lnot y \right)$ en el cubit 3

En el siguiente paso, se realiza el último AND entre el resultado anterior
y $\lnot y$. 

Finalmente, es necesario deshacer las operaciones sobre los cubits auxiliares `q[2]` y `q[3]`. En efecto, como el estado cuántico está
compuesto de todas las contribuciones de los cubits, es importante 
'borrar' el estado de esos cubits auxiliares para que solamente se tenga
como inputs los valores necesarios de los cubits 0, 1 y 4 en el siguiente 
paso.

<img src="/img/Oracle.png" alt="" width="600"/>


### El circuito difusor

Para el difusor se propone el siguiente circuito:

<img src="/img/Diffuser.svg" alt="" width="600"/>

que se encuentra [acá](https://quantum-computing.ibm.com/composer/files/new?initial=N4IgdghgtgpiBcIAiBLAZmgrgZxgJxABoQATGbAYzxQAcAXFAezARCJAEcJspWB5AAoBRAHIBFAIIBlALIACAEwA6AAwBuADpgUYCgBtMZORs4w9KAEYBGJToonNYLRzwwA5nI4BtAMwBdRyp3OQovKwCtLVdcOk8vFQiwaJhY73DHAAs4hMy49K0AD2zEorTEigpS%2BL9CPJq4hRLixwsIPGp8Opa2jrxmwq6tLO8cocGwWG5MV2K5AFoAPhDqxy0AejW5AAEKRgMoMGw5eMIVQitzwgUrwh9CABYHwgBWF8IANg-CAHY-EABfIA)

Finalmente, el circuito completo es [SAT Solver](https://quantum-computing.ibm.com/composer/files/new?initial=N4IgdghgtgpiBcIDKBBAKgAgPICcIGMAbOAGhABMYBnfHASwAcAXOgezARBDIEcIqonLAAUAogDkAiiiQBZDACYAdAAYA3AB0wdMEQCulDBpA8YhOgCMAjEp35jmsAHMITGBiIQ9VGAowQSDAsMYC0MDAAPf0dw-CiAoJjI6K0AXy0XNwxWPCJ3BItA-EDyQPdQsHD4pKiLJPw4-0DCjxqUyuS6sKCIHHoYHFbui17%2BwfIkkb66AfbwqbHE4dGZwYh6wi8fPxaJ5enZro6F1aHjldm9jqj8esbijFKMGEmLtdeDwaOqs-m3x4%2Bi1u%2B0W626nm8viCJTaYPOnyW8KBgNOVx%2B3w8jQKRVhbSO6WcrncEJ8Via0I8IW61WpiNiWOaONpcPRjgJmXcOGoekITHJLXwVOuc0x8UZv2ScPZRMedAAZnLIWtxYKKuEABYizUYmnCjENMUU4HClmdJKa03atlaLQ8LlODA8ADaAFYALqOWgwB34J0KD02sCa50qANBx1Oqxhk6zENhkkwMlxwLOqMpv3R-6pzMI53%2Bz2bSF%2BbPpgDMOcWeYrp2d5ccXKoPL5VbLbvTABZq7GM44Y4Na-HC9sI2mI3WtH2I-mJ1nI4OtomI6H06Oq73Z1H17mezPt6HHOR5YqfP2ncuR22I53HLB%2BHouUu3RgALQAPg8Z7Dt8bD%2BzL-fvqbiAqRAA)

<img src="/img/SAT Oracle.svg" alt="" width="600"/>

El resultado que se obtiene al correr 1024 instancias es:

<img src="/img/SAT%20Histogram.png" alt="" width="600"/>

Es decir, $x=1$ y $y=0$ con alta probabilidad, que es el resultado esperado
clásicamente.




