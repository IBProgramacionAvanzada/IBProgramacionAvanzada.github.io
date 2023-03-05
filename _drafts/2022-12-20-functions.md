---
layout: post
title: Functions
categories: 
- Programming
tags:
- fsharp
---

## So what is a function anyway?


Since we are about to learn functional programming, we need to agree in what a _function_ is in this context. Let us start with a set of entities, for example, the cars parked in a given block of a street. One can identify each car by its license plate, and then build a table with two columns: the first one with the license plate, and the second one with the corresponding the color of each car:

| Lic. Plate | Color         | 
| :----------: |:-------------:|
| ABC 124 | black |
| DEF 350 | red   |
| QRZ 441 | black |
| JPG 255 | white | 

That's a function that we can call `carColor`, that associates each car of the block with its color. The table is a representation of that function. A function from a set of entities A to a set of entities B is then a relation that associates elements between the sets A and B, with the property that each and every element of A has a one and only one corresponding element of B. 

There are two things to note. First, a function is defined _from_ one set _to_ another set, in our example, from the set of license plates to the set of colors. To get the color of a particular car, you go to the table representation of the function, look up the plate in the column of license plates, and get the color from the second column. With this in mind, one can identify the _from_ set as the _input_ of the function, while the _to_ set is the _output_. 

Second, all elements of the input must relate to some element in the output set. In our example, every car in the block has a color assigned in the table. This means that there cannot be empty cells in the second column of our table. 

> ❓ Can you come up with more examples of functions in the real world? 

In math, functions are ubiquitous. Let us assume that $x$ is a real number, and define the function

$$
f(x) = x + 1, 
$$

that is, the function that returns the value $x$ plus one. The input set (which is called the _domain_ of the function in math) is the set of all real values, $\mathbb{R}$, while the output set (the _codomain_ in math) is also $\mathbb{R}$, because adding one to any real number is also another real number. Using the _arrow_ notation, the complete definition of the function would be

$$
f: \mathbb{R} \rightarrow \mathbb{R} \; ; \;  x \mapsto x + 1,
$$

that can be read as following: $f$ is a function from $\mathbb{R}$ to $\mathbb{R}$ such that $f$ of $x$ is $x + 1$".

> 🔔 A slight detour around the codomain. The codomain is the set of entities where the function can possibly map input values into. For example, in the case of the color of the cars, the codomain is simply the set of all the possible colors. In many cases that information is too general, and it is convenient to define the _range_ of the function, which is the set of actual values of outputs the function maps inputs into. The range in the cars example is the set {black, red, white}.

### Multiple inputs and outputs

Let us take the example of a vending machine. In a vending machine, products are arranged in shelves, where each shelf is named by a letter. In each shelf, the products are aligned and identified by a number. Then, in A1 you have a bag of chips, in A2 a chocolate, in B1 a soda, and so on. The machine also has a keyboard with letters and numbers for you to choose the product. To buy something, you need to give the machine some money (coins, bills, credit card, etc), select the product by clicking the letter and the number on the keyboard. The machine returns the product and some cashback, if any. 
The inputs of our `vendingMachine` function are the money, the letter and the number you selected, and the outputs are the product and 
the cashback (if any). 

An example from math could be a translation function, where given a point with coordinates \(x\) and $y$ in the plane, it returns a point with coordinates $x+1$ and $y+1$:

$$
g: \mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}  \; ; \;  (x,y) \mapsto (x + 1, y+1)
$$

or more succintly

$$
g(x,y) = (x+1,y+1)
$$

### Partial application 
When we feed a function of several input elements, we can obtain the proper output(s). But having many inputs opens a new possibility: what happens when one decides not to complete all the inputs? Let us find out. Let us assume that we entered a bill into the vending machine. It is clear that we will not get any product, because the machine still needs two more inputs: the letter of the shelf and the product number that we want. _After_ we complete these two inputs, we will get our treat (and cashback, if any). So, entering money only in the vending machine leads to a state where two inputs are needed and two outputs will be returned. But, this is _another function_!!!. Let us call it `vendingMachineAfterInsertBill` that receives the letter of the shelf and the product number that we want and returns the product (and cashback, if any). 

Going back to the math example, let us feed the function with just the $x = 3$ value, 

$$
g(3,y) = (4,y+1)
$$

Again, the result of feeding the function with one value is another function:

$$
h: \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}  \; ; \;  y \mapsto (4, y+1)
$$

or 

$$
h(y) = (4,y+1)
$$

This property of functions is called _partial application_. Whenever you do not complete all the inputs of a function, you get another function.

### Composition 
 
Finally, we look here how to work with several functions at once. Let us assume that we have a function `getFirstName` that given the full name of a person, it returns the first name (it does not matter at this point the specifics of the implementation, not even the language). For example, when applied to 'David Gilmour', it returns 'David', or when applied to 'Annie Lennox', it returns, of course 'Annie'.
We also have a function `getInitial` that for a given name, it returns the initial. In the previous cases, 'D' for 'David' and 'A' for 'Annie'. 

Now we want to build a function that gives us the initial of the first name, given the full name. For 'Paul McCartney'. We feed 'Paul McCartney' as input to the function `getFirstName`, which gives us the output 'Paul'. Now, 'Paul' is the input of the function `getInitial` and returns 'P' as the final output. 
This plumbing where the output of one function is the input of another is called _composition_. Note that it is absolutely necessary that the output of the first called function (`getFirstName`) and the input of the second one (`getInitial`) are the same kind of entity, in our case, both are first names.
You can [see this pictures for a graphical explanation](https://mathinsight.org/function_machine_composition).

Let us now look at a math example. We defined before the function $f(x)$ that adds one to $x$, for example 

$$
f(0) = 0 + 1 = 1
$$

What if we apply the $f$ function again? It means to compute

$$
f(f(0)) = f(0) + 1 = 0 + 1 + 1 = 2 
$$

In general,

$$
f(f(x)) = f(x) + 1 = x + 1 + 1 = x + 2 
$$

If we look carefully to the last expression, it looks like composing the function is like passing the function as the input itself ($f(f(x))$). This means that if we are going to have a programming language that implements function composition, in some way functions should be able to be passed as input to another functions. 

> ❗️ The fact that we use the same function to compose with itself is not relevant to this discussion, one can compose as many different functions as one wants, provided that inputs and outputs are compatible in each composition step.

> 🔔 However, composing that particular function with itself is interesting. Imaging having only the zero and this function. You can create all the natural numbers {1, 2, ...} just by composing this function with itself again and again. For example, $4 = f(f(f(f(0))))$, and so on. Therefore, given the number 0, $f(x) = x + 1$ and function composition, one can get all the natural numbers. Looks like there is something going on here. More on this, hopefully, in a future episode.


## Functions in F\# 

The F\# language implements functions in such a way that they satisfy the properties mentioned above. To define a function, the language also uses the keyword `let`:


```fsharp
let next x =
    x + 1 
```

We defined the function named `next` that receives an argument `x`. Notice that there are no other symbols or parentheses in the function definition. The body of the function should be indented, and there is no `return` keyword at the end. The function simply returns the last expression found in its body. Clean, isn't it? 
Using the function is easy as well:

```fsharp
let one = next 0 
let two = next (next 0)

printfn "one: %A" one 
printfn "two: %A" two 
```

    one: 1
    two: 2


Notice that there is no need to use parentheses around the argument when using a function. However, you need to use them when passing a more complex expression as the argument to the function, such as in the case of `two`. 

There is another way to write the computation of `two`, by using the _pipe operator_ `|>`:

```fsharp
let anotherTwo =
    0 
    |> next
    |> next 
    
printfn "anotherTwo: %A" anotherTwo
```

    anotherTwo: 2


This operator takes care of the plumbing when calling functions one after another. In the example above, the first `|>` receives `0` as the input, passes it to the next function, the second `|>` receives the output of the first `next` function and feeds it as input to the second `next`. 

Another example. Let us assume that we have the functions `getInitial` and `getFirstName` defined as:

```fsharp
let getInitial name = 
    .... //Implementation not important right now
```

and 

```fsharp
let getFirstName fullName = 
    .... //Implementation not important right now
```

and we defined the value

```fsharp
let paul = "Paul McCartney"
```

Then, 

```fsharp
paul
|> getFirstName
|> getInitial 
```
Here the string value `paul` is fed into the `getFirstName` function as the input by the first pipe `|>`, and returns 'Paul' as output. Then, the string 'Paul' is passed as the input of the function `getInitial` that gives us the 'P'. 

Composition is so important in functional languages, that it has its own symbol in F\#, `>>` :

```fsharp
let add2 = next >> next 
let two' = add2 0 
printfn "%A" two'
```

    2


Yes, you can use the `'` symbol in any identifier! (provided it is not the first character). Note also that we defined a _function_ `add2` by using the composition operator (no argument needed). This is equivalent to:

```fsharp
let add2' x = 
    x
    |> next
    |> next 
```

Remember that there is no return at the end of the function, just the last expression of the function is the return value. 

Back to the names example, to clarify the order in which functions are composed. 

```fsharp
let getInitialFromFirstName fullName =
    fullName
        |> getFirstName 
        |> getInitial 
```
and 

```fsharp
let getInitialFromFirstName' =
        getFirstName >> getInitial 
```

are equivalent. 

> ❓ Think about routines, procedures or functions that maybe you have written in your language of preference. Do they behave as F\# functions? What are the main differences you see?

> 🏋🏽 We have a function `mult2` that given a number `x` doubles that number. Without coding, can you determine what the next composite functions return when applied to 3? :

```fsharp
let f = mult2 >> next 
let g = next >> mult2 
```

Code the function `mult2` and see the result by yourself.

Some final remarks for now on functions. First, note that the language use the same keyword `let` to bind simple values and functions to a name or identifier. This emphasizes the fact that in F# functions are 'just' values, and can be treated in the same way as, say, a simpler binding of an expression to an identifier. 
Second, the properties of functions that were discussed above match perfectly inmutability. In fact, functions receive immutable inputs and return an immutable value. 

> Checkout [the Jupyter notebook companion of this guide](https://github.com/fcolavecchia/fp-course/blob/main/en/Functions.ipynb).
