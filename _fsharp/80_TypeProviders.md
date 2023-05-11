---
layout: post
title: Leyendo datos estructurados
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---
Continuando con la lectura de archivos de datos estructurados, a veces no es posible (o es mucho trabajo) hacer el _parsing_ de los datos. Veamos un ejemplo en que esto ocurre:

```fsharp
let readFile(fileName: string) =  
    let lines = File.ReadAllLines(fileName)
    lines 
    
```

En este caso, tomamos datos de un archivo con canciones de los Beatles

```fsharp
let beatlesFile = "../data/The Beatles songs dataset.csv"

let songs = readFile(beatlesFile)
```

```fsharp
songs.GetType()
```


<span><a href="https://docs.microsoft.com/dotnet/api/system.string[]?view=net-7.0">System.String[]</a></span>


```fsharp
printfn "%A" songs[0]
printfn "%A" songs[1]
```

    "Title,Year,Album.debut,Duration,Other.releases,Genre,Songwriter,Lead.vocal,Top.50.Billboard"
    "12-Bar Original,1965,Anthology 2,174,0,Blues,"Lennon, McCartney, Harrison and Starkey",,-1"


```fsharp
songs[0..20]
|> Seq.iteri  (fun i s->  printfn $"{i}: {s}")
```

    0: Title,Year,Album.debut,Duration,Other.releases,Genre,Songwriter,Lead.vocal,Top.50.Billboard
    1: 12-Bar Original,1965,Anthology 2,174,0,Blues,"Lennon, McCartney, Harrison and Starkey",,-1
    2: A Day in the Life,1967,Sgt. Pepper's Lonely Hearts Club Band,335,12,"Psychedelic Rock, Art Rock, Pop/Rock",Lennon and McCartney,Lennon and McCartney,-1
    3: A Hard Day's Night,1964,UK: A Hard Day's Night US: 1962-1966,152,35,"Rock, Electronic, Pop/Rock",Lennon,"Lennon, with McCartney",8
    4: A Shot of Rhythm and Blues,1963,Live at the BBC,104,0,"R&B, Pop/Rock",Thompson,Lennon,-1
    5: A Taste of Honey,1963,UK: Please Please Me US: The Early Beatles,163,29,"Pop/Rock, Jazz, Stage&Screen","Scott, Marlow",McCartney,-1
    6: Across the Universe,1968,Let It Be,230,19,"Psychedelic folk, Pop/Rock",Lennon,Lennon,-1
    7: Act Naturally,1965,UK: Help! US: Yesterday and Today,139,14,"Country, Pop/Rock","Russell, Morrison",Starkey,50
    8: Ain't She Sweet,1961,Anthology 1,150,9,Pop/Rock,"Yellen, Ager",Lennon,41
    9: All I've Got to Do,1963,UK: With the Beatles US: Meet The Beatles!,124,9,Pop/Rock,Lennon,Lennon,-1
    10: All My Loving,1963,UK: With the Beatles US: Meet The Beatles!,124,32,Pop/Rock,McCartney,McCartney,-1
    11: All Things Must Pass,1969,Anthology 3,227,0,"Folk Rock, Pop/Rock",Harrison,Harrison,-1
    12: All Together Now,1967,Yellow Submarine,130,8,"Skiffle, Pop/Rock","McCartney, with Lennon","McCartney, with Lennon",-1
    13: All You Need Is Love,1967,Magical Mystery Tour,237,25,Pop/Rock,Lennon,Lennon,15
    14: And I Love Her,1964,UK: A Hard Day's Night US: Something New,152,29,Pop/Rock,"McCartney, with Lennon",McCartney,37
    15: And Your Bird Can Sing,1966,UK: Revolver US: Yesterday and Today,121,9,"Power Pop, Psychedelic Pop, Pop/Rock","Lennon, with McCartney",Lennon,-1
    16: Anna (Go to Him),1963,UK: Please Please Me US: The Early Beatles,168,16,"Soul, Pop/Rock",Alexander,Lennon,-1
    17: Another Girl,1965,Help!,124,9,"Country Rock, Pop/Rock",McCartney,McCartney,-1
    18: Any Time at All,1964,UK: A Hard Day's Night US: Something New,133,15,Pop/Rock,"Lennon, with McCartney","Lennon, with McCartney",-1
    19: Ask Me Why,1962,UK: Please Please Me US: The Early Beatles,144,24,Pop/Rock,"Lennon, with McCartney",Lennon,-1
    20: Baby It's You,1963,UK: Please Please Me US: The Early Beatles,162,17,Pop/Rock,"Bacharach, David, Dixon",Lennon,-1


```fsharp
let song =   songs[2].Split('"')
printfn "%A" song
```

    [|"A Day in the Life,1967,Sgt. Pepper's Lonely Hearts Club Band,335,12,";
      "Psychedelic Rock, Art Rock, Pop/Rock";
      ",Lennon and McCartney,Lennon and McCartney,-1"|]


```fsharp
song.Length
```


<div class="dni-plaintext"><pre>3</pre></div><style>
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


Uno puede consultar otros _parsers_, que los hay por doquier, por ejemplo [en esta página](https://www.joelverhagen.com/blog/2020/12/fastest-net-csv-parsers), pero, gracias al tipo de dato estático, existe una módulo que nos puede resolver el problema, a través de _type providers_.

### Type Providers

Un _type provider_  es una biblioteca que nos permite lidiar con tipos particulares datos:

- [CSV type provider](http://fsprojects.github.io/FSharp.Data/library/CsvProvider.html).
- [Html type provider](https://fsprojects.github.io/FSharp.Data/library/HtmlProvider.html).
- [Json type provider](https://fsprojects.github.io/FSharp.Data/library/JsonProvider.html).

son algunos ejemplos. 

La biblioteca `FSharp.Data` es la que usaremos para aprender a leer estos tipos de datos. En un notebook se importa de la siguiente manera:

```fsharp
#r "nuget: FSharp.Data"

open FSharp.Data
```


<div><div></div><div></div><div><strong>Installed Packages</strong><ul><li><span>FSharp.Data, 6.2.0</span></li></ul></div></div>


Un _type provider_ genera un tipo de dato a partir de la información que lee desde un archivo. Esto ocurre en el momento de la compilación. Al momento de ejecutar el código, el tipo que se creó puede utilizarse para procesar los datos

```fsharp
type SongsTypeProvider = FSharp.Data.CsvProvider<"../data/The Beatles songs dataset.csv", HasHeaders=true>
```

El compilador (y la biblioteca `FSharp.Data`) construyen el _type provider_ utilizando el archivo "../data/The Beatles songs dataset.csv" como plantilla, descubriendo la estructura de los datos. 

Se puede obtener los datos propiamente dichos con:

```fsharp
let songs = SongsTypeProvider.GetSample()
```

De este modo, usamos el mismo archivo para crear el tipo y para obtener los datos. Sin embargo, se podría usar dos archivos diferentes, uno como plantilla y otro con los datos. En ese caso, llamamos al método  `.Load`: 

```fsharp
type SongsTypeProvider = FSharp.Data.CsvProvider<"myTemplateDataFile.csv", HasHeaders=true>
let songs = SongsTypeProvider.Load("myRealDataFile.csv")
```


Al crear el tipo de dato, el _type provider_ crea los campos para poder acceder a la información, por ejemplo

```fsharp
songs.Headers 
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(System.String[])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>[ Title, Year, Album.debut, Duration, Other.releases, Genre, Songwriter, Lead.vocal, Top.50.Billboard ]</pre></div></td></tr></tbody></table></div></details><style>
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


nos da los encabezados de cada columna de los datos. Los datos propiamente dichos los encontramos en el campo `.Rows`: 

```fsharp
songs.Rows
|> Seq.take 20 
|> Seq.iteri  (fun i s ->  printfn $"{i}: {s}")
```

    0: (12-Bar Original, 1965, Anthology 2, 174, 0, Blues, Lennon, McCartney, Harrison and Starkey, , -1)
    1: (A Day in the Life, 1967, Sgt. Pepper's Lonely Hearts Club Band, 335, 12, Psychedelic Rock, Art Rock, Pop/Rock, Lennon and McCartney, Lennon and McCartney, -1)
    2: (A Hard Day's Night, 1964, UK: A Hard Day's Night US: 1962-1966, 152, 35, Rock, Electronic, Pop/Rock, Lennon, Lennon, with McCartney, 8)
    3: (A Shot of Rhythm and Blues, 1963, Live at the BBC, 104, 0, R&B, Pop/Rock, Thompson, Lennon, -1)
    4: (A Taste of Honey, 1963, UK: Please Please Me US: The Early Beatles, 163, 29, Pop/Rock, Jazz, Stage&Screen, Scott, Marlow, McCartney, -1)
    5: (Across the Universe, 1968, Let It Be, 230, 19, Psychedelic folk, Pop/Rock, Lennon, Lennon, -1)
    6: (Act Naturally, 1965, UK: Help! US: Yesterday and Today, 139, 14, Country, Pop/Rock, Russell, Morrison, Starkey, 50)
    7: (Ain't She Sweet, 1961, Anthology 1, 150, 9, Pop/Rock, Yellen, Ager, Lennon, 41)
    8: (All I've Got to Do, 1963, UK: With the Beatles US: Meet The Beatles!, 124, 9, Pop/Rock, Lennon, Lennon, -1)
    9: (All My Loving, 1963, UK: With the Beatles US: Meet The Beatles!, 124, 32, Pop/Rock, McCartney, McCartney, -1)
    10: (All Things Must Pass, 1969, Anthology 3, 227, 0, Folk Rock, Pop/Rock, Harrison, Harrison, -1)
    11: (All Together Now, 1967, Yellow Submarine, 130, 8, Skiffle, Pop/Rock, McCartney, with Lennon, McCartney, with Lennon, -1)
    12: (All You Need Is Love, 1967, Magical Mystery Tour, 237, 25, Pop/Rock, Lennon, Lennon, 15)
    13: (And I Love Her, 1964, UK: A Hard Day's Night US: Something New, 152, 29, Pop/Rock, McCartney, with Lennon, McCartney, 37)
    14: (And Your Bird Can Sing, 1966, UK: Revolver US: Yesterday and Today, 121, 9, Power Pop, Psychedelic Pop, Pop/Rock, Lennon, with McCartney, Lennon, -1)
    15: (Anna (Go to Him), 1963, UK: Please Please Me US: The Early Beatles, 168, 16, Soul, Pop/Rock, Alexander, Lennon, -1)
    16: (Another Girl, 1965, Help!, 124, 9, Country Rock, Pop/Rock, McCartney, McCartney, -1)
    17: (Any Time at All, 1964, UK: A Hard Day's Night US: Something New, 133, 15, Pop/Rock, Lennon, with McCartney, Lennon, with McCartney, -1)
    18: (Ask Me Why, 1962, UK: Please Please Me US: The Early Beatles, 144, 24, Pop/Rock, Lennon, with McCartney, Lennon, -1)
    19: (Baby It's You, 1963, UK: Please Please Me US: The Early Beatles, 162, 17, Pop/Rock, Bacharach, David, Dixon, Lennon, -1)


A partir de los encabezados de las columnas, el _type provider_ construye los campos correspondientes a cada dato:

```fsharp
songs.Rows
|> Seq.take 20 
|> Seq.iteri  (fun i s ->  printfn $"{i}: {s.Title} by {s.Songwriter}")
```

    0: 12-Bar Original by Lennon, McCartney, Harrison and Starkey
    1: A Day in the Life by Lennon and McCartney
    2: A Hard Day's Night by Lennon
    3: A Shot of Rhythm and Blues by Thompson
    4: A Taste of Honey by Scott, Marlow
    5: Across the Universe by Lennon
    6: Act Naturally by Russell, Morrison
    7: Ain't She Sweet by Yellen, Ager
    8: All I've Got to Do by Lennon
    9: All My Loving by McCartney
    10: All Things Must Pass by Harrison
    11: All Together Now by McCartney, with Lennon
    12: All You Need Is Love by Lennon
    13: And I Love Her by McCartney, with Lennon
    14: And Your Bird Can Sing by Lennon, with McCartney
    15: Anna (Go to Him) by Alexander
    16: Another Girl by McCartney
    17: Any Time at All by Lennon, with McCartney
    18: Ask Me Why by Lennon, with McCartney
    19: Baby It's You by Bacharach, David, Dixon


```fsharp
songs.Rows
|> Seq.take 20 
|> Seq.iteri  (fun i s ->  printfn $"{i}: {s.Title} by >{s.``Lead.vocal``}<")
```

    0: 12-Bar Original by ><
    1: A Day in the Life by >Lennon and McCartney<
    2: A Hard Day's Night by >Lennon, with McCartney<
    3: A Shot of Rhythm and Blues by >Lennon<
    4: A Taste of Honey by >McCartney<
    5: Across the Universe by >Lennon<
    6: Act Naturally by >Starkey<
    7: Ain't She Sweet by >Lennon<
    8: All I've Got to Do by >Lennon<
    9: All My Loving by >McCartney<
    10: All Things Must Pass by >Harrison<
    11: All Together Now by >McCartney, with Lennon<
    12: All You Need Is Love by >Lennon<
    13: And I Love Her by >McCartney<
    14: And Your Bird Can Sing by >Lennon<
    15: Anna (Go to Him) by >Lennon<
    16: Another Girl by >McCartney<
    17: Any Time at All by >Lennon, with McCartney<
    18: Ask Me Why by >Lennon<
    19: Baby It's You by >Lennon<


```fsharp
songs.Rows 
|> Seq.filter (fun r -> r.``Top.50.Billboard``=1)
|> Seq.iter (fun r -> printfn $"Name:{r.Title} position {r.``Top.50.Billboard``}")

```

    Name:Hey Jude position 1


#### Eligiendo separadores

Se pueden especificar los separadores al momento de crear el tipo:
```fsharp
CsvProvider<"../data/AirQuality.csv", Separators=";,">
```

### Datos que faltan

El _type provider_ tiene [ciertas reglas para tratar con datos que faltan](https://fsprojects.github.io/FSharp.Data/library/CsvProvider.html#Controlling-the-column-types). Por ejemplo, si el dato que se espera en alguna columna es un número, pero el archivo contiene `NaN`, al crear el dato el _type provider lo reportará como `Double.NaN`. 

Por otro lado, [podemos especificar qué `strings` queremos que se conviertan a `Nan`](https://fsprojects.github.io/FSharp.Data/library/CsvProvider.html#Missing-values):

```fsharp 
CsvProvider<"X,Y,Z\nthis,that,1.0", MissingValues="this,that">
    .GetSample()
    .Rows
```

Además, si preferimos no utilizar las reglas del _type provider_, usamos `PreferOptionals=true` para que genere tipos `option` en el caso 
de datos faltantes:


```fsharp
type SongsTypeProviderOpt = FSharp.Data.CsvProvider<"../data/The Beatles songs dataset.csv", HasHeaders=true, PreferOptionals=true>

let songsWithOpt = SongsTypeProviderOpt.GetSample()

songsWithOpt.Rows
|> Seq.take 5
|> Seq.iteri  (fun i s ->  printfn $"{i}: {s}")
```

    0: (12-Bar Original, 1965, Some(Anthology 2), 174, 0, Some(Blues), Lennon, McCartney, Harrison and Starkey, , -1)
    1: (A Day in the Life, 1967, Some(Sgt. Pepper's Lonely Hearts Club Band), 335, 12, Some(Psychedelic Rock, Art Rock, Pop/Rock), Lennon and McCartney, Some(Lennon and McCartney), -1)
    2: (A Hard Day's Night, 1964, Some(UK: A Hard Day's Night US: 1962-1966), 152, 35, Some(Rock, Electronic, Pop/Rock), Lennon, Some(Lennon, with McCartney), 8)
    3: (A Shot of Rhythm and Blues, 1963, Some(Live at the BBC), 104, 0, Some(R&B, Pop/Rock), Thompson, Some(Lennon), -1)
    4: (A Taste of Honey, 1963, Some(UK: Please Please Me US: The Early Beatles), 163, 29, Some(Pop/Rock, Jazz, Stage&Screen), Scott, Marlow, Some(McCartney), -1)


### Html Provider

También podemos obtener los datos de una página web usando un Html provider:

```fsharp
[<Literal>]
let url = """https://en.wikipedia.org/wiki/List_of_songs_recorded_by_the_Beatles"""

type WebSongsTypeProvider = FSharp.Data.HtmlProvider<url>


let songs = WebSongsTypeProvider.Load("https://en.wikipedia.org/wiki/List_of_songs_recorded_by_the_Beatles")
```

```fsharp
songs.Tables.``Main songsEdit 3``.Rows
|> Seq.map (fun r -> r.Song, r.Year)
|> Seq.filter (fun (s,y) -> y=1968)
|> Seq.iter (fun (s,y) -> printfn $"Name:%s{s} Year:{y}")

```

    Name:"Back in the U.S.S.R." Year:1968
    Name:"Birthday" Year:1968
    Name:"Blackbird" Year:1968
    Name:"The Continuing Story of Bungalow Bill" Year:1968
    Name:"Cry Baby Cry" Year:1968
    Name:"Dear Prudence" Year:1968
    Name:"Don't Pass Me By" Year:1968
    Name:"Everybody's Got Something to Hide Except Me and My Monkey" Year:1968
    Name:"Glass Onion" Year:1968
    Name:"Good Night" Year:1968
    Name:"Happiness Is a Warm Gun" Year:1968
    Name:"Helter Skelter" Year:1968
    Name:"Hey Jude" # Year:1968
    Name:"Honey Pie" Year:1968
    Name:"I Will" Year:1968
    Name:"I'm So Tired" Year:1968
    Name:"The Inner Light" # Year:1968
    Name:"Julia" Year:1968
    Name:"Lady Madonna" # Year:1968
    Name:"Long, Long, Long" Year:1968
    Name:"Martha My Dear" Year:1968
    Name:"Mother Nature's Son" Year:1968
    Name:"Ob-La-Di, Ob-La-Da" Year:1968
    Name:"Piggies" Year:1968
    Name:"Revolution"[m]# Year:1968
    Name:"Revolution 1"[n] Year:1968
    Name:"Revolution 9"[o] Year:1968
    Name:"Rocky Raccoon" Year:1968
    Name:"Savoy Truffle" Year:1968
    Name:"Sexy Sadie" Year:1968
    Name:"While My Guitar Gently Weeps" Year:1968
    Name:"Why Don't We Do It in the Road?" Year:1968
    Name:"Wild Honey Pie" Year:1968
    Name:"Yer Blues" Year:1968


### Json Provider

Finalmente, existe un _type provider_  para leer datos en formato JSON (JavaScript Object Notation), que es standard en la transmisión de información en internet.

```fsharp
[<Literal>]
let tvUrl = "https://raw.githubusercontent.com/mganitombalak/training/master/tv-shows.json"
```

```fsharp
type TvListing = JsonProvider<tvUrl>
let tvListing = TvListing.GetSamples()                                   

```

```fsharp
tvListing
|> Seq.map (fun t -> (t.Name,t.Rating.Average))
|> Seq.sortByDescending (fun (n,a) -> a)
|> Seq.take 20
|> Seq.iter (fun (n,a) -> printfn $"{n}: {a}")
```

    "Game of Thrones": Some(9.4)
    "Rick and Morty": Some(9.4)
    "Breaking Bad": Some(9.3)
    "The Wire": Some(9.3)
    "Firefly": Some(9.3)
    "Stargate SG-1": Some(9.3)
    "Berserk": Some(9.2)
    "Person of Interest": Some(9)
    "Fargo": Some(9)
    "House": Some(9)
    "Banshee": Some(9)
    "The Newsroom": Some(8.9)
    "Fringe": Some(8.9)
    "Battlestar Galactica": Some(8.9)
    "Stargate Atlantis": Some(8.9)
    "Vikings": Some(8.8)
    "Boardwalk Empire": Some(8.8)
    "Justified": Some(8.8)
    "Bob's Burgers": Some(8.8)
    "CSI: Crime Scene Investigation": Some(8.8)

