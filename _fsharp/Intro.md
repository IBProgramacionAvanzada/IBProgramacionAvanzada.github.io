---
layout: post
title: My road to F#  
date: 2023-01-21
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

<!-- ## My road to F#  -->

Long ago I heard about functional programming. 

For years I have been using C and Fortran to code algorithms and functions to calculate stuff as fast as possible, transforming mathematical and physical models into computations. In this domain, that some people call High  Performance Computing, the goal is to be fast and accurate, and to use the computational resources available (cpu/gpu, memory and bandwidth) in the most efficient way to get results. Those resources were so limited, that one needed to exploit every possible optimization path to speed up your code. The physical realm I had to deal with was mostly cast into large linear algebra systems that need to be solved over and over again. There was no time nor intention to use other languages, because much of the precedent work (that is, libraries) were written in C or Fortran. 

Then I moved on into medical image processing, developing software in Python. A switch into a completely different domain, where you need to select the proper packages, master how to integrate them in a coherent way, and have your image nicely done and ready for consumption by health professionals. A complete different beast, more object oriented coding, but still fighting the bug.

Just a few years ago, I had the chance to search for the functional way of programming. I visited Haskell, jumped through OCaml, got away from the sea of parentheses of Clojure, got excited with Elm, and landed softly in the world of F#.  

If you has the chance to select the language to code with (which certainly is not a general case, since most of the time one ends up speaking the language of the team or business or enterprise one works in), then you know that this election has many faces. It could be opportunity,  the need to jump into a different domain or it could be simply the desire to learn something new. In my case, I wanted to learn a functional language and _also_ be able to use it to learn how to develop web applications. 

And, I did not know _anything_ about .NET.  

First thing first, [.NET](https://dotnet.microsoft.com/en-us/) is the framework to program almost everything (there is probably the reason of the word _universal_  comes to F#) created originally by Microsoft, and lately open sourced. 

So there were some bad news: At the time I started learning, there were not only one .NET, but many .NET versions ([Microsoft .NET History](https://time.graphics/pt/line/593132)).

![.NET history line](/img/dotnet.png)

Moreover, my perception was that F# was viewed as the functional sibling of C# , the .NET object oriented language; and there was around this idea of ‘evolution’ in the programming paradigm style: from imperative, then object oriented, to end in a functional one. So I stumbled upon many great resources that were targeted to C# programmers, which I definitely wasn’t. 
This was just fine: one would probably be more successful inviting C# programmers to jump into the F# wagon than, for example, Java or Python ones, not to mention solo structured programmers like myself.

Nowadays the landscape is completely different. As you can see in the picture, there is now only one .NET _to rule them all_ (and with the advent of the next version .NET 7, there will be more integration among the different parts of the ecosystem). Moreover, you do not even need to install _anything_ to start writing F#! [Just jump into the web and you are good to go](https://fable.io/repl/) .

<!-- In the following sections, I dive into F#, why use it, where to start from, and finally, a list of resources that _I_ found useful in this journey.  -->

Please remember that learning a new language (moreover, _learning_) is a very personal experience, pick your own ingredients for your recipe.


## What is F#
[F# ](https://dotnet.microsoft.com/en-us/languages/fsharp) (also written as fsharp when the numeral sign is not welcomed) is a language created in [Microsoft](https://docs.microsoft.com/en-us/dotnet/fsharp/) by [Don Syme](@dsymetweets). It was described as a _functional-first_ programming language, albeit in the last iteration, the tag line was changed to _a universal programming language for writing succinct, robust and performant code_.  As many other languages (such as Java), it was build originally as a corporate endeavor inside Microsoft, but fortunately became [open sourced](https://github.com/dotnet/fsharp),  with the great benefit that there is a large ecosystem of tools and libraries built over .NET to choose from.   You can read a the history of the language [here](https://fsharp.org/history/hopl-final/hopl-fsharp.pdf).

## Why F# 

Here are some features that F# exhibits as _a language_:

### It is a multi paradigm language

When coding in F# you will be presented with a functional approach _first_, that can be easily extended to include object oriented and/or imperative ones. You can mix and match paradigms according to the specific needs of your code.  

### F# is a strongly typed language

This means that one needs to know in advance what kind of value one is saving in each expression (Note that I am _not_ talking about _variables_. More on that later.). _However_, the usual experience when programming in F\# with a modern IDE is that your are not coding alone, the compiler is your friend and it continuously surveys what you are writing, and decides whether or not the proper type definition is explicitly needed. In the vast majority of lines of code you will not need to set the type of data and it is properly inferred, but it is very helpful to do so for your team coworkers or your future you.

### F# is a compiled language

You will be dealing with the usual edit-build-test-run loop. For details on how the compilation process works within .NET, check this [link](https://docs.microsoft.com/en-us/dotnet/standard/clr).  _But_, the language comes with console where [you can code interactively](https://learn.microsoft.com/en-us/dotnet/fsharp/tools/fsharp-interactive/). Moreover, if you are used to script code in a [notebook](https://jupyter.org/), you can attach an F\# kernel and use it! 

Here are some of the benefits that you get because _F# is part of the .NET_:

- It is fully cross-platform.
- .NET is a vast library.
- It is easy to integrate with other .NET languages, such as C# or VB.
- It is open source.
- There are great tools available to develop in F#.
- It has a huge and friendly community.


 



