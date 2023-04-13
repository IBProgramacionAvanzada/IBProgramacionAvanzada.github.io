---
layout: post
title: Records
tagline: Tratar con entidades que no pertenecen a un grupo de casos bien predefinido.
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Presentábamos antes la necesidad de agregar información útil de algunas entidades que no se puede restringir a tener unos tipos específicos, por ejemplo, los datos de una tarjeta de crédito.

Prácticamente cualquier lenguaje de alto nivel proporciona una forma de definir dichos tipos. En lenguaje C uno tiene el tipo `struct`, en Python una clase sin métodos (o una [tupla con nombre](https://docs.python.org/3/library/collections.html#collections.namedtuple)), etc. Este tipo en F\# se denomina _record_.

```fsharp
type CreditCard =
    {
        HoldersName : string
        Number: string
        ExpirationDateMonth: uint8 
        ExpirationDateYear: uint8 
        CVV: uint16
    }
```

El tipo record utiliza etiquetas para agregar los diferentes componentes del tipo. Cada componente tiene una etiqueta (`HoldersName`, `Number`, etc.) y un tipo asociado a él.
Para crear un tipo record, es necesario definir todos y cada uno de los componentes:

```fsharp
let doeCard = 
    {
        HoldersName = "John Doe"
        Number = "1234 5678 9101 1121"
        ExpirationDateMonth = 12uy
        ExpirationDateYear = 23uy 
        CVV = 111us 
    }

```

> 🔔 Tenga en cuenta el sufijo `uy` para enteros sin signo de 8 bits (`uint8`) y `us` para su compañero de 16 bits (`uint16`).

En lugar de indentar la definición, uno puede escribir todos los componentes juntos, separados por `;`:

```fsharp
let doeFakeCard = { HoldersName = "John Doe"; Number = "1234 5678 9101 1121"; ExpirationDateMonth = 12uy; ExpirationDateYear = 23uy ; CVV = 111us}

printfn "%A" doeFakeCard    
```

    { HoldersName = "John Doe"
      Number = "1234 5678 9101 1121"
      ExpirationDateMonth = 12uy
      ExpirationDateYear = 23uy
      CVV = 111us }


pero como se puede ver, esto es adecuado [solo para records pequeños](https://learn.microsoft.com/en-us/dotnet/fsharp/style-guide/formatting#formatting-record-expressions).

¿Qué sucede si la tarjeta de John Doe vence y necesita ser reemplazada por una nueva?
Al igual que con todos los demás valores en el idioma, los records son _inmutables_, por lo que no es posible actualizar `doeCard` en su lugar. Para hacer eso, necesitamos crear otro valor nuevo. F\# proporciona una forma de _copiar y actualizar_ un valor del registro, que nos permite cambiar solo los componentes que deben cambiarse en un record. Suponiendo que la nueva tarjeta mantenga el número (y, por supuesto, el nombre del titular de la tarjeta), tendríamos:

```fsharp
let newDoeCard = 
    { doeCard with 
        ExpirationDateMonth = 12uy
        ExpirationDateYear = 25uy
        CVV = 222us 
    }

printfn "%A" newDoeCard    
```

    { HoldersName = "John Doe"
      Number = "1234 5678 9101 1121"
      ExpirationDateMonth = 12uy
      ExpirationDateYear = 25uy
      CVV = 222us }


Uno usa nuevamente etiquetas para expresar el tipo de record. De esta manera se crea un nuevo registro utilizando el valor antiguo `doeCard` usando `with` para identificar los componentes que necesitan ser actualizados.

Para acceder a un componente específico de un record, se usa nuevamente `.`, como hicimos con las uniones discriminadas:

```fsharp
printfn "John's Does card number: %A" newDoeCard.Number 
printfn "John's Does card CVV: %A" newDoeCard.CVV
```

    John's Does card number: "1234 5678 9101 1121"
    John's Does card CVV: 222us


## Mezclando tipos

La unión discriminada y el record son las dos formas en que uno puede representar entidades en el lenguaje. Uno puede construir todo tipo de tipos (;-D) complejos mezclándolos, depende del programador cómo combinar estos ladrillos más pequeños para modelar el dominio.

Por ejemplo, uno puede juntar la fecha de vencimiento en su propio tipo:

```fsharp
type ExpirationDate =
    { 
        Month : uint8
        Year : uint8 
    }
```

Eso nos daría un tipo `CreditCard2` más limpio:

```fsharp
type CreditCard2 =
    {
        HoldersName : string
        Number: string
        ExpirationDate: ExpirationDate 
        CVV: uint16
    }
```

Para la máquina expendedora, se puede escribir

```fsharp
type FoodProduct =
    | Chips
    | Chocolate
    | Candy 

type BrandedFood =
    | Chips of string 
    | Chocolate of string 
    | Candy of string     

type FoodMachineItem =
    {
        Brand: BrandedFood
        ProductType: FoodProduct 
        Price: float 
    }
```

```fsharp
let sourCandy = {
    Brand = BrandedFood.Candy "TearDrops"
    ProductType = FoodProduct.Candy 
    Price = 2.39
}
```

Note que necesitamos especificar completamente el tipo en el componente `ProductType`, usando `FoodProduct.Candy`. Esto es para evitar la colisión con el caso `Candy of string` en el tipo BrandedFood. ¡No te preocupes! El compilador detrás lo cubrirá, señalando el problema:

```fsharp
let sourCandyWithCollision = {
    Brand = BrandedFood.Candy "TearDrops"
    ProductType = Candy 
    Price = 2.39
}
```


    input.fsx (3,19)-(3,24) typecheck error This expression was expected to have type


        'FoodProduct'    


    but here has type


        'string -> BrandedFood'    

