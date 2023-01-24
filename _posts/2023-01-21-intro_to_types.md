---
layout: post
title: Types
categories: 
- Programming
tags:
- fsharp
---


To completely define a function, you not only need to establish its behavior, but also its inputs and outputs. That is what _types_ are for! 

## Thinking with types

We have covered the basics of functions in F\#, and defined some properties that difference them from routines or procedures in other languages. We have also seen how one can combine functions together. That is the first aspect about defining functions: how they behave, and how one can encapsulate this behavior in one or several functions that can be composed to generate results. In our example, the behavior of our `vendingMachine` is to give you some product once you provided enough money and selected your treat with a keyboard. 
The second aspect that is _needed_ to define the function is what sort of inputs and outputs it has. Going back to our first example, one had a table that represented the function `carColor`, with the color of each car in parked in a block. A completely different function would be `carHorsePower`, where for each car in the block, you write down the horsepower. Clearly here the inputs of both functions are the same, the license plate, but the outputs are different. If our vending machine does not take paper bills but credit cards, the input will be different, but you still get your beloved chips, that is, the outputs of `vendingMachine` and the modern `vendingMachineWithCreditCard` are of the same kind, but the inputs are different.

Then, to completely define a function, you need to specify (code...) the behavior of the function _and_ the types of inputs and outputs. These are two sides of the same coin, and one cannot live without the other. And that is where the type of inputs and a outputs of the real world need to be properly represented by your code. And that is where the language provides you with the tools to define these types. 

How to define the kind of inputs and outputs of your functions? To start, typically one writes down a bullet list of all the relevant characteristics of them. In the case of colors of the cars in the block, the inputs are the license plates that usually are letters and numbers, so in our code model, that would be some kind of string. How about the outputs? One can use the usual color names, as we did. That would be represented by a collection of strings.

> Or, one can go for more detail and use the names of the colors [each car model has](https://www.caranddriver.com/features/g28196287/wildest-paint-colors-for-sale/). That would be a more complete collection of colors, but this also would affect the inputs of our function, because we need now also the name of the car model. Going further, the name of the color probably changes with the years model, so we would need that too. 

> ❓ Can you come up with the inputs and outputs of a function `carModel`? This function identifies the model of a car in the block. Start with a minimal description, and then go into more details. 

> ❓ Take the real world examples of functions that you wrote down in the previous chapter, and try to define their inputs and outputs.


For our vending machine with bills, our inputs are going to be the number of bills of each denomination  introduced in the machine, and a letter and a number to determine which product the customer wants:

- Number of bills of each denomination
- A letter
- A number 

 The outputs are possible the products, that can be minimallistically modeled by saying

- The product brand
- The product type (chocolate, chips, etc.)

and also a number of bills if the machine gives change.

If we have a vending machine with credit cards, our input will be slightly different, since we need to model a credit card with

- Carholder's name
- Card number
- Expiration date
- CVV number 

A _type_ is the tool the computer languages provides to group or aggregate the properties of the model of any entity of our domain. As we all know, computers only understand only 0s and 1s bits at the lowest level, however, they are presented typically as chunks _bytes_ of 8 bits each. Putting bytes together one can have the basic types of all languages, such as integer and float numbers in all of their variations (signed or unsigned integers, integers of different size, float numbers of different sizes). Other basic types, such as strings or chars, even booleans, depend on the way each language implements them. 

All contemporary languages can build compound types using these basic types. For example, if we need to model a wallet, we will need a way to represent money bills, credit cards, personal id, etc. Each of these elements in turn needs also to be modeled, and so on. Therefore, the wallet will be represented as a hierarchical aggregation of simpler models that, at the very bottom of it, will resort to basic types. 

There is also another aspect of modelling the real world entities with types to look at. Let us go back to our examples and examine how we can map each of the properties of our models into simpler types. Clearly the cardholder's name of a credit card can be a string, the expiration date is, well, a date. 
How about the type of the product? In principle can also be a string too. However, it seems that one can be more specific: for a food vending machine, one can make a bullet list of the possible food types:

- chips
- chocolate
- candy

and so on. There are also electronics vending machines, so one would have

- phone
- speakers
- headphones
etc. 

The bullet list can be long, but probably one can write it down in a page (or, maybe it is provided by your vending machine client). 

It looks like one can classify the features of inputs and outputs more broadly. On one hand, the cardholder's name of a credit card and expiration dates are huge collections that can not be determined _a priori_. On the other hand, we have some features that belong to a well predefined collection (car colors, types of products). Notice also that these two kind of types can be compound together in inputs or outputs, like in the wallet model. 

One of the greatest features of F\# is that provides precise ways to implement these two kind of types. In the next sections we will see how to build these inputs and outputs in F\#.

By the way, the keyword that F\# uses to build complex types is, of course, `type` ;-D. 


## Annotating types and type inference

We have lightly defined some values before: 

```fsharp
let a = 1.0 + 3.0 // floats 
let s = "this is a string" 
let l = [1 ; 2 ; 3] // A list of integers 
let m = ("Messi",10) // A tuple of a string and a number 
let t = true // A bool 
```

which, except for the use of the `let` keyword (and the fact that they are inmutable!), can look quite familiar to, for example, Python: one uses the name of the value, the equal sign and the expression that will be assigned to the value in the right hand side. There is no declaration of _which_ type the value is! But we already stated that the language uses static types, so how the language knows which type each value has? 

If you hover your mouse over each of the values above, you can see something like this:

<img src="/img/type_inference.png" alt="Type inference" width="400"/>


That box contains the type of the value `a` that has been _inferred_ by the compiler. Yes, when you code in a F\# supported IDE, there will be an F\# compiler digesting your code as you write it, and exploring which types are you working with. In the vast majority of the cases, the compiler guess is correct. There are some cases where it cannot infer the type from your code's context. In that case, you will see some red wiggles underlining your variable, and a warning message:

<img src="/img/indeterminate_lookup.png" alt="Indeterminate lookup" width="600"/>


that says exactly that: the compiler does not know which type needs to assign to the troubled value.

So, in general, one does not explicitly define the types of the values, and that task is delegated to the compiler behind scenes. However, one can annotate the type as: 

```fsharp
let aa: float = 1.0 + 3.0 // floats 
let ss: string = "this is a string" 
let ll: int list = [1 ; 2 ; 3] // A list of integers 
let mm: string * int = ("Messi",10) // A tuple of a string and a number 
let tt: bool = true // A bool 
```

One uses the `:<type>` annotation to define the types of each value. In the case of functions one also uses `:<type>` just after defining all the inputs:

```fsharp
let sum a b : int = 
    a + b 
```

A closer look at the function above leads us to another interesting notation. Hovering over `sum` we see:

<img src="/img/function_types.png" alt="Function types" width="400"/>

The compiler says us that the function `sum` is of type 
```fsharp
int -> int -> int 
```
that is, it receives two `int`s as input and returns an `int` as output. We only needed to explicitly type the output of the function, and the compiler determined that both inputs are `int`s. That is because the sum operator `+` needs two values of the _same_ type, and there is no implicit promotion of types in the language:

```fsharp
let si = 2 + 3 
let sf = 2 + 3.0 
```

    input.fsx (2,12)-(2,13) typecheck error The type 'float' does not match the type 'int'


> 🔔 Looking at the signature  `int -> int -> int` of the function `sum` it does not seem clear which are the inputs and which are the outputs. From the definition of the function we know that it receives two inputs and returns another one, but the signature is completely oblivious to this, and uses the same `->` symbol between all the types. Could `sum` be a function that receives an `int` and returns `int -> int`, which is itself a function? The answer is yes, and is called _currying_. 
We will come back later to currying when we revisit functions.

One can fully define the types of the input variables together with the output:

```fsharp
let sumf (a:float) (b:float) : float = 
    a + b 
```

The parenthesis are used to associate each input value with its type. One does not need to annotate all the inputs of a function, just those ones that confuse the compiler or your fellow developer, or just you in the future. 

### The `unit` type and first look to side effects

The list of all basic types in F\# can be found [here](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/basic-types). Beside the usual `bool`, `char`, `string` and all the various numeric numbers, there is one that is called `unit`. 

The `unit` type has only one possible value `()`, and is used to represent that there is no type. 

Although it may seem obscure at first, the language provides this feature to deal, for example, with those things that happen in the code that do not return anything. For example, the function `printfn`:

```fsharp
let q = printfn "I am printfn "
printfn "and I do return unit: %A" q
```

    I am printfn 
    and I do return unit: ()


Here we assign the `printfn` result into the value `q`, and then we print its value, which is, `()`. Note that the function `printfn` does print in the console, pretty much as any print-like function in most languages. 

Also, one can have a function that does not have any input:

```fsharp
let ohMy () =
    "ohMy"    
```

The function `ohMy` just defines the string "ohMy", and does not need to receive anything as an input. 

In general, the `unit` type is a typical clue that we are dealing with some _side effect_. For example, printing something in the console is _not_ the output of the `printfn` function, but a side effect: the function does something that alters what is happening in the console. Your code is minding its own bussiness, but at some point, you decided to show some information on the console. But your code is not the console program, it has nothing to do with it, except that it _can_ write things in the console. That is a side effect: something your code does that does not belong to the realm the code is working in. 

All the data that your code produces will be in the form of some side effect: printing in the console, saving in a database, creating a file, sending an email, etc. Also, all the data your code uses comes from somewhere: a file, the input of the user in a keyboard, a url, etc. Any useful program will result in some side effect. Therefore, even though functional programming is all about functions, at some point it needs to have tools to receive or return data from the world. Those are _desired_ side effects, probably written in some form of specification of your application. 

What functional programming does is to give you all the tools to prevent _undesired_ side effects in your code, but to produce _desired_ side effects when are needed. For example, a particular function in your code will receive some inmutable inputs and produce some new outputs. Then, if one restricts the attention to that function, anything outside the world of it remains unchanged, but a new value (the output) is created. 

In non-functional languages, the most common undesired side effect is to be able to change input values in a function. In most cases, these languages have the ability to pass arguments to a function as a reference (a pointer in C, for example) so one can change the values of it, which in turn will change the value of the argument variable. Or one can change the value of a global variable within a function. This makes debugging much harder, and code much difficult to mantain. 


> Checkout [the Jupyter notebook companion of this guide](https://github.com/fcolavecchia/fp-course/blob/main/en/IntroToTypes.ipynb).


