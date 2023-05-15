---
layout: post
title: Organizando el código
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---
El comando `dotnet` es el que permite el manejo de los códigos en .NET.  Si bien se usa el mismo comando, tiene una variante de _runtime_ que permite ejecutar programas, y una variante de desarrollo, que se utiliza para crear, organizar, testear y compilar códigos.


```bash
dotnet --help
```
da información sobre el comando y sus variantes:

```bash
Usage: dotnet [runtime-options] [path-to-application] [arguments]

Execute a .NET application.

runtime-options:
  --additionalprobingpath <path>   Path containing probing policy and assemblies to probe for.
  --additional-deps <path>         Path to additional deps.json file.
  --depsfile                       Path to <application>.deps.json file.
  --fx-version <version>           Version of the installed Shared Framework to use to run the application.
  --roll-forward <setting>         Roll forward to framework version  (LatestPatch, Minor, LatestMinor, Major, LatestMajor, Disable).
  --runtimeconfig                  Path to <application>.runtimeconfig.json file.

path-to-application:
  The path to an application .dll file to execute.

Usage: dotnet [sdk-options] [command] [command-options] [arguments]

Execute a .NET SDK command.

sdk-options:
  -d|--diagnostics  Enable diagnostic output.
  -h|--help         Show command line help.
  --info            Display .NET information.
  --list-runtimes   Display the installed runtimes.
  --list-sdks       Display the installed SDKs.
  --version         Display .NET SDK version in use.

SDK commands:
  add               Add a package or reference to a .NET project.
  build             Build a .NET project.
  build-server      Interact with servers started by a build.
  clean             Clean build outputs of a .NET project.
  format            Apply style preferences to a project or solution.
  help              Show command line help.
  list              List project references of a .NET project.
  msbuild           Run Microsoft Build Engine (MSBuild) commands.
  new               Create a new .NET project or file.
  nuget             Provides additional NuGet commands.
  pack              Create a NuGet package.
  publish           Publish a .NET project for deployment.
  remove            Remove a package or reference from a .NET project.
  restore           Restore dependencies specified in a .NET project.
  run               Build and run a .NET project output.
  sdk               Manage .NET SDK installation.
  sln               Modify Visual Studio solution files.
  store             Store the specified assemblies in the runtime package store.
  test              Run unit tests using the test runner specified in a .NET project.
  tool              Install or manage tools that extend the .NET experience.
  vstest            Run Microsoft Test Engine (VSTest) commands.
  workload          Manage optional workloads.

Additional commands from bundled tools:
  dev-certs         Create and manage development certificates.
  fsi               Start F# Interactive / execute F# scripts.
  user-jwts         Manage JSON Web Tokens in development.
  user-secrets      Manage development user secrets.
  watch             Start a file watcher that runs a command when files change.
```


```bash
dotnet --info
```
muestra la información de _runtime_ y _sdks_ instalados:

```bash
.NET SDK:
 Version:   7.0.102
 Commit:    4bbdd14480

Runtime Environment:
 OS Name:     Mac OS X
 OS Version:  12.6
 OS Platform: Darwin
 RID:         osx.12-x64
 Base Path:   /usr/local/share/dotnet/sdk/7.0.102/

Host:
  Version:      7.0.2
  Architecture: x64
  Commit:       d037e070eb

.NET SDKs installed:
  3.0.100 [/usr/local/share/dotnet/sdk]
  3.0.101 [/usr/local/share/dotnet/sdk]
  3.1.424 [/usr/local/share/dotnet/sdk]
  5.0.402 [/usr/local/share/dotnet/sdk]
  6.0.200 [/usr/local/share/dotnet/sdk]
  6.0.301 [/usr/local/share/dotnet/sdk]
  7.0.102 [/usr/local/share/dotnet/sdk]

.NET runtimes installed:
  Microsoft.AspNetCore.App 3.0.1 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 3.1.30 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 5.0.11 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 6.0.2 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 6.0.6 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 7.0.2 [/usr/local/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.NETCore.App 3.0.1 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 3.1.30 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 5.0.11 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 5.0.17 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 6.0.2 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 6.0.6 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]
  Microsoft.NETCore.App 7.0.2 [/usr/local/share/dotnet/shared/Microsoft.NETCore.App]

Other architectures found:
  None

Environment variables:
  Not set

global.json file:
  Not found

Learn more:
  https://aka.ms/dotnet/info

Download .NET:
  https://aka.ms/dotnet/download
```


### Organización de código en .NET

#### Proyectos
Son colecciones de código en alguno de los lenguajes de .NET (VB,C#,F#). Típicamente uno encuentra un archivo de texto estructurado en XML con la información del proyecto. En F# son archivos de extensión `.fsproj`


#### Soluciones
Son colecciones de Proyectos que constituyen un programa particular. 

### Creando soluciones y proyectos

Para crear soluciones o proyectos, se usa el comando `dotnet new`: 

```bash
dotnet new --help
```

Supongamos que queremos crear la solución `HolaMundo`:
```bash
dotnet new sln -o HolaMundo
```
> Nótese que el comando `dotnet new sln` utilizado con la opción `-o` crea un directorio donde se va a desarrollar la solución. 
El comando 
```bash
dotnet new list
```
muestra todos los tipos de proyectos que se pueden crear. Además, se pueden instalar nuevos plantillas (_templates_) de proyectos. 

Vamos a crear dos proyectos, una aplicación de consola y una biblioteca. 

> Si bien la estructura de proyectos queda a criterio de quienes programan, en general se crea un directorio `src` **dentro** del
> directorio donde está la solución, donde a sus vez se crearán los proyectos.

Primero entonces hacemos

```bash
cd HolaMundo
mkdir src
```

Para crear la aplicación de consola, ejecutamos:
```bash
dotnet new console -lang F# -o src/MyProject
```
mientras que para crear la biblioteca:
```bash
dotnet new classlib -lang F# -o src/MyLibrary
```
Finalmente, agregamos ambos proyectos a la solución:
```bash
dotnet sln add src/MyProject/MyProject.fsproj
dotnet sln add src/MyLibrary/MyLibrary.fsproj
```

La estructura de directorios quedaría así:

```bash
.
└── HolaMundo/
    ├── src/
    │   ├── MyProject/
    │   │   ├── bin/ 
    │   │   ├── obj/
    │   │   ├── Program.fs 
    │   │   └── MyProject.fsproj
    │   └── MyLibrary/
    │       ├── obj/
    │       ├── Library.fs 
    │       └── MyLibrary.fsproj      
    └── HolaMundo.sln     
```

### Pausa: creando un repositorio

Siempre es conveniente utilizar `git` para mantener nuestro código. Antes de olvidarnos, conviene que creemos
el repositorio entonces en GitHub (o el sitio de la nube donde guardaremos el código). Al crearlo, obtendremos un
`REMOTE_URL` (se encuentra en el botón verde `<> Code`).

> Al crear el repositorio en GitHub, conviene agregar _Visual Studio_ como .gitignore. De esta forma,
> git se encargará de ignorar archivos temporales que se crean al compilar un código de .NET. 

Estando en el directorio `HolaMundo`:

```bash
git init 
```
para crear el repositorio local. Luego conviene agregar sólo el archivo de solución `.sln` (agregaremos
el resto de los archivos después, una vez que tengamos `.gitignore` copiado desde el repo remoto al local).

```bash
git add HolaMundo.sln
git commit -am "First Commit"
```

Conectamos el repo local con el remoto:
```bash 
$ git remote add origin REMOTE_URL
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
```
Le indicamos a `git` cómo queremos que mezcle las novedades de las distintas instancias 
de nuestro repositorio
```bash
git config pull.rebase false
```
En este punto tenemos dos repositorios
- El local, con su historia dada por el primer commit `First Commit`
- El remoto, también con su propia historia, que fue generada al crear el `README.md` y `.gitignore`

Entonces, traemos las modificaciones del repositorio remoto y mezclamos su historia con el local:
```bash
git pull origin main --allow-unrelated-histories
```
> La opción `--allow-unrelated-histories` sólo es necesaria en esta situación, a partir de aquí, ambos 
> repositorios comparten la historia.

Aparecerá un texto para registrar esta mezcla, típicamente podríamos poner 'Merge histories of local and remote', 
por ejemplo.

Ahora sí, con `.gitignore` en el repo local, podemos agregar los fuentes
```bash
git add .
```
para luego terminar con
```bash
git commit -m "Added srcs"
git push origin main
``` 

Se puede usar `git push --set-upstream origin main` para no tener que especificar qué rama uno quiere hacer `pull`.


[Aquí](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github) están las instrucciones completas oficiales,
pero [ésta página contiene una mejor explicación](https://www.freecodecamp.org/news/create-and-sync-git-and-github-repositories/#how-to-create-a-remote-github-repository).


### Agregando referencias

Si ahora queremos usar nuestra bibioteca `MyLibrary` en nuestro proyecto `MyProject`, tenemos que agregarla como referencia:

```bash
cd src/MyProject
dotnet add reference ../../src/MyLibrary/MyLibrary.fsproj
```

Por otra parte, si quisiéramos agregar una biblioteca externa, por ejemplo `FSharp.Data`, tendríamos que hacer:

```bash
cd src/MyProject
dotnet add package FSharp.Data
```

