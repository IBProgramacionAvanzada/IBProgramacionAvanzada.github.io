---
layout: post
title: Fundamentals
categories: 
- Programming
tags:
- fsharp
---

### In preparation for take off

These guides are oriented to those progammers interested in learning some concepts on F\# functional programming, from a practical perspective. F\# is an excelent _first_ functional programming language: it is functional (of course), it has a clean and readable syntax (not a lot of fancy symbols and stuff), it is flexible (in case you need to grasp some other paradigm in the middle of your code) and it is concise enough to express your ideas with clarity. 

Learning a new language _and_ a new programming paradigm is a wonderful adventure. You do not need any special preparation, although I assume that the reader has some background in at least one popular language (let us say C, Python, Java or JavaScript, for example). Hope you enjoy your journey as much as I did when I started with F\# a few years ago. Rembember to code and make mistakes, for there is where the learning transforms. 

F\#, as every other computer language, has it own syntax and peculiarities that one needs to understand. But learning the functional paradigm requires to set aside a few concepts that one is very fond of and uses in everyday coding in imperative or object oriented languages. 

> This difference has profound roots at the very origin of computing science.
> In the beginning, there where two mutually equivalent approaches to formal computing, one developed by Alan Turing with his [computing machine](https://londmathsoc.onlinelibrary.wiley.com/doi/epdf/10.1112/plms/s2-42.1.230), while the other one was worked out by Alonzo Church with his [lambda calculus](https://www.jstor.org/stable/2371045?origin=crossref&seq=10#metadata_info_tab_contents). Both approaches evolved in parallel, and gave rise to different branches of programming styles. 
> Functional programming is by no means new. LISP, the first functional programming language appeared in 1958, just a year shy of its 
> imperative counterpart, Fortran.
> 
> In a very loose sense, learning a functional language is like travelling in time, going back through the imperative branch of  programming and taking the other exit in the roundabout.

Even though you do not finally adopt the functional paradigm, adding a new language style to your assets will give you the experience to revisit old programming habits with new eyes, giving a deeper understanding, and more fine tuning to your code. You will become a better programmer, definetly!

F\# is one of the languages provided by the .NET framework (together with C# and Visual Basic). As such, F\# is tighly integrated with all the tools and libraries that .NET provides. But in this approach, I will view .NET as the set of libraries that one can use to enhance your code. In other words, I will stay away from using them, and focusing in the core features of F\# as a functional language. From Web programming to Machine Learning, from databases to windows, .NET has everything you need to make your programming a better experience, once you know the core features F\# provides. 

Also, my aim is to keep it as simple as possible, because I would like the reader to have the fundamental set of tools from the language to play with. To that end, some concepts are left aside on purpose, and will be revealed in the future. 

> If you are a C# programmer, you will probably get a better mileage going into some of the excellent resources of F\# for C# coders, such as [Get programming with F\#](https://www.manning.com/books/get-programming-with-f-sharp#:~:text=about%20the%20book-,Get%20Programming%20with%20F%23%3A%20A%20guide%20for%20.,of%20functional%20programming%20in%20F%23.) 


## Tools

This series is about the fundamentals of F\# programming, and none of the examples need even to install F\#. You can simply code along in your web browser of your choice, heading to the [Fable REPL](https://fable.io/repl/). Just write your code in the left panel, click the usual play button (or Alt+Enter), and there you go, you are programming in F\#!.

You can also install the F\# language and use your everyday editor, but you will need to install the [.NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/7.0), available for Windows, Linux and macOS.  

Besides, this series is written as [Jupyter notebooks](https://jupyter.org/). You can grab them at [this github repository](https://github.com/fcolavecchia/fp-course). Instructions on how to set up F\# as a Notebook kernel can be found [here](https://github.com/dotnet/interactive/blob/main/docs/NotebookswithJupyter.md). 

[Visual Studio Code](https://code.visualstudio.com/) has a wonderful extension called [Ionide](https://ionide.io/Editors/Code/overview.html) to work with F\#. I personally use JetBrains Rider for my daily coding which supports F\#.

You can also use F\# with Visual Studio for Windows or MacOS, if you are familiar with that tool. 

Moreover, if you want to code F\# with `vim`, [Ionide also gets you covered](https://ionide.io/Editors/Vim/overview.html).




### `let` the fun begin 

Any code you write will need data. 

Any code you write will do _something_ with that data. 

So the first step in a new language is to learn how to define a way to hold your data, and how to transform it along the program. These concepts usually translates into _variables_ and _routines_, (or any other name, such as procedures, or functions, depending your programming scope), respectively. In this way, one uses routines to change the variables, or create other ones, from an order to buy something in a website, to the color of a pixel in your game of preference. 

But let us go back and start with _expressions_. An expression is an ordered set of symbols that may represent different entities in the code. Pretty much like a sentence in a human language, an expression has to have a valid syntax (nobody will understand me if. I use the. period wrong.) and also has to be meaningful (I a example for if me nobody of sentence sort the understand will words). The compiler, however, is much more strict with the rules and does not allow such poems. Why? Because the compiler needs to grab the expression, process it and obtain a _value_. So the value is the result of the evaluation of an expression. 

And since every expression results in a value, one needs to manage all these values that will be appearing as the code runs, identify and use them as one sees fit. To that end, F\# uses several _keywords_, and one of them is `let`. 

In F\# one says that `let` _binds_ an expression to an identifier. For example


```fsharp
// This is my first line in F#, which is a comment ¯\_(シ)_/¯
let j = 1 // an integer 
```

binds the literal expression `1` to the name `a`. On the right hand side of the `=` symbol (that acts here as a binding operator), one can have any valid expression:

```fsharp
let a = 1.0 + 3.0 // floats 
let s = "this is a string" 
let l = [1 ; 2 ; 3] // A list of integers 
let m = ("Messi",10) // A tuple of a string and a number 
let t = true // A bool 
```

and so on. Of course one can associate a previous binding with a new one through an expression:

```fsharp
let b = a + 4.0 
```

In the line above, we add four to `a` and bind the result of this expression to the `b` identifier. 

### Immutability

However, you cannot bind an expression to an identifier that has already been used:

```fsharp
let q = 3
let q = q + 1
```


    input.fsx (2,5)-(2,6) typecheck error Duplicate definition of value 'q'


which answers why one does not use the term _variable_ in F\#: once the value of an expression is obtained, it cannot be changed. In other words, all the values are _immutable_: once there, they cannot be changed. There are no variables in the language, because there is nothing to _vary_. You can create as many values as you want, but you cannot change them: 

```fsharp
let q = 3
let qq = q + 1
```

The implications of this are deep and it is very important to let the concept mature and sink in, because it percolates all the code in F\# (and any other functional language). In some way, since everything is an expression, coding in F\# is mananging the expressions that solve our problem. You name an expression (that is, you bind it to an identifier), use it in another expression, bind its value to a new identifier and so on an so on.

The perspective of a code as a long list of `let` bindings is kind of daunting. That is where functions come in.

> Checkout [the Jupyter notebook companion of this guide](https://github.com/fcolavecchia/fp-course/blob/main/en/Fundamentals.ipynb).

Next, [functions]({% post_url 2022-12-20-functions %}).
