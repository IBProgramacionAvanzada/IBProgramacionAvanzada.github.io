
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
