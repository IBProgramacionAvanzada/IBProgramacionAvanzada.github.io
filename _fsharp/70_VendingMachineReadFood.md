---
layout: post
title: La máquina expendedora
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

Vamos a trabajar con un modelo para los items de nuestra máquina expendedora, que nos han llegado como [archivo](../data/FoodAndDrinks.csv). El modelo es el siguiente:

```fsharp
[<Measure>]
type oz 

type FoodType = 
    FoodType of string 

type FoodProduct =
    | Eat of FoodType 
    | Drink of FoodType 

type Brand = 
    | Brand of string 
 

type FoodMachineItem =
    {
        Brand: Brand
        ProductType: FoodProduct 
        Price: float 
        Weight: float<oz> 
    }
```

Algunas funciones de conveniencia:

```fsharp
let prettyPrint item = 
    let (Brand nameBrand) = item.Brand

    let foodType = 
        match item.ProductType with 
        | Eat (FoodType t) -> t 
        | Drink (FoodType t) -> t

    printfn $"{foodType} ({nameBrand}, {item.Weight} oz) costs {item.Price}$"
```

```fsharp
let readFile(fileName: string) =  
    let lines = File.ReadAllLines(fileName)
    lines 
    
```

Ahora podemos leer los datos:

```fsharp
let foodAndDrinkData = "../data/FoodAndDrinks.csv"

let products = readFile(foodAndDrinkData)

```

```fsharp
products.GetType()
```


<span><a href="https://docs.microsoft.com/dotnet/api/system.string[]?view=net-7.0">System.String[]</a></span>


```fsharp
printfn "%A" products[0]
printfn "%A" products[1]
```

    "Food,Brand,Weight/Size,Price,Drinks,Brand,Weight/Size,Price"
    "Chips,Lay's,1 oz,$1.00,Bottled Water,Aquafina,16 oz,$1.50"


```fsharp
products[0..20]
|> Seq.iteri (fun i s ->  printfn $"{i}: {s}")
```

    0: Food,Brand,Weight/Size,Price,Drinks,Brand,Weight/Size,Price
    1: Chips,Lay's,1 oz,$1.00,Bottled Water,Aquafina,16 oz,$1.50
    2: Chips,Doritos,2 oz,$2.00,Bottled Water,Dasani,20 oz,$2.00
    3: Candy Bars,Snickers,1.5 oz,$1.25,Soda,Coca-Cola,12 oz,$1.50
    4: Candy Bars,Kit Kat,1.5 oz,$1.25,Soda,Pepsi,20 oz,$2.00
    5: Granola Bars,Nature Valley,1.5 oz,$1.50,Sports Drinks,Gatorade,20 oz,$2.50
    6: Cookies,Chips Ahoy,1.5 oz,$1.25,Juice Boxes,Capri Sun,6 oz,$1.00
    7: Crackers,Cheez-Its,2 oz,$1.50,Energy Drinks,Red Bull,8.4 oz,$3.00
    8: Nuts,Planters,1 oz,$1.50,Iced Tea,Lipton,16 oz,$1.50
    9: Popcorn,Pop-Secret,2 oz,$2.00,Lemonade,Minute Maid,16 oz,$1.50
    10: Beef Jerky,Jack Link's,1 oz,$3.50,Iced Coffee,Starbucks,12 oz,$2.50
    11: Cup Noodles,Maruchan,3 oz,$1.50,Chocolate,Swiss Miss,8 oz,$2.00
    12: Pretzels,Rold Gold,1 oz,$1.00,Milk,Nesquik,8 oz,$1.00
    13: Trail Mix,Planters,1 oz,$1.50,Chocolate Milk,Yoo-hoo,8 oz,$1.00
    14: Rice Krispie Treats,Kellogg's,1.5 oz,$1.25,Fruit Smoothies,Naked Juice,16 oz,$4.00
    15: Protein Bars,Quest,2.1 oz,$3.00,,


Qué tenemos que hacer? Veamos un poco cómo nos llega el dato:

```fsharp
let items =   products[2].Split(',')
printfn "%A" items
```

    [|"Chips"; "Doritos"; "2 oz"; "$2.00"; "Bottled Water"; "Dasani"; "20 oz";
      "$2.00"|]


Esto es una secuencia (`seq`) que tiene dos items. Podemos hacer 

- una función que dado este string nos devuelva dos datos `FoodMachineItem`
- una función que transforme el peso en un tipo `float<oz>`
- una función que transforme el precio en un tipo `float`

```fsharp
let price (s:string) =
    s.Split('$')[1] |> float
    
    
price "$2.00"
```


<div class="dni-plaintext"><pre>2</pre></div><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>


```fsharp
let weight (s:string) =
    s.Split('o')[0] 
    |> float
    |> (fun v -> v*1.0<oz>)

printfn $"""{weight "20 oz"}"""
```

    20


```fsharp
let createItems (s: string) = 
    let itemList = 
        s.Split(',')
        |> Seq.toList 

    printfn "%A" itemList 
    let eat = 
        {
            Brand = Brand (itemList[1])
            ProductType = Eat (FoodType itemList[0]) 
            Price = price itemList[3]
            Weight = weight itemList[2]
        }
    let drink = 
        {
            Brand = Brand (itemList[5])
            ProductType = Eat (FoodType itemList[4]) 
            Price = price itemList[7]
            Weight = weight itemList[6]
        }

    (eat,drink)        
            


```

```fsharp
let items =   products[2].Split(',')
printfn "%A" items
createItems products[2]
```

    [|"Chips"; "Doritos"; "2 oz"; "$2.00"; "Bottled Water"; "Dasani"; "20 oz";
      "$2.00"|]
    ["Chips"; "Doritos"; "2 oz"; "$2.00"; "Bottled Water"; "Dasani"; "20 oz";
     "$2.00"]



<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>({ Brand = Brand "Doritos"\n  ProductType = Eat (FoodType "Chips")\n  Price = 2.0\n  Weight = 2.0 }, { Brand = Brand "Dasani"\n  ProductType = Eat (FoodType "Bottled Water")\n  Price = 2.0\n  Weight = 20.0 })</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Brand = Brand "Doritos"\n  ProductType = Eat (FoodType "Chips")\n  Price = 2.0\n  Weight = 2.0 }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Brand</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Brand "Doritos"</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td>Doritos</td></tr></tbody></table></div></details></td></tr><tr><td>ProductType</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Eat (FoodType "Chips")</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>FoodType "Chips"</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td>Chips</td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>Price</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr><tr><td>Weight</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>{ Brand = Brand "Dasani"\n  ProductType = Eat (FoodType "Bottled Water")\n  Price = 2.0\n  Weight = 20.0 }</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Brand</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Brand "Dasani"</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td>Dasani</td></tr></tbody></table></div></details></td></tr><tr><td>ProductType</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Eat (FoodType "Bottled Water")</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>FoodType "Bottled Water"</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item</td><td>Bottled Water</td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>Price</td><td><div class="dni-plaintext"><pre>2</pre></div></td></tr><tr><td>Weight</td><td><div class="dni-plaintext"><pre>20</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>


```fsharp
products[1..]
|> Seq.map (fun p -> createItems p)
```

    ["Chips"; "Lay's"; "1 oz"; "$1.00"; "Bottled Water"; "Aquafina"; "16 oz";
     "$1.50"]
    ["Chips"; "Doritos"; "2 oz"; "$2.00"; "Bottled Water"; "Dasani"; "20 oz";
     "$2.00"]
    ["Candy Bars"; "Snickers"; "1.5 oz"; "$1.25"; "Soda"; "Coca-Cola"; "12 oz";
     "$1.50"]
    ["Candy Bars"; "Kit Kat"; "1.5 oz"; "$1.25"; "Soda"; "Pepsi"; "20 oz"; "$2.00"]
    ["Granola Bars"; "Nature Valley"; "1.5 oz"; "$1.50"; "Sports Drinks"; "Gatorade";
     "20 oz"; "$2.50"]
    ["Cookies"; "Chips Ahoy"; "1.5 oz"; "$1.25"; "Juice Boxes"; "Capri Sun"; "6 oz";
     "$1.00"]
    ["Crackers"; "Cheez-Its"; "2 oz"; "$1.50"; "Energy Drinks"; "Red Bull"; "8.4 oz";
     "$3.00"]
    ["Nuts"; "Planters"; "1 oz"; "$1.50"; "Iced Tea"; "Lipton"; "16 oz"; "$1.50"]
    ["Popcorn"; "Pop-Secret"; "2 oz"; "$2.00"; "Lemonade"; "Minute Maid"; "16 oz";
     "$1.50"]
    ["Beef Jerky"; "Jack Link's"; "1 oz"; "$3.50"; "Iced Coffee"; "Starbucks";
     "12 oz"; "$2.50"]
    ["Cup Noodles"; "Maruchan"; "3 oz"; "$1.50"; "Chocolate"; "Swiss Miss"; "8 oz";
     "$2.00"]
    ["Pretzels"; "Rold Gold"; "1 oz"; "$1.00"; "Milk"; "Nesquik"; "8 oz"; "$1.00"]
    ["Trail Mix"; "Planters"; "1 oz"; "$1.50"; "Chocolate Milk"; "Yoo-hoo"; "8 oz";
     "$1.00"]
    ["Rice Krispie Treats"; "Kellogg's"; "1.5 oz"; "$1.25"; "Fruit Smoothies";
     "Naked Juice"; "16 oz"; "$4.00"]
    ["Protein Bars"; "Quest"; "2.1 oz"; "$3.00"; ""; ""]



    System.ArgumentException: The index was outside the range of elements in the list. (Parameter 'n')


       at Microsoft.FSharp.Collections.PrivateListHelpers.nth[a](FSharpList`1 l, Int32 n) in D:\a\_work\1\s\src\FSharp.Core\prim-types.fs:line 4026


       at FSI_0015.createItems(String s)


       at FSI_0017.it@2-1.Invoke(String p)


       at Microsoft.FSharp.Collections.Internal.IEnumerator.map@99.DoMoveNext(b& curr) in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 105


       at Microsoft.FSharp.Collections.Internal.IEnumerator.MapEnumerator`1.System.Collections.IEnumerator.MoveNext() in D:\a\_work\1\s\src\FSharp.Core\seq.fs:line 88


       at System.Linq.Enumerable.SelectIterator[TSource,TResult](IEnumerable`1 source, Func`3 selector)+MoveNext()


       at Microsoft.DotNet.Interactive.Formatting.EnumerableExtensions.TakeAndCountRemaining[T](IEnumerable`1 source, Int32 count, Boolean forceCountRemainder) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\EnumerableExtensions.cs:line 23


       at Microsoft.DotNet.Interactive.Formatting.HtmlFormatter`1.<>c__DisplayClass7_0.<CreateTableFormatterForAnyEnumerable>g__BuildTable|4(T source, FormatContext context, Boolean summarize) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\HtmlFormatter{T}.cs:line 99


       at Microsoft.DotNet.Interactive.Formatting.HtmlFormatter`1.<>c__DisplayClass7_0.<CreateTableFormatterForAnyEnumerable>b__3(T value, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\HtmlFormatter{T}.cs:line 83


       at Microsoft.DotNet.Interactive.Formatting.HtmlFormatter`1.Format(T value, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\HtmlFormatter{T}.cs:line 53


       at Microsoft.DotNet.Interactive.Formatting.TypeFormatter`1.Microsoft.DotNet.Interactive.Formatting.ITypeFormatter.Format(Object instance, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\TypeFormatter{T}.cs:line 23


       at Microsoft.DotNet.Interactive.Formatting.HtmlFormatter.<>c.<.cctor>b__0_12(IEnumerable value, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\HtmlFormatter.cs:line 198


       at Microsoft.DotNet.Interactive.Formatting.HtmlFormatter`1.Format(T value, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\HtmlFormatter{T}.cs:line 53


       at Microsoft.DotNet.Interactive.Formatting.TypeFormatter`1.Microsoft.DotNet.Interactive.Formatting.ITypeFormatter.Format(Object instance, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\TypeFormatter{T}.cs:line 23


       at Microsoft.DotNet.Interactive.Formatting.Formatter.<>c__DisplayClass53_0.<TryInferPreferredFormatter>b__4(Object value, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\Formatter.cs:line 702


       at Microsoft.DotNet.Interactive.Formatting.AnonymousTypeFormatter`1.Format(T instance, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\AnonymousTypeFormatter{T}.cs:line 30


       at Microsoft.DotNet.Interactive.Formatting.TypeFormatter`1.Microsoft.DotNet.Interactive.Formatting.ITypeFormatter.Format(Object instance, FormatContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\TypeFormatter{T}.cs:line 23


       at Microsoft.DotNet.Interactive.Formatting.Formatter`1.FormatTo(T obj, FormatContext context, String mimeType) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\Formatter{T}.cs:line 79


       at Microsoft.DotNet.Interactive.Formatting.Formatter.FormatTo[T](T obj, FormatContext context, String mimeType) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\Formatter.cs:line 308


       at Microsoft.DotNet.Interactive.Formatting.Formatter.ToDisplayString(Object obj, String mimeType) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.Formatting\Formatter.cs:line 265


       at Microsoft.DotNet.Interactive.FormattedValue.<>c__DisplayClass7_0.<FromObject>b__0(String mimeType) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive\FormattedValue.cs:line 37


       at System.Linq.Enumerable.SelectArrayIterator`2.ToArray()


       at Microsoft.DotNet.Interactive.FormattedValue.FromObject(Object value, String[] mimeTypes) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive\FormattedValue.cs:line 36


       at <StartupCode$Microsoft-DotNet-Interactive-FSharp>.$FSharpKernel.clo@196-5.MoveNext() in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive.FSharp\FSharpKernel.fs:line 223


       at Microsoft.DotNet.Interactive.Kernel.HandleAsync(KernelCommand command, KernelInvocationContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive\Kernel.cs:line 325


       at Microsoft.DotNet.Interactive.KernelCommandPipeline.<BuildPipeline>b__6_0(KernelCommand command, KernelInvocationContext context, KernelPipelineContinuation _) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive\KernelCommandPipeline.cs:line 60


       at Microsoft.DotNet.Interactive.KernelCommandPipeline.SendAsync(KernelCommand command, KernelInvocationContext context) in D:\a\_work\1\s\src\Microsoft.DotNet.Interactive\KernelCommandPipeline.cs:line 41


### Tratando con datos que no están

La forma que F# nos ofrece para tratar con datos que no existen es el tipo `option`: 

```fsharp
type Option<'a> =       
   | Some of 'a           // valid value
   | None                 // missing
```

```fsharp
let emptyStringAsOption (s:string) = 

    if (String.IsNullOrEmpty(s)) then 
        None 
    else 
        Some s

printfn "%A" (emptyStringAsOption "hola")
printfn "%A" (emptyStringAsOption "")        
```

    Some "hola"
    None


El tipo `option` tiene métodos similares a los del tipo `Result`

```fsharp
let tryParseInt (s:string) = 
    if (s |> Seq.forall Char.IsDigit) then 
        Some (int s)
    else 
        None 

printfn "%A" (tryParseInt "43")

printfn "%A" (tryParseInt "Messi")

```

    Some 43
    None


```fsharp
let divisionBy2 s = 
    s
    |> tryParseInt
    |> Option.map (fun n -> n/2)


```

```fsharp
printfn "%A" (divisionBy2 "43")   
printfn "%A" (divisionBy2 "Messi") 
```

    Some 21
    None


Luego de esta introducción, volvamos a mirar nuestros datos. El problema lo tenemos en la última línea:

```fsharp
products[15]
```


    Protein Bars,Quest,2.1 oz,$3.00,,


```fsharp
products[15].Split(',')
```


<div class="dni-plaintext"><pre>[ Protein Bars, Quest, 2.1 oz, $3.00, ,  ]</pre></div><style>
.dni-code-hint {
    font-style: italic;
    overflow: hidden;
    white-space: nowrap;
}
.dni-treeview {
    white-space: nowrap;
}
.dni-treeview td {
    vertical-align: top;
    text-align: start;
}
details.dni-treeview {
    padding-left: 1em;
}
table td {
    text-align: start;
}
table tr { 
    vertical-align: top; 
    margin: 0em 0px;
}
table tr td pre 
{ 
    vertical-align: top !important; 
    margin: 0em 0px !important;
} 
table th {
    text-align: start;
}
</style>


```fsharp
let createItemsRobust (s: string) = 
    let itemList = 
        s.Split(',')
        |> Seq.toList 

    // printfn "%A" itemList 
    let eat = 
        {
            Brand = Brand (itemList[1])
            ProductType = Eat (FoodType itemList[0]) 
            Price = price itemList[3]
            Weight = weight itemList[2]
        }        

    let drink = 
        if (String.IsNullOrEmpty(itemList[4])) then 
            None 
        else 
            {
                Brand = Brand (itemList[5])
                ProductType = Eat (FoodType itemList[4]) 
                Price = price itemList[7]
                Weight = weight itemList[6]
            } |> Some 

    (eat,drink)        
            


```

```fsharp
let mixedSeq = 
    products[1..]
    |> Seq.map (fun p -> createItemsRobust p)

mixedSeq     
|> Seq.iter (fun i -> printfn "%A" i)
```

    ({ Brand = Brand "Lay's"
       ProductType = Eat (FoodType "Chips")
       Price = 1.0
       Weight = 1.0 }, Some { Brand = Brand "Aquafina"
                              ProductType = Eat (FoodType "Bottled Water")
                              Price = 1.5
                              Weight = 16.0 })
    ({ Brand = Brand "Doritos"
       ProductType = Eat (FoodType "Chips")
       Price = 2.0
       Weight = 2.0 }, Some { Brand = Brand "Dasani"
                              ProductType = Eat (FoodType "Bottled Water")
                              Price = 2.0
                              Weight = 20.0 })
    ({ Brand = Brand "Snickers"
       ProductType = Eat (FoodType "Candy Bars")
       Price = 1.25
       Weight = 1.5 }, Some { Brand = Brand "Coca-Cola"
                              ProductType = Eat (FoodType "Soda")
                              Price = 1.5
                              Weight = 12.0 })
    ({ Brand = Brand "Kit Kat"
       ProductType = Eat (FoodType "Candy Bars")
       Price = 1.25
       Weight = 1.5 }, Some { Brand = Brand "Pepsi"
                              ProductType = Eat (FoodType "Soda")
                              Price = 2.0
                              Weight = 20.0 })
    ({ Brand = Brand "Nature Valley"
       ProductType = Eat (FoodType "Granola Bars")
       Price = 1.5
       Weight = 1.5 }, Some { Brand = Brand "Gatorade"
                              ProductType = Eat (FoodType "Sports Drinks")
                              Price = 2.5
                              Weight = 20.0 })
    ({ Brand = Brand "Chips Ahoy"
       ProductType = Eat (FoodType "Cookies")
       Price = 1.25
       Weight = 1.5 }, Some { Brand = Brand "Capri Sun"
                              ProductType = Eat (FoodType "Juice Boxes")
                              Price = 1.0
                              Weight = 6.0 })
    ({ Brand = Brand "Cheez-Its"
       ProductType = Eat (FoodType "Crackers")
       Price = 1.5
       Weight = 2.0 }, Some { Brand = Brand "Red Bull"
                              ProductType = Eat (FoodType "Energy Drinks")
                              Price = 3.0
                              Weight = 8.4 })
    ({ Brand = Brand "Planters"
       ProductType = Eat (FoodType "Nuts")
       Price = 1.5
       Weight = 1.0 }, Some { Brand = Brand "Lipton"
                              ProductType = Eat (FoodType "Iced Tea")
                              Price = 1.5
                              Weight = 16.0 })
    ({ Brand = Brand "Pop-Secret"
       ProductType = Eat (FoodType "Popcorn")
       Price = 2.0
       Weight = 2.0 }, Some { Brand = Brand "Minute Maid"
                              ProductType = Eat (FoodType "Lemonade")
                              Price = 1.5
                              Weight = 16.0 })
    ({ Brand = Brand "Jack Link's"
       ProductType = Eat (FoodType "Beef Jerky")
       Price = 3.5
       Weight = 1.0 }, Some { Brand = Brand "Starbucks"
                              ProductType = Eat (FoodType "Iced Coffee")
                              Price = 2.5
                              Weight = 12.0 })
    ({ Brand = Brand "Maruchan"
       ProductType = Eat (FoodType "Cup Noodles")
       Price = 1.5
       Weight = 3.0 }, Some { Brand = Brand "Swiss Miss"
                              ProductType = Eat (FoodType "Chocolate")
                              Price = 2.0
                              Weight = 8.0 })
    ({ Brand = Brand "Rold Gold"
       ProductType = Eat (FoodType "Pretzels")
       Price = 1.0
       Weight = 1.0 }, Some { Brand = Brand "Nesquik"
                              ProductType = Eat (FoodType "Milk")
                              Price = 1.0
                              Weight = 8.0 })
    ({ Brand = Brand "Planters"
       ProductType = Eat (FoodType "Trail Mix")
       Price = 1.5
       Weight = 1.0 }, Some { Brand = Brand "Yoo-hoo"
                              ProductType = Eat (FoodType "Chocolate Milk")
                              Price = 1.0
                              Weight = 8.0 })
    ({ Brand = Brand "Kellogg's"
       ProductType = Eat (FoodType "Rice Krispie Treats")
       Price = 1.25
       Weight = 1.5 }, Some { Brand = Brand "Naked Juice"
                              ProductType = Eat (FoodType "Fruit Smoothies")
                              Price = 4.0
                              Weight = 16.0 })
    ({ Brand = Brand "Quest"
       ProductType = Eat (FoodType "Protein Bars")
       Price = 3.0
       Weight = 2.1 }, None)


```fsharp
let eats = 
    mixedSeq
    |> Seq.map fst 

let drinks = 
    mixedSeq
    |> Seq.map snd 
    |> Seq.choose id 

let all = 
    seq { 
        yield! eats
        yield! drinks 
    }

```

```fsharp
all
|> Seq.iter prettyPrint
```

    Chips (Lay's, 1 oz) costs 1$
    Chips (Doritos, 2 oz) costs 2$
    Candy Bars (Snickers, 1,5 oz) costs 1,25$
    Candy Bars (Kit Kat, 1,5 oz) costs 1,25$
    Granola Bars (Nature Valley, 1,5 oz) costs 1,5$
    Cookies (Chips Ahoy, 1,5 oz) costs 1,25$
    Crackers (Cheez-Its, 2 oz) costs 1,5$
    Nuts (Planters, 1 oz) costs 1,5$
    Popcorn (Pop-Secret, 2 oz) costs 2$
    Beef Jerky (Jack Link's, 1 oz) costs 3,5$
    Cup Noodles (Maruchan, 3 oz) costs 1,5$
    Pretzels (Rold Gold, 1 oz) costs 1$
    Trail Mix (Planters, 1 oz) costs 1,5$
    Rice Krispie Treats (Kellogg's, 1,5 oz) costs 1,25$
    Protein Bars (Quest, 2,1 oz) costs 3$
    Bottled Water (Aquafina, 16 oz) costs 1,5$
    Bottled Water (Dasani, 20 oz) costs 2$
    Soda (Coca-Cola, 12 oz) costs 1,5$
    Soda (Pepsi, 20 oz) costs 2$
    Sports Drinks (Gatorade, 20 oz) costs 2,5$
    Juice Boxes (Capri Sun, 6 oz) costs 1$
    Energy Drinks (Red Bull, 8,4 oz) costs 3$
    Iced Tea (Lipton, 16 oz) costs 1,5$
    Lemonade (Minute Maid, 16 oz) costs 1,5$
    Iced Coffee (Starbucks, 12 oz) costs 2,5$
    Chocolate (Swiss Miss, 8 oz) costs 2$
    Milk (Nesquik, 8 oz) costs 1$
    Chocolate Milk (Yoo-hoo, 8 oz) costs 1$
    Fruit Smoothies (Naked Juice, 16 oz) costs 4$

