---
layout: post
title: What is F# 
date: 2023-01-21
categories: 
- F# as your first functional programming language
tags:
- fsharp
---



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


 



