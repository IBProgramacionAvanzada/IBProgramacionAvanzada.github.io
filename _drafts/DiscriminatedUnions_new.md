---
layout: post
title: Discriminated unions
tagline:  Dealing with entities that belong to well predefined group of cases.
categories: 
- F# as your first functional programming language
tags:
- fsharp
---


## Discriminated unions

One of the key aspects of F\# (and other functional languages) is that it provides a specific syntax to model those characteristics of inputs and outputs that belong to a well predefined collection. These are called _discriminated unions_. 

The type for our food `vendingMachine` would be:

```fsharp
type FoodProduct =
    | Chips
    | Chocolate
    | Candy 
```

while for the electronics we could have:

```fsharp
type Electronics = 
    | Phones
    | Speakers
    | Headphones
```

> 🔔 It is customary to use `PascalCase` for types (i.e., `FoodProduct`).

> ❗️ Case types in a discriminated union should start with an uppercase letter (`Phones`, `Chips`, etc.).

In the expressions above we are defining a type with a name (`FoodProduct`, `Electronics`) that can have several  case types. It is important to stress the fact that the cases of each discriminated union type are _disjoint_, that is, can not be accessed at the same time. For example:

```fsharp
let d = FoodProduct.Chips
let s = Electronics.Speakers
```

The languages uses the dot `.` to represent a case of a discriminated union. The value `s` represents of course an speaker, and since it is inmutable, there is no possible way that can be a phone or a headphone. 

So how can we define functions with discriminated unions? Let us write a function `price` that receives an input of type `Electronics` and gives us the price:

```fsharp
let price electronic = 
    match electronic with 
    | Phones -> 435
    | Speakers -> 29
    | Headphones -> 122

printfn "The price of speaker is: %A $" (price s)    
```

    The price of speaker is: 29 $


The way one can disaggregate the different cases of an input that is a discriminated union is through _pattern matching_, represented by the construct `match ... with` and then, all the cases. The syntax is pretty straightforward: for each discriminated union case label (after the `|` sign in the construct) the function returns the price as an `int`. 

Note also that the match should contain _all_ the possible cases of the discriminated union. If one is forgetting some case, the compiler will tell us about with some wiggled underlining at the match. This means that the patter matching is _exhaustive_. 

Another very important aspect of the pattern matching is that is evaluated _in the order_ is written. 

But, what if we want to write down a `priceFood` function, and asign a 1.5 price to all the items except Chocolates, that are tagged at a 2.35 price? The language introduces the _wildcard_ symbol that matches _any_ input in the pattern matching construct. The wildcard is represented by the `_` (single underscore) symbol:

```fsharp
let priceFood food = 
    match food with 
    | Chocolate -> 2.35
    | _ -> 1.5 

printfn "Chocolate: %A $" (priceFood FoodProduct.Chocolate)        
printfn "Chips: %A $" (priceFood FoodProduct.Chips)        
printfn "Candy: %A $" (priceFood FoodProduct.Candy)        
```

    Chocolate: 2.35 $
    Chips: 1.5 $
    Candy: 1.5 $


Here we see the interplay between the order evaluation of the pattern matching and the wildcard. We are returning a specific values for _some cases_ (Chocolate), and asign a common value for _the rest of the cases_. When a food `value` is received by `priceFood` it is compared with the `Chocolate` case first. If it is not chocolate, then is get captured by the wildcard pattern. Consider the following example:

```fsharp
let priceSale food = 
    match food with 
    | _ -> 1.5 
    | Chocolate -> 2.35

printfn "Chocolate: %A $" (priceSale FoodProduct.Chocolate)        
printfn "Chips: %A $" (priceSale FoodProduct.Chips)        
printfn "Candy: %A $" (priceSale FoodProduct.Candy)        
```

    Chocolate: 1.5 $
    Chips: 1.5 $
    Candy: 1.5 $


Well, everything is "on sale" at 1.5! That is because the wildcard captures _any input_ and since it is the first _case_, any food will be at that price. 

Here again, the compiler behind scenes comes to our rescue. It will let you know that some of the pattern matching rules will not be reached:

<img src="img/rule_will_never_match.png" alt="This will save you from bankruptcy" width="400"/>




### Combining discriminated unions and basic types

In many cases, the discriminated union construct is not general enough, so one can combine it with basic types. Let us say that we want to identify the brand of each of our items in the vending machines. Since it can accomodate multiple brands of products, we decide that we represent the brand by a `string`. We can expand our `FoodProduct` discriminated union type as:


```fsharp
type BrandedFood =
    | Chips of string 
    | Chocolate of string 
    | Candy of string 
```

Each of the cases of the `BrandedFood` type has now a _value_ of type `string`. The discriminated union makes use of the keyword `of` to associate each case with each value type. One can define identifiers for this compound type as:

```fsharp
let belovedChocolate = BrandedFood.Chocolate "Wonka"
let healthyChips = BrandedFood.Chips "NotALeis"
let sourCandy = BrandedFood.Candy "TearDrops"
```

In the brand example, we choose to combine all the cases with the same basic type, `string`. But, again, one can mix and match. For example, a chocolate can come in different presentations:

```fsharp
type ChocolatePresentation =
| Bar of float  // a chocolate Bar of a given weight
| Box of int // a package with a number of chocolate pieces    
```

Or, if we want to model the change money the vending machine returns to the customer, we can define a case where an actual amount is returned, and another one that represents the fact that the client just put the exact money into the machine:

```fsharp
type Change =
| Amount of float
| NoChange
```

Pattern matching in functions against these compound discriminated unions is again fairly simple. To get the brand of a `BrandedFood` product, we can define the function `brand`:

```fsharp
let brand product = 
    match product with
    | Chips p -> p 
    | Chocolate p -> p 
    | Candy p -> p  
```

```fsharp
printfn "Brand of belovedChocolate: %s" (brand belovedChocolate)
```

    Brand of belovedChocolate: Wonka


In this function, each case has an associated value represented by the identifier `p`, that is _unwrapped_ from the discriminated union, and returned. 

Another example:

```fsharp
let changeValue change =
    match change with 
    | Amount money -> money
    | NoChange -> 0 

let c = Amount 3 

printfn "You are receiving %A $ as change" (changeValue c)
```

    You are receiving 3.0 $ as change


> Note on how a discriminated union that mix basic types seems to present a kind of divergence in code. The type `Change` combines a wrapped `float` type (with `Amount`) and a pure union type `NoChange`. Since functions need to have specific input and output types, any function that receives a `Change` input has to return a defined output. There usually two possibilities. The first one is that the function flattens  the inputs (such as in the case of `changeValue`), where one gets the float value of the money the user receives. The second one corresponds to a function that  promotes the input types to another one: for example, the trivial case would be a function that prints the amount of money the customer receives, `printChange: Change -> ()`, that is, receives a `Change` and returns `unit`. 
> This is one of the key ideas behind functional programming, being able to connect different types through functions.


### Protecting inputs with single discriminated unions

The last, but not least, use case of discriminated unions correspond to those ones that only have one term. Let us say that we need to describe the models of the different items in our electronics vending machine. One way to do it is using a _single_ discriminated union:


```fsharp
type Model =
    | Model of string
```

Then, we can define different models of items:

```fsharp
let yPhone = Model "Xtreme 3S"
let miniSpeakers = Model "Louder Pro"
```

Since the discriminated union has only one case, there is a short way to unwrap the value inside it:

```fsharp
let (Model yPhoneModel) = yPhone
printfn "The model of the yPhone is %A" yPhoneModel
```

    The model of the yPhone is "Xtreme 3S"


Looks like there is a lot of pomp in this types, why just not use a simpler `string` instead? The answer is, again, related with functions. Let us define the `printModel` function as:

```fsharp
let printModel (model: Model) =
    let (Model value) = model 
    printfn "The model is %A" value 
```

```fsharp
printModel yPhone
printModel miniSpeakers
```

    The model is "Xtreme 3S"
    The model is "Louder Pro"


The signature of the `printModel` function is `Model -> ()`, meaning that receives a Model value and returns unit. Since the input is a `Model`, there is no possible way that we can pass a plain string to it. In this way, one protects the input of the function such that it has to be the precise type we decided, and nothing else. 

It should be pointed out that this is not a validation, that is, one still can mix up the meaning of `Model` by building it with any string, the single piece of code that is the function `printModel` is guarded by the single discriminated union type. Then, this type does not replace validation, but gives you a cleaner code.


### Wrapping up

The discriminated union type in both variants (single and multiple) is a key aspect of the functional programming in F\#. The ability to describe well predefined collections with a single type simplifies codes and makes them robust. The exhaustive pattern matching specification (monitored by the F\# compiler behind scenes) makes sure that the programmer takes care of all cases of the discriminated union, and not a single case is left behind, with possible holes in the code. 

The discriminated union type is the way F\# represents a _sum type_, as this kind of types are called in algebraic type theory. 


