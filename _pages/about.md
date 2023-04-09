---
title: About
author: Flavio Colavecchia
date: 2022-02-04
category: Programming
layout: post
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
