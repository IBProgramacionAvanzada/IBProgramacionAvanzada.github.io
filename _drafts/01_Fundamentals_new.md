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
