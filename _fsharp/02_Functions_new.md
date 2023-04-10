---
layout: post
title: ¿Qué es una función?
date: 2023-01-21
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Ya que estamos a punto de aprender programación funcional, necesitamos estar de acuerdo en qué es una _función_ en este contexto. Comencemos con un conjunto de entidades, por ejemplo, los autos estacionados en un cuadra dada de una calle. Se puede identificar cada auto por su placa, y luego construir una tabla con dos columnas: la primera con la placa y la segunda con el color correspondiente a cada auto:

| Lic. Placa | Color |
| :----------: |:-------------:|
| ABC 124 | negro |
| DEF 350 | rojo |
| QRZ 441 | negro |
| JPG 255 | blanco |

Esa es una función que podemos llamar `carColor`, que asocia cada auto del cuadra con su color. La tabla es una representación de esa función. Una función de un conjunto de entidades A a un conjunto de entidades B es entonces una relación que asocia elementos entre los conjuntos A y B, con la propiedad de que todos y cada uno de los elementos de A tienen un único elemento correspondiente de B.

Hay dos cosas a tener en cuenta. Primero, se define una función _de_ un conjunto _a_ otro conjunto, en nuestro ejemplo, del conjunto de matrículas al conjunto de colores. Para obtener el color de un automóvil en particular, vaya a la tabla de representación de la función, busque la placa en la columna de placas y obtenga el color de la segunda columna. Con esto en mente, se puede identificar el conjunto _desde_ como la _entrada_ de la función, mientras que el conjunto _a_ es la _salida_.

En segundo lugar, todos los elementos de la entrada deben relacionarse con algún elemento del conjunto de salida. En nuestro ejemplo, cada automóvil en el cuadra tiene un color asignado en la tabla. Esto significa que no puede haber celdas vacías en la segunda columna de nuestra tabla.

> ❓ ¿Se te ocurren más ejemplos de funciones en el mundo real?

En matemáticas, las funciones son ubicuas. Supongamos que $x$ es un número real y definamos la función

$$
f(x) = x + 1,
$$

es decir, la función que devuelve el valor $x$ más uno. El conjunto de entrada (que se llama el _dominio_ de la función en matemáticas) es el conjunto de todos los valores reales, $\mathbb{R}$, mientras que el conjunto de salida (el _codominio_ en matemáticas) también es $\mathbb{R}$ , porque sumar uno a cualquier número real también es otro número real. Usando la notación _flecha_, la definición completa de la función sería

$$
f: \mathbb{R} \rightarrow \mathbb{R} \; ; \; x \mapsto a x + 1,
$$

que se puede leer de la siguiente manera: $f$ es una función de $\mathbb{R}$ a $\mathbb{R}$ tal que $f$ de $x$ es $x + 1$".

> 🔔 Un ligero desvío alrededor del codominio. El codominio es el conjunto de entidades en las que la función posiblemente puede asignar valores de entrada. Por ejemplo, en el caso del color de los coches, el codominio es simplemente el conjunto de todos los colores posibles. En muchos casos, esa información es demasiado general y es conveniente definir el _rango_ de la función, que es el conjunto de valores reales de las salidas en las que la función asigna las entradas. El rango en el ejemplo de los autos es el conjunto {negro, rojo, blanco}.

### Múltiples entradas y salidas

Tomemos el ejemplo de una máquina expendedora. En una máquina expendedora, los productos se organizan en estantes, donde cada estante se nombra con una letra. En cada estante, los productos están alineados e identificados por un número. Luego, en A1 tienes una bolsa de papas fritas, en A2 un chocolate, en B1 un refresco, y así sucesivamente. La máquina también tiene un teclado con letras y números para que elijas el producto. Para comprar algo, debe darle dinero a la máquina (monedas, billetes, tarjeta de crédito, etc.), seleccionar el producto haciendo clic en la letra y el número en el teclado. La máquina devuelve el producto y algún cashback, si lo hubiera.
Las entradas de nuestra función `vendingMachine` son el dinero, la letra y el número que seleccionó, y las salidas son el producto y el cambio (si lo hay).

Un ejemplo matemático podría ser una función de traslación, donde dado un punto con coordenadas \(x\) y $y$ en el plano, devuelve un punto con coordenadas $x+1$ y $y+1$:

$$
g: \mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R} \; ; \; (x,y) \mapsto (x + 1, y+1)
$$

o más sucintamente

$$
g(x,y) = (x+1,y+1)
$$

### Aplicación parcial
Cuando alimentamos una función de varios elementos de entrada, podemos obtener las salidas adecuadas. Pero tener muchas entradas abre una nueva posibilidad: ¿qué sucede cuando uno decide no completar todas las entradas? Averigüémoslo. Supongamos que ingresamos un billete en la máquina expendedora. Está claro que no obtendremos ningún producto, porque la máquina todavía necesita dos entradas más: la letra de la estantería y el número de producto que queremos. _Después_ de que completemos estas dos entradas, obtendremos nuestro producto (y el reembolso, si corresponde). Por lo tanto, ingresar dinero solo en la máquina expendedora conduce a un estado en el que se necesitan dos entradas y se devolverán dos salidas. Pero, esta es _otra función_!!!. Llamémosle `vendingMachineAfterInsertBill` que recibe la letra del estante y el número de producto que queremos y devuelve el producto (y el vuelto, si lo hay).

Volviendo al ejemplo matemático, alimentemos la función solo con el valor $x = 3$,

$$
g(3,y) = (4,y+1)
$$

Nuevamente, el resultado de alimentar la función con un valor es otra función:

$$
h: \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R} \; ; \; y \mapsto (4, y+1)
$$

o

$$
h(y) = (4,y+1)
$$

Esta propiedad de las funciones se llama aplicación parcial. Cada vez que uno no completa todas las entradas de una función, obtiene otra función.

### Composición

Finalmente, vemos aquí cómo trabajar con varias funciones a la vez. Supongamos que tenemos una función `getFirstName` que, dado el nombre completo de una persona, devuelve el nombre (no importa en este punto los detalles de la implementación, ni siquiera el idioma). Por ejemplo, cuando se aplica a 'David Gilmour', devuelve 'David', o cuando se aplica a 'Annie Lennox', devuelve, por supuesto, 'Annie'.
También tenemos una función `getInitial` que para un nombre dado, devuelve la inicial. En los casos anteriores, 'D' por 'David' y 'A' por 'Annie'.

Ahora queremos construir una función que nos dé la inicial del primer nombre, dado el nombre completo. Por 'Paul McCartney'. Alimentamos 'Paul McCartney' como entrada a la función 'getFirstName', que nos da la salida 'Paul'. Ahora, 'Paul' es la entrada de la función 'getInitial' y devuelve 'P' como salida final.
Esta tubería donde la salida de una función es la entrada de otra se llama _composición_. Tenga en cuenta que es absolutamente necesario que la salida de la primera función llamada (`getFirstName`) y la entrada de la segunda (`getInitial`) sean del mismo tipo de entidad, en nuestro caso, ambos son nombres.
Puede [ver esta página para una explicación gráfica del asunto](https://mathinsight.org/function_machine_composition).

Veamos ahora un ejemplo matemático. Definimos antes la función $f(x)$ que suma uno a $x$, por ejemplo

$$
f(0) = 0 + 1 = 1
$$

¿Qué pasa si aplicamos la función $f$ nuevamente? significa computar

$$
f(f(0)) = f(0) + 1 = 0 + 1 + 1 = 2
$$

En general,

$$
f(f(x)) = f(x) + 1 = x + 1 + 1 = x + 2
$$

Si observamos cuidadosamente la última expresión, componer la función es como pasar la función como la entrada misma ($f(f(x))$). Esto significa que si vamos a tener un lenguaje de programación que implemente la composición de funciones, de alguna manera las funciones deberían poder pasarse como entrada a otras funciones.

> ❗️ El hecho de que usemos la misma función para componer consigo misma no es relevante para esta discusión, uno puede componer tantas funciones diferentes como quiera, siempre que las entradas y salidas sean compatibles en cada paso de composición.

> 🔔 Sin embargo, componer esa función particular consigo misma es interesante. Imágenes que tienen solo el cero y esta función. Puedes crear todos los números naturales {1, 2, ...} simplemente componiendo esta función consigo misma una y otra vez. Por ejemplo, $4 = f(f(f(f(0))))$, y así sucesivamente. Por lo tanto, dado el número 0, $f(x) = x + 1$ y la composición de la función, se pueden obtener todos los números naturales. Parece que algo está pasando aquí. Más sobre esto, con suerte, en un episodio futuro.


## Funciones en F\#

El lenguaje F\# implementa funciones de tal manera que satisfacen las propiedades mencionadas anteriormente. Para definir una función, el lenguaje también usa la palabra clave `let`:


```fsharp
let next x =
    x + 1 
```

Definimos la función llamada `next` que recibe un argumento `x`. Observe que no hay otros símbolos o paréntesis en la definición de la función. El cuerpo de la función debe estar indentado y no hay ninguna palabra clave `return` al final. La función simplemente devuelve la última expresión encontrada en su cuerpo. Limpio, ¿no?
Usar la función también es fácil:

```fsharp
let one = next 0 
let two = next (next 0)

printfn "one: %A" one 
printfn "two: %A" two 
```

Tenga en cuenta que no es necesario usar paréntesis alrededor del argumento cuando se usa una función. Sin embargo, debe usarlos cuando pase una expresión más compleja como argumento a la función, como en el caso de `two`.

Hay otra forma de escribir el cálculo de `two`, utilizando el operador _pipe_ `|>`:

```fsharp
let anotherTwo =
    0 
    |> next
    |> next 
    
printfn "anotherTwo: %A" anotherTwo
```

Este operador se encarga de la plomería al llamar funciones una tras otra. En el ejemplo anterior, el primer `|>` recibe `0` como entrada, lo pasa a la siguiente función, el segundo `|>` recibe la salida de la primera función `next` y la alimenta como entrada a la segunda `next`.

Otro ejemplo. Supongamos que tenemos las funciones `getInitial` y `getFirstName` definidas como:

```fsharp
let getInitial name = 
    .... //Implementation not important right now
```

y

```fsharp
let getFirstName fullName = 
    .... //Implementation not important right now
```

y definimos el valor

```fsharp
let paul = "Paul McCartney"
```

Entonces,

```fsharp
paul
|> getFirstName
|> getInitial 
```
Aquí, el valor de la cadena `paul` se introduce en la función `getFirstName` como la entrada de la primera canalización `|>`, y devuelve 'Paul' como salida. Luego, la cadena 'Paul' se pasa como entrada de la función 'getInitial' que nos da la 'P'.

La composición es tan importante en los lenguajes funcionales que tiene su propio símbolo, que en F\# es `>>` :

```fsharp
let add2 = next >> next 
let two' = add2 0 
printfn "%A" two'
```

¡Sí, puede usar el símbolo `'` en cualquier identificador! (siempre que no sea el primer carácter). Tenga en cuenta también que definimos una _función_ `add2` usando el operador de composición (no se necesita ningún argumento). Esto es equivalente a:

```fsharp
let add2' x = 
    x
    |> next
    |> next 
```

Recuerda que no hay retorno al final de la función, solo la última expresión de la función es el valor de retorno.

Volviendo al ejemplo de los nombres, para aclarar el orden en que se componen las funciones.

```fsharp
let getInitialFromFirstName fullName =
    getInitial (getFirstName fullName)
        
```

```fsharp
let getInitialFromFirstName' fullName =
    fullName
        |> getFirstName 
        |> getInitial 
```
y

```fsharp
let getInitialFromFirstName'' =
        getFirstName >> getInitial 
```



son equivalentes.

> ❓ Piensa en rutinas, procedimientos o funciones que quizás tengas escritas en tu idioma de preferencia. ¿Se comportan como funciones F\#? ¿Cuáles son las principales diferencias que ves?

> 🏋🏽 Tenemos una función `mult2` que dado un número `x` duplica ese número. Sin codificar, ¿puedes determinar qué devuelven las siguientes funciones compuestas cuando se aplican a 3? :

```fsharp
let f = mult2 >> next 
let g = next >> mult2 
```

Codifique la función `mult2` y vea el resultado usted mismo.

Algunas observaciones finales por ahora sobre las funciones. Primero, tenga en cuenta que el lenguaje usa la misma palabra clave `let` para vincular valores y funciones simples a un nombre o identificador. Esto enfatiza el hecho de que en las funciones de F# son valores 'solo' y se pueden tratar de la misma manera que, por ejemplo, un enlace más simple de una expresión a un identificador.
En segundo lugar, las propiedades de las funciones que se discutieron anteriormente coinciden perfectamente con la inmutabilidad. De hecho, las funciones reciben entradas inmutables y devuelven un valor inmutable.

<!-- > Aquí está  [el Jupyter notebook de esta guía](https://github.com/fcolavecchia/fp-course/blob/main/es/Functions_es.ipynb).
 -->
