---
layout: home
title: Programación Avanzada
permalink: /
---

Te damos la bienvenida al curso de _Programación Avanzada_ del Instituto Balseiro. En este sitio encontrarás el material del curso (a veces en castellano, a veces en inglés). El único requisito es estar familiarizado previamente con algún lenguaje de programación.

El curso apunta a exponer al estudiante a un lenguaje de programación funcional. En esta versión del curso, se utiliza [F\#](https://learn.microsoft.com/en-us/dotnet/fsharp/what-is-fsharp). Sin embargo, no se trata exclusivamente de un curso para aprender un lenguaje, sino que también visitaremos otros aspectos de la programación que son relevantes y pueden practicarse con cualquier lenguaje. Tocaremos algunos temas de diseño de software y de técnicas de programación. Por otra parte, repasaremos fundamentos formales de la programación funcional, para poner en contexto este paradigma.



## Aspectos prácticos

### Link

El link con la documentación del curso es [https://tinyurl.com/ibadv](https://tinyurl.com/ibadv).

### `Git`

Para participar activamente del curso, es necesario tener una cuenta en [GitHub](www.github.com). Tanto la entrega de ejercicios como el feedback sobre los mismos se realizará en esa plataforma, utilizando la aplicación `git`. Si bien el cursado de la materia requerirá sólo los comandos básicos, puede resultar útil tener más conocimiento 
del tema. [Acá](https://happygitwithr.com/) se puede encontrar una guía completa sobre `git`. 

> Una vez que se cree el usuario en GitHub, envíe esa información a flavio.colavecchia@ib.edu.ar así se le incluye en el curso. 

> Si no le gusta usar la terminal, puede descargarse [GitHub Desktop](https://desktop.github.com/). Veremos su uso en clase.

### Editor

Para cualquier lenguaje moderno es necesario contar con un editor potente que permita manejar las distintas capacidades de edición actuales. Tanto [Visual Studio Code](https://code.visualstudio.com/download) como [JetBrains Rider](https://www.jetbrains.com/rider/) son los editores recomendados. 
Mientras que el primero ofrece [una versión web](https://vscode.dev/) que no requiere de instalación, el segundo es un editor pago, pero que [puede usarse gratis para educación](https://www.jetbrains.com/community/education/#students). 

### Textos

Eventualmente se pedirá la entrega de algún ejercicio escrito que no sea un programa. Para ello utilizamos [Markdown](https://www.argentina.gob.ar/contenidosdigitales/escribir-en-markdown/markdown-basico) que posee una sintaxis simple, aunque permite agregar ecuaciones $$f(g(x)) = g(x)+1$$ en LaTeX.

### Jupyter notebooks

> Veremos esto a partir de la clase 1.

El curso está editado en una serie de Jupyter notebooks, que permiten trabajar en forma interactiva. Si bien después de las primeras clases se recomienda instalar todo el _toolchain_  de aprendizaje y desarrollo localmente, se puede utilizar los contenidos
en [Binder](https://mybinder.org). Para ello:

- Con su usuario de GitHub, haga un _fork_ del repositorio [https://github.com/fcolavecchia/fp-course](). 

- Dirígase a My Binder ([https://mybinder.org/](https://mybinder.org/))

- Complete la información solicitada en My Binder. Por ejemplo, si el usuario de GitHub es `fulano`,
  entonces el nombre del repositorio será `https://github.com/fulano/fp-course`. Deje vacío el campo referido a `Git ref`, y el que define un notebook default. 

- Debería ver algo así:

![.NET history line](/img/mybinder-config.png)

- Listo, haga click en launch. Después de un rato podrá ver los notebooks en mybinder. El lenguaje 
  default es C#, por lo tanto es necesario cambiarlo en la esquina superior derecha de la página:

![I do not want C#](/img/i-do-not-want-csharp.png)

y elegir F#

![I want F#](/img/i-want-fsharp.png). 

El resto es usar el notebook como en Python.




### Cronograma

0. Intro a la programación funcional
    - Nociones básicas de F\#, inmutabilidad, tipos básicos.
    - Ejemplos de funciones
    - Composición
    - Aplicación parcial

1. Funciones
    - Instalación de herramientas.
    - Ejemplos de funciones
    - Composición
    - Aplicación parcial

2. Tipos
    - Tipos algebraicos
    - Tipo Suma, _Discriminated Unions_
    - Tipo Producto, _Records_ 

3. Control de flujo
    - La expresión `if`
    - Más sobre _pattern matching_ 
    - Recursión     

4. Colecciones
    - Colecciones heterogeneas: tuplas
    - Colecciones homogeneas: listas, `seq`
    - _List comprehension_     

5. Dojo    

6. Tipos genéricos    
    - Option
    - Result 

7. Misc
    - IO
    - Units of measure
    - Excepciones 

8. .NET
    - Organizando el código en módulos, proyectos y soluciones
    - Testing 













