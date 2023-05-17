---
layout: post
title: ¿Qué es F#?
tagline: Repasando algunas cuestiones sobre funciones
categories: 
- F# as your first functional programming language
tags:
- fsharp
---


# ¿Qué es F#?

[F# ](https://dotnet.microsoft.com/en-us/languages/fsharp) (también escrito como fsharp cuando el signo numeral no es bienvenido) es un lenguaje creado en [Microsoft](https://docs.microsoft.com/en-us/dotnet/fsharp/) por [Don Syme](@dsymetweets). Fue descrito como un lenguaje de programación _functional-first_, aunque en la última iteración, el lema fue cambiado a _a universal programming language for writing succinct, robust and performant code_.  Al igual que muchos otros lenguajes (como Java), se creó originalmente como un esfuerzo corporativo dentro de Microsoft, pero afortunadamente se convirtió en [open sourced] (https://github.com/dotnet/fsharp), con la gran ventaja de que existe un gran ecosistema de herramientas y bibliotecas construidas sobre .NET entre las que elegir.   Puedes leer la historia del lenguaje [aquí](https://fsharp.org/history/hopl-final/hopl-fsharp.pdf).

## Por qué F# 

Aquí hay algunas características que F# exhibe como _un lenguaje_:

### Es un lenguaje multi paradigma

Cuando se programa en F# se presentará _primero_ un enfoque funcional, que puede ser fácilmente extendido para incluir estilos orientado a objetos y/o imperativo. Se puede mezclar y combinar paradigmas según las necesidades específicas del código.  

### F# es un lenguaje fuertemente tipado

Esto significa que uno necesita saber de antemano qué tipo de valor está guardando en cada expresión (Nótese que _no_ estoy hablando de _variables_. Más sobre esto más adelante). Sin embargo, la experiencia habitual al programar en F\# con un IDE moderno es que no se programa en solitario, el compilador es un amigo que está continuamente inspeccionando lo que estás escribiendo, y decide si la definición de tipo apropiada es explícitamente necesaria o no. En la gran mayoría de las líneas de código no se necesita establecer el tipo de los datos y se infiere correctamente, pero es muy útil hacerlo para tus compañeros de equipo o para tu futuro yo.

### F# es un lenguaje compilado

Estarás tratando con el habitual _loop_ editar-compilar-ejecutar. Para más detalles sobre cómo funciona el proceso de compilación dentro de .NET, consulta este [enlace](https://docs.microsoft.com/en-us/dotnet/standard/clr).  Pero, el lenguaje viene con consola donde [se puede programar interactivamente](https://learn.microsoft.com/en-us/dotnet/fsharp/tools/fsharp-interactive/). Además, si estás acostumbrado a escribir código en un [_notebook_](https://jupyter.org/), ¡se puede cargar un _kernel_ de F\# y usarlo! 

Estas son algunas de las ventajas que se obtienen porque _F# forma parte de .NET_:

- Es totalmente multiplataforma.
- .NET es una amplia biblioteca.
- Es fácil de integrar con otros lenguajes .NET, como C# o VB.
- Es de código abierto.
- Hay grandes herramientas disponibles para desarrollar en F#.
- Tiene una comunidad enorme y amigable.


# ¿Qué es una función?

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

# En preparación para el despegue

Estas guías están orientadas a aquellos programadores interesados ​​en aprender algunos conceptos sobre programación funcional en F\#, desde una perspectiva práctica. F\# es un excelente lenguaje de programación _first_ funcional: es funcional (por supuesto), tiene una sintaxis limpia y legible (sin muchos símbolos sofisticados y demás), es flexible (en caso de que necesite comprender algún otro paradigma en medio de su código) y es lo suficientemente conciso para expresar sus ideas con claridad.

Aprender un nuevo lenguaje _y_ un nuevo paradigma de programación es una aventura maravillosa. No necesita ninguna preparación especial, aunque supongo que el lector tiene experiencia en al menos un lenguaje popular (digamos C, Python, Java o JavaScript, por ejemplo). Espero que disfrutes tu viaje tanto como yo lo hice cuando comencé con F\# hace unos años. Recuerda programar y cometer errores, pues ahí es donde se transforma el aprendizaje.

F\#, como cualquier otro lenguaje informático, tiene su propia sintaxis y peculiaridades que es necesario comprender. Pero aprender el paradigma funcional requiere dejar de lado algunos conceptos que a uno le gustan mucho y que usa en la programación diaria en lenguajes imperativos u orientados a objetos.

> Esta diferencia tiene profundas raíces en el mismo origen de la informática.
> Al principio, había dos enfoques de computación formal mutuamente equivalentes, uno desarrollado por Alan Turing con su [computing machine](https://londmathsoc.onlinelibrary.wiley.com/doi/epdf/10.1112/plms/s2-42.1.230), mientras que el otro fue desarrollado por Alonzo Church con su [computing machine](https://londmathsoc.onlinelibrary.wiley.com/doi/epdf/10.1112/plms/s2-42.1.230). Ambos enfoques evolucionaron en paralelo y dieron lugar a diferentes ramas de estilos de programación.
> La programación funcional no es nueva en absoluto. LISP, el primer lenguaje de programación funcional apareció en 1958, apenas un año antes de su
> contrapartida imperativa, Fortran.
>
> En un sentido muy amplio, aprender un lenguaje funcional es como viajar en el tiempo, retroceder por la rama imperativa de la programación y tomar la otra salida de la rotonda.

Aunque finalmente no adopte el paradigma funcional, agregar un nuevo estilo de lenguaje a sus activos le brindará la experiencia para revisar viejos hábitos de programación con nuevos ojos, brindando una comprensión más profunda y un ajuste más fino de su código. ¡Te convertirás en un mejor programador, definitivamente!

F\# es uno de los lenguajes proporcionados por .NET (junto con C# y Visual Basic). Como tal, F\# está estrechamente integrado con todas las herramientas y bibliotecas que proporciona .NET. Pero en este enfoque, veremos a .NET como el conjunto de bibliotecas que uno puede usar para mejorar su código. En otras palabras, me mantendré al principio alejado de usarlos y me centraré en las características principales de F\# como lenguaje funcional. Desde la programación web hasta el aprendizaje automático, desde bases de datos hasta Windows, .NET tiene todo lo que necesita para que su programación sea una mejor experiencia, una vez que conozca las características principales que proporciona F\#.

Además, mi objetivo es mantenerlo lo más simple posible, porque me gustaría que el lector tenga el conjunto fundamental de herramientas del idioma para jugar. Con ese fin, algunos conceptos se dejan de lado a propósito y se revelarán en el futuro.

> Si eres un programador de C#, probablemente obtendrás un mejor rendimiento al utilizar algunos de los excelentes recursos de F\# para programadores de C#, como [Get programming with F\#](https://www.manning.com/books/get-programming-with-f-sharp#:~:text=about%20the%20book-,Get%20Programming%20with%20F%23%3A%20A%20guide%20for%20.,of%20functional%20programming%20in%20F%23.)


## Herramientas

Esta serie trata sobre los fundamentos de la programación de F\#, y ninguno de los ejemplos necesita siquiera instalar F\#. Simplemente puede programar en su navegador web de su elección, dirigiéndose a [Fable REPL](https://fable.io/repl/). Simplemente escriba su código en el panel izquierdo, haga clic en el botón de reproducción habitual (o Alt+Enter) y ya está, ¡está programando en F\#!.

También puede instalar el lenguaje F\# y usar su editor diario, pero deberá instalar [.NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/7.0), disponible para Windows, Linux y macOS.

Además, esta serie está escrita como [Jupyter notebooks](https://jupyter.org/). Puede obtenerlos en [este repositorio de Github](https://github.com/fcolavecchia/fp-course). Las instrucciones sobre cómo configurar F\# como kernel de Notebook se pueden encontrar en [acá](https://github.com/dotnet/interactive/blob/main/docs/NotebookswithJupyter.md). .

[Visual Studio Code](https://code.visualstudio.com/) tiene una extensión llamada [Ionide](https://ionide.io/Editors/Code/overview.html) para trabajar con F\#. Yo personalmente uso JetBrains Rider para mi programación diaria que admite F\#.

También puede usar F\# con Visual Studio para Windows o MacOS, si está familiarizado con esa herramienta.

Además, si desea programar F\# con `vim`, [Ionide también está disponible para `vim`](https://ionide.io/Editors/Vim/overview.html).




### Comenzando la diversión con `let`

Cualquier código que escriba necesitará datos.

Cualquier código que escriba hará _algo_ con esos datos.

Entonces, el primer paso en un nuevo lenguaje es aprender cómo definir una forma de almacenar sus datos y cómo transformarlos a lo largo del programa. Estos conceptos generalmente se traducen en _variables_ y _rutinas_ (o cualquier otro nombre, como procedimientos o funciones, según el alcance de su programación), respectivamente. De esta forma, uno usa rutinas para cambiar las variables, o crear otras, desde una orden para comprar algo en un sitio web, hasta el color de un píxel en tu juego de preferencia.

Pero volvamos atrás y comencemos con _expresiones_. Una expresión es un conjunto ordenado de símbolos que pueden representar diferentes entidades en el código. Casi como una oración en un lenguaje humano, una expresión debe tener una sintaxis válida (nadie me entenderá si uso mal el punto) y también debe ser significativa (yo un ejemplo para si yo nadie del tipo de oración las palabras comprenderán). El compilador, sin embargo, es mucho más estricto con las reglas y no permite este tipo de poemas. ¿Por qué? Porque el compilador necesita captar la expresión, procesarla y obtener un _valor_. Entonces el valor es el resultado de la evaluación de una expresión.

Y dado que cada expresión da como resultado un valor, uno debe administrar todos estos valores que aparecerán a medida que se ejecuta el código, identificarlos y usarlos como mejor le parezca. Para ello, F\# utiliza varias _palabras clave_, y una de ellas es `let`.

En F\# se dice que `let` _vincula_ una expresión a un identificador. Por ejemplo


```fsharp
// Mi primer línea en F# es un comentario ¯\_(シ)_/¯
let j = 1 // un entero 
```

une la expresión literal `1` al nombre `a`. En el lado derecho del símbolo `=` (que actúa aquí como un operador vinculante), uno puede tener cualquier expresión válida:

```fsharp
let a = 1.0 + 3.0 // punto flotante 
let s = "this is a string" 
let l = [1 ; 2 ; 3] // una lista de enteros
let m = ("Messi",10) // Una tupla de un string y un entero
let t = true // Un tipo lógico (booleano)
```

etcétera. Por supuesto, uno puede asociar un identificador usado antes con uno nuevo a través de una expresión:

```fsharp
let b = a + 4.0 
```

En la línea anterior, agregamos cuatro a `a` y vinculamos el resultado de esta expresión al identificador `b`.

### Inmutabilidad

Sin embargo, no puede vincular una expresión a un identificador que ya se ha utilizado:

```fsharp
let q = 3
let q = q + 1
```

lo que responde por qué no se usa el término _variable_ en F\#: una vez que se obtiene el valor de una expresión, **no se puede cambiar**. En otras palabras, todos los valores son _inmutables_: una vez allí, no se pueden cambiar. No hay variables en el idioma, porque no hay nada que _varíe_. Puede crear tantos valores como desee, pero no puede cambiarlos:

```fsharp
let q = 3
let qq = q + 1
```

Las implicaciones de esto son profundas y es muy importante dejar que el concepto madure y se asiente, porque permea todo el código en F\# (y cualquier otro lenguaje funcional). De alguna manera, como todo es una expresión, programar en F\# es gestionar las expresiones para que resuelven nuestro problema. Se nombra una expresión (es decir, la vincula a un identificador), se la usa en otra expresión, se vincula su valor a un nuevo identificador y así sucesivamente.

La perspectiva de un código como una larga lista de enlaces `let` es un poco desalentadora. Ahí es donde entran las funciones.

<!-- > Aquí está el [Jupyter notebook de esta guía](https://github.com/fcolavecchia/fp-course/blob/main/en/Fundamentals.ipynb). -->

# Ejercicios

### Funciones en la vida real 

- Escriba diez ejemplos de funciones en la vida real. 

- Escriba tres ejemplos de funciones que puedan componerse en la vida real. 

- Escriba tres ejemplos de la vida real donde se vea la aplicación parcial de funciones.

### Una introducción a un _Kata_ 

Un _kata_ es un ejercicio de programación que puede resolverse en una o dos horas. En general
se presenta como un ejercicio con varias partes, que deben ser resueltas en el orden indicado. A 
diferencia de un ejercicio _a secas_, hay algunas reglas para seguir.

1. En cada parte del ejercicio, se busca mejorar la forma en que se implementó el código, 
respecto de la parte anterior.
2. Es fundamental escribir _tests_. Esto es, pequeños programas para probar el código en
casos conocidos. El programa final que resuelve el ejercicio debe ser capaz de _pasar_ todos
los tests escritos.



### FizzBuzz _Kata_ 

Este es un ejercicio clásico de programación. Use su lenguaje de programación habitual.

- Escriba un programa que imprime los números del 1 al 100. Pero, cuando un número es múltiplo de tres, tiene que imprimir `Fizz` en lugar del número. Y para 
números múltiplos de cinco, tiene que imprimir `Buzz` en lugar del número. además, si el número 
es múltiplo de tres y cinco, debe imprimir `FizzBuzz`. 

Por ejemplo,

- 9 es divisible por 3, con lo cual debe imprimirse `Fizz`.
- 15 es divisible por 3 y por 5, con lo cual debe imprimirse `FizzBuzz`.
- 25 es divisible por 5, con lo cual debe imprimirse `Buzz`.
- 34 no es divisible ni por 3 ni por 5, con lo cual debe imprimirse 34.

El resultado esperado hasta desde el 1 hasta el 15 es:
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
```

- Ahora considere que se quiere imprimir los números desde 1 hasta un número arbitrario cualquiera 
_N_ mayor que 1. 

- Siguiendo la idea anterior, agregue ahora el caso de reemplazar los múltiplos de siete en 
la lista por `Whizz`. Por supuesto, en caso en que el número sea múltiplo de 3 y 7, deberá imprimir
`FizzWhizz`, mientras que en el caso en que sea múltiplo de 3, 5 y 7, debería decir `FizzBuzzWhizz`, etc.

- ¿Cuál sería la mejor manera de implementar el código para que sea sencillo agregar un caso arbitrario
de múltiplos en el futuro? 

### El _Kata_ de los precios en el supermercado

En general, la mayoría de los productos en el supermercado tienen un precio definido en pesos, por ejemplo,
un paquete de galletas puede costar 123,75\$. 
Pero también hay productos que se venden por peso, como un kilo de manzanas o naranjas.
Además, se ven frecuentemente algunas promociones como:

- Si lleva dos del mismo producto, 70% de descuento en la segunda unidad.
- Una lata de atún puede estar de oferta, llevando tres , paga por dos.
- O puede ser que por 1000 pesos uno pueda llevarse tres paquetes de yerba.

Este es un kata para la ducha. No es necesario en este punto implementarlo como código, sino más bien
pensar el modelo que uno usaría para describir, por ejemplo, cómo sumar los distintos precios 
de los productos en la caja. Algunas cosas para pensar

- ¿Se puede tener valores de precio representados por números periódicos?
- ¿Cómo manejar el redondeo?
- ¿Cómo diseñar los tests para los casos de arriba?






