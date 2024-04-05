
Te damos la bienvenida al curso de _Programación Avanzada_ del Instituto Balseiro. En este sitio encontrarás el material del curso (a veces en castellano, a veces en inglés). El único requisito es estar familiarizado previamente con algún lenguaje de programación.

El curso apunta a exponer al estudiante a un lenguaje de programación funcional. En esta versión del curso, se utiliza [F\#](https://learn.microsoft.com/en-us/dotnet/fsharp/what-is-fsharp). Sin embargo, no se trata exclusivamente de un curso para aprender un lenguaje, ya que visitaremos otros aspectos de la programación que son relevantes y pueden practicarse con cualquier lenguaje. Tocaremos algunos temas de diseño de software y de técnicas de programación. Por otra parte, repasaremos fundamentos formales de la programación funcional, para poner en contexto este paradigma.



## Aspectos prácticos

### Links

La documentación del curso está [acá](https://tinyurl.com/ibadv).

Para empezar, podemos programar en F# en [Fable Repl](https://fable.io/repl/).

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

### Instalando el _toolchain_ 

Si bien en las primeras clases utilizaremos alguno de los entornos web para iniciarnos en la programación en F#, es conveniente ir acercándose 
al juego completo de herramientas. Para poder utilizar F# en Jupyter notebooks, [seguir estas instrucciones](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.dotnet-interactive-vscode) para instalar _Polyglot Notebooks_.

#### Ubuntu 22.04

Para instalar el toolchain en ubuntu 22.04, es necesario [seguir algunos pasos detalladamente](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu).

- Remover versiones anteriores de .NET instaladas quizás desde los paquetes nativos de ubuntu. 
- Como _Polyglot Notebooks_ es compatible con .NET 8.0, que [requiere ser instalada manualmente en esta versión de ubuntu](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu#register-the-microsoft-package-repository).

<!-- ```bash
# Get Ubuntu version
declare repo_version=$(if command -v lsb_release &> /dev/null; then lsb_release -r -s; else grep -oP '(?<=^VERSION_ID=).+' /etc/os-release | tr -d '"'; fi)

# Download Microsoft signing key and repository
wget https://packages.microsoft.com/config/ubuntu/$repo_version/packages-microsoft-prod.deb -O packages-microsoft-prod.deb

# Install Microsoft signing key and repository
sudo dpkg -i packages-microsoft-prod.deb

# Clean up
rm packages-microsoft-prod.deb -->
```bash 
# Update packages
sudo apt update
```
<!-- La salida de todas estas instrucciones debiera parecerse a esto:
![Instalación de .NET 8.0 en Ubuntu 22.04](/img/dotnet-install-22.04.png)
 -->


- Luego instalar el SDK con 

```bash
sudo apt install dotnet-sdk-8.0
```

- Finalmente, abrir VSCode e instalar la extensión Polyglot Notebooks. La misma extensión instalará una versión
  compatible de .NET interactive, que provee los _kernels_ de .NET para Jupyter.






<!-- ### Videos

En este momento del siglo XXI, no hay manera de escapar a la información que viene en videos. En el siguiente link
hay una lista de videos que con temas asociados a la materia. 

[Videos de programación](https://youtube.com/playlist?list=PLnMc6Rr34vPYU5liVvEL_irif5XJQoBpK). 
 -->




<!-- ### Jupyter notebooks

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

El resto es usar el notebook como en Python. -->




### Cronograma

0. Intro a la programación funcional
    - Nociones básicas de F\#, inmutabilidad, tipos básicos.
    - Ejemplos de funciones
    - Composición
    - Aplicación parcial

1. Tipos 
    - Instalación de herramientas.
    - Tipos algebraicos
    - Tipo Suma, _Discriminated Unions_
    - Tipo Producto, _Records_ 

<!-- 3. Control de flujo
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
 -->












