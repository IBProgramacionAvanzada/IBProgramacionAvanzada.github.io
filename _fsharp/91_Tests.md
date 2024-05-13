##

## Tests

Todo programa tiene que ser probado para confirmar que hace lo que uno dice que hace. Una manera de hacerlo es correr el programa completo cada vez que se introduce una modificación significativa. Este modelo de trabajo es a todas luces costosísimo, particularmente en el tiempo que lleva realizar una y otra vez este procedimiento, y se vuelve inmanejable a medida que la aplicación crece en complejidad. 

Para eso existen [distintos tipos de pruebas o _tests_](https://programacionymas.com/blog/tipos-de-testing-en-desarrollo-de-software) que se pueden programar para que la computadora los realice automáticamente. 

Un test consiste esencialmente en correr una parte del código con ciertos datos (que pueden ser reales o sintéticos),
y comparar el resultado de esa corrida con uno conocido.

Algunos de los tipos de tests que se usan son:

- Test unitarios: donde se prueba cada parte del programa en forma aislada, evaluando distintas funciones, por ejemplo. 
- Test de integración: donde se verifican el flujo de trabajo entre distintas componentes del sistema.
- Test end-to-end: donde se evalúa la experiencia completa del usuario, definida a partir de los distintos casos de uso de la aplicación.

Los lenguajes de programación modernos ofrecen distintas bibliotecas para poder escribir tests. En particular, en el ecosistema .NET [existen varias opciones](https://learn.microsoft.com/en-us/dotnet/core/testing/):

- xUnit
- NUnit
- MSTest 
- [Expecto](https://www.codit.eu/blog/the-beautiful-f-expecto-paradigm-think-in-tests-not-frameworks/) 

Veremos a continuación cómo agregar tests unitarios a nuestro ejemplo `HolaMundo`.

### NUnit

Usaremos NUnit para escribir nuestros tests. Para empezar creamos un directorio `tests` en el directorio en el que se encuentra nuestra 
solución:

```bash
mkdir tests
cd tests 
```
> No es obligatorio, pero es una buena práctica que los tests estén en su propio directorio. 
>

con lo cual, nuestra estructura de directorios quedaría así:

```bash
.
└── HolaMundo/
    ├── src/
    │   ├── MyProject/
    │   └── MyLibrary/    │           
    └── tests/
    └── HolaMundo.sln 
```

Ahora vamos a crear un proyecto de NUnit usando:

```bash
dotnet new nunit -lang F# -o tests/MyTests
``` 
con lo cual tendremos:
```bash
.
└── HolaMundo/
    ├── src/
    │   ├── MyProject/
    │   └── MyLibrary/         
    └── tests/
    │   └── MyTests/    
    └── HolaMundo.sln 
```

> Es posible que inicialmente la creación del test demore, debido a que `dotnet` realiza la operación de _restore_. Restore
> se ocupa de actualizar las dependencias del proyecto, lo cual puede implicar la búsqueda en la nube de módulos que
> no se encuentran instalados localmente. 

Luego podemos agregar el proyecto de tests a la solución:

```bash
dotnet sln add tests/MyTests/MyTests.fsproj
```

Como todos los _templates_, `dotnet` nos provee de un código mínimo pero funcional al crear el proyecto `MyTests`. Si hacemos

```bash
/HolaMundo> dotnet test 
```
Correrá el test trivial que propone el _template_: 

```fsharp
[<Test>]
let Test1 () =
    Assert.Pass()
```

Se puede correr `dotnet test` con la opción `-t` para ver cuáles son los test que se van a correr:
```bash
/HolaMundo> dotnet test -t
  Determining projects to restore...
  All projects are up-to-date for restore.
  MyLibrary -> /Users/flavioc/Codes/GitHub/HolaMundo/src/MyLibrary/bin/Debug/net7.0/MyLibrary.dll
  MyTests -> /Users/flavioc/Codes/GitHub/HolaMundo/tests/MyTests/bin/Debug/net7.0/MyTests.dll
Test run for /Users/flavioc/Codes/GitHub/HolaMundo/tests/MyTests/bin/Debug/net7.0/MyTests.dll (.NETCoreApp,Version=v7.0)
Microsoft (R) Test Execution Command Line Tool Version 17.4.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

The following Tests are available:
    Test1
```


A modo de ejemplo, vamos a testear alguna función de nuestra biblioteca `MyLibrary`. Para ello, tenemos que avisarle al proyecto
`MyTests` de la existencia de la biblioteca:

```bash
dotnet add tests/MyTests/MyTests.fsproj reference src/MyLibrary/MyLibrary.fsproj 
``` 

Podemos escribir algunos test, por ejemplo:

```fsharp
[<Test>]
let ``Suma 2 mas 2`` () = 
    let expected = 4
    let actual = add 2 2 

    Assert.That(expected, Is.EqualTo(actual))
```

Para ir más allá de la prueba caso a caso, uno puede hacer [tests basados en propiedades](https://fsharpforfunandprofit.com/pbt/).


