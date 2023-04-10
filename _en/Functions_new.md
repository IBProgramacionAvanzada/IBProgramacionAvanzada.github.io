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


## Qu'est-ce qu'une fonction ?



Puisque nous sommes sur le point d'apprendre la programmation fonctionnelle, nous devons nous mettre d'accord sur ce qu'est une _fonction_ dans ce contexte. Commençons par un ensemble d'entités, par exemple, les voitures garées dans un bloc donné d'une rue. On peut identifier chaque voiture par sa plaque d'immatriculation, puis construire un tableau à deux colonnes : la première avec la plaque d'immatriculation, et la seconde avec la couleur correspondante de chaque voiture :

non défini Lic. Plaque undefined Couleur undefined
non défini :---------- : non défini :---------------------- : non défini
indéfini ABC 124 indéfini noir indéfini
indéfini DEF 350 indéfini rouge indéfini
undefined QRZ 441 undefined noir undefined
indéfini JPG 255 indéfini blanc indéfini

C'est une fonction que nous pouvons appeler `carColor`, qui associe chaque voiture du bloc à sa couleur. Le tableau est une représentation de cette fonction. Une fonction d'un ensemble d'entités A vers un ensemble d'entités B est alors une relation qui associe des éléments entre les ensembles A et B, avec la propriété que chaque élément de A a un et un seul élément correspondant de B.

Il y a deux choses à noter. Premièrement, une fonction est définie _de_ un ensemble _à_ un autre ensemble, dans notre exemple, de l'ensemble des plaques d'immatriculation à l'ensemble des couleurs. Pour obtenir la couleur d'une voiture particulière, vous accédez à la représentation du tableau de la fonction, recherchez la plaque dans la colonne des plaques d'immatriculation et obtenez la couleur de la deuxième colonne. Dans cet esprit, on peut identifier l'ensemble _from_ comme l'_entrée_ de la fonction, tandis que l'ensemble _to_ est la _sortie_.

Deuxièmement, tous les éléments de l'entrée doivent être liés à un élément de l'ensemble de sortie. Dans notre exemple, chaque voiture du bloc a une couleur attribuée dans le tableau. Cela signifie qu'il ne peut pas y avoir de cellules vides dans la deuxième colonne de notre tableau.

> ❓ Pouvez-vous proposer d'autres exemples de fonctions dans le monde réel ?

En mathématiques, les fonctions sont omniprésentes. Supposons que $x$ est un nombre réel et définissons la fonction

$$
f(x) = x + 1, 
$$

c'est-à-dire la fonction qui renvoie la valeur $x$ plus un. L'ensemble d'entrée (qui est appelé le _domaine_ de la fonction en mathématiques) est l'ensemble de toutes les valeurs réelles, $\mathbb{R}$, tandis que l'ensemble de sortie (le _codomaine_ en mathématiques) est également $\mathbb{R}$ , car l'ajout d'un à n'importe quel nombre réel est également un autre nombre réel. En utilisant la notation _flèche_, la définition complète de la fonction serait

$$
f: \mathbb{R} \rightarrow \mathbb{R} \; ; \;  x \mapsto x + 1,
$$

qui peut être lu comme suit : $f$ est une fonction de $\mathbb{R}$ à $\mathbb{R}$ telle que $f$ de $x$ est $x + 1$".

> 🔔 Un léger détour autour du codomaine. Le codomaine est l'ensemble d'entités dans lesquelles la fonction peut éventuellement mapper des valeurs d'entrée. Par exemple, dans le cas de la couleur des voitures, le codomaine est simplement l'ensemble de toutes les couleurs possibles. Dans de nombreux cas, ces informations sont trop générales et il est pratique de définir la _plage_ de la fonction, qui est l'ensemble des valeurs réelles des sorties dans lesquelles la fonction mappe les entrées. La gamme dans l'exemple des voitures est l'ensemble {noir, rouge, blanc}.

### Entrées et sorties multiples

Prenons l'exemple d'un distributeur automatique. Dans un distributeur automatique, les produits sont disposés dans des étagères, chaque étagère étant désignée par une lettre. Dans chaque étagère, les produits sont alignés et identifiés par un numéro. Ensuite, en A1 vous avez un sachet de chips, en A2 un chocolat, en B1 un soda, etc. La machine dispose également d'un clavier avec des lettres et des chiffres pour que vous puissiez choisir le produit. Pour acheter quelque chose, vous devez donner de l'argent à la machine (pièces de monnaie, billets, carte de crédit, etc.), sélectionnez le produit en cliquant sur la lettre et le chiffre sur le clavier. La machine renvoie le produit et une remise en argent, le cas échéant.
Les entrées de notre fonction "vendingMachine" sont l'argent, la lettre et le numéro que vous avez sélectionnés, et les sorties sont le produit et
le cashback (le cas échéant).

Un exemple mathématique pourrait être une fonction de traduction, où étant donné un point avec les coordonnées \(x\) et $y$ dans le plan, elle renvoie un point avec les coordonnées $x+1$ et $y+1$ :

$$
g: \mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}  \; ; \;  (x,y) \mapsto (x + 1, y+1)
$$

ou plus succinctement

$$
g(x,y) = (x+1,y+1)
$$

### Application partielle
Lorsque nous alimentons une fonction de plusieurs éléments d'entrée, nous pouvons obtenir la ou les sorties appropriées. Mais avoir de nombreuses entrées ouvre une nouvelle possibilité : que se passe-t-il quand on décide de ne pas compléter toutes les entrées ? Découvrons-le. Supposons que nous ayons entré une facture dans le distributeur automatique. Il est clair que nous n'obtiendrons aucun produit, car la machine a encore besoin de deux entrées supplémentaires : la lettre de l'étagère et le numéro de produit que nous voulons. _Après_ nous aurons terminé ces deux entrées, nous obtiendrons notre friandise (et notre cashback, le cas échéant). Ainsi, entrer de l'argent uniquement dans le distributeur automatique conduit à un état où deux entrées sont nécessaires et deux sorties seront retournées. Mais, c'est _une autre fonction_!!!. Appelons-le `vendingMachineAfterInsertBill` qui reçoit la lettre de l'étagère et le numéro de produit que nous voulons et renvoie le produit (et le cashback, le cas échéant).

Pour en revenir à l'exemple mathématique, alimentons la fonction avec juste la valeur $x = 3$,

$$
g(3,y) = (4,y+1)
$$

Encore une fois, le résultat de l'alimentation de la fonction avec une valeur est une autre fonction :

$$
h: \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}  \; ; \;  y \mapsto (4, y+1)
$$

ou

$$
h(y) = (4,y+1)
$$

Cette propriété des fonctions est appelée _application partielle_. Chaque fois que vous ne remplissez pas toutes les entrées d'une fonction, vous obtenez une autre fonction.

### Composition
 
Enfin, nous regardons ici comment travailler avec plusieurs fonctions à la fois. Supposons que nous ayons une fonction `getFirstName` qui, étant donné le nom complet d'une personne, renvoie le prénom (peu importe à ce stade les spécificités de l'implémentation, pas même la langue). Par exemple, lorsqu'il est appliqué à 'David Gilmour', il renvoie 'David', ou lorsqu'il est appliqué à 'Annie Lennox', il renvoie, bien sûr, 'Annie'.
Nous avons également une fonction `getInitial` qui, pour un nom donné, renvoie l'initiale. Dans les cas précédents, 'D' pour 'David' et 'A' pour 'Annie'.

Maintenant, nous voulons construire une fonction qui nous donne l'initiale du prénom, étant donné le nom complet. Pour 'Paul McCartney'. Nous alimentons 'Paul McCartney' en entrée de la fonction `getFirstName`, qui nous donne la sortie 'Paul'. Maintenant, 'Paul' est l'entrée de la fonction `getInitial` et renvoie 'P' comme sortie finale.
Cette plomberie où la sortie d'une fonction est l'entrée d'une autre s'appelle _composition_. Notez qu'il est absolument nécessaire que la sortie de la première fonction appelée (`getFirstName`) et l'entrée de la seconde (`getInitial`) soient le même type d'entité, dans notre cas, les deux sont des prénoms.
Vous pouvez [voir ces images pour une explication graphique](https://mathinsight.org/function_machine_composition).

Regardons maintenant un exemple mathématique. Nous avons défini avant la fonction $f(x)$ qui ajoute un à $x$, par exemple

$$
f(0) = 0 + 1 = 1
$$

Et si nous appliquions à nouveau la fonction $f$ ? Cela signifie calculer

$$
f(f(0)) = f(0) + 1 = 0 + 1 + 1 = 2 
$$

En général,

$$
f(f(x)) = f(x) + 1 = x + 1 + 1 = x + 2 
$$

Si nous regardons attentivement la dernière expression, il semble que composer la fonction revient à passer la fonction comme entrée elle-même ($f(f(x))$). Cela signifie que si nous allons avoir un langage de programmation qui implémente la composition de fonctions, d'une certaine manière, les fonctions devraient pouvoir être transmises en entrée à d'autres fonctions.

> ❗️ Le fait qu'on utilise la même fonction pour composer avec elle-même n'est pas pertinent dans cette discussion, on peut composer autant de fonctions différentes que l'on veut, à condition que les entrées et les sorties soient compatibles à chaque étape de composition.

> 🔔 Cependant, composer cette fonction particulière avec elle-même est intéressant. Imagerie n'ayant que le zéro et cette fonction. Vous pouvez créer tous les nombres naturels {1, 2, ...} simplement en composant cette fonction avec elle-même encore et encore. Par exemple, $4 = f(f(f(f(0))))$, etc. Par conséquent, étant donné le nombre 0, $f(x) = x + 1$ et la composition de la fonction, on peut obtenir tous les nombres naturels. On dirait qu'il se passe quelque chose ici. Plus d'informations à ce sujet, espérons-le, dans un prochain épisode.

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

