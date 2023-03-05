---
layout: post
title:  Records
tagline: Dealing with entities that do not belong to well predefined group of cases.
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

We presented before the need to aggregate useful information of some entities that cannot be restricted to have some specific types, for example, the data of a credit card. 

Pretty much any high level language provides a way to define such types. In C language one has the `struct` type, in Python a class without methods (or a [named tuple](https://docs.python.org/3/library/collections.html#collections.namedtuple)), etc. This type in F\# is called a _record_. 

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

The record uses curly braces to aggregate the different components of the type. Each component has a label (`HoldersName`, `Number`, etc.) and a type associated with it. 
To create a record, one needs to define all and every component:

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

> 🔔 Note the suffix `uy` for unsigned integers of 8 bits (`uint8`) and `us` for their 16 bits partner (`uint16`).

Instead of indenting the definition, one can write all the components together, separated by `;`:

```fsharp
let doeFakeCard = { HoldersName = "John Doe"; Number = "1234 5678 9101 1121"; ExpirationDateMonth = 12uy; ExpirationDateYear = 23uy ; CVV = 111us}

printfn "%A" doeFakeCard    
```

    { HoldersName = "John Doe"
      Number = "1234 5678 9101 1121"
      ExpirationDateMonth = 12uy
      ExpirationDateYear = 23uy
      CVV = 111us }


but as one can sees that this is suitable [only for small records](https://learn.microsoft.com/en-us/dotnet/fsharp/style-guide/formatting#formatting-record-expressions).

What happens if John Doe's card expires and needs to be replaced by a new one? 
As with all other values in the language, records are _inmutable_, so it is not possible to update `doeCard` in place. To do that, we need to create another, new value. F\# provides a way to _copy and update_ a record value, that enables us to change just the components that need to be changed in a record. Assuming that the new card keeps the number (and, of course, the cardholder's name), we would have:

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


One uses again curly braces to express the record type, then the old value `doeCard` that will be updated `with` the components that need to be updated. 

To access a specific component of a record, one uses again the `.`, as we did with discriminated unions:

```fsharp
printfn "John's Does card number: %A" newDoeCard.Number 
printfn "John's Does card CVV: %A" newDoeCard.CVV
```

    John's Does card number: "1234 5678 9101 1121"
    John's Does card CVV: 222us


## Mixing types 

Discriminated union and record are the two ways one can represent entities in the language. One can build all sort of complex types by mixing them, it is up to the programmer how to combine this smaller bricks to model the domain. 

For example, one can put together the expiration date in its own type:

```fsharp
type ExpirationDate =
    { 
        Month : uint8
        Year : uint8 
    }
```

That would give us a cleaner `CreditCard2` type:

```fsharp
type CreditCard2 =
    {
        HoldersName : string
        Number: string
        ExpirationDate: ExpirationDate 
        CVV: uint16
    }
```

For the vending machine, one can write

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

Notice that we need to specify completely the type in the `ProductType` component, by using `FoodProduct.Candy`. This is to avoid the collision with the case `Candy of string` in the BrandedFood type. Do not worry! The compiler behind will get you covered, signaling the problem:

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


And from here on, the sky is the limit.
