---
layout: post
title: Explorando exoplanetas con proveedores de tipos y Plotly
tagline: 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

 
Este es un nuevo episodio de dos partes del curso [Haga de F# su primer lenguaje de programación funcional](https://github.com/fcolavecchia/fp-course-public). En la primera parte revisamos el uso de un proveedor de tipos (un _Type Provider_) (gracias a [`FSharp.Data`](https://fsprojects.github.io/FSharp.Data/)), mientras exploramos cómo trazar los datos usando [Plotly](https://plotly.com/fsharp/). Este es un flujo de trabajo típico para el procesamiento en ciencias de datos.









## Obteniendo los datos

¡Vamos al espacio!

Sí, la Tierra no está sola en el Universo, ya que hay miles de planetas orbitando estrellas en nuestra galaxia, no tan lejana. Estos planetas, llamados exoplanetas, fueron descubiertos por primera vez en 1992. Para este episodio, usaremos los datos de [Archivo de exoplanetas de la NASA](https://exoplanetarchive.ipac.caltech.edu/index.html). Los datos se almacenan en una base de datos a la que se puede acceder mediante una API, por ejemplo con `wget` o directamente en el navegador. Descargué una versión seleccionada de esos datos para jugar, en un archivo [archivo csv](data/consolidatedExoPlanets.csv).

Recordemos que un proveedor de tipos es una implementación en F# que permite crear un "tipo" a partir de algunos datos estructurados leídos de un archivo. Estos datos pueden estar en formato html, csv, json o xml, que son omnipresentes en la web. Para ello, necesitamos instalar el paquete en este _notebook_ y abrirlo:

```F#
#r "nuget: FSharp.Data"

open FSharp.Data
```


<div><div></div><div></div><div><strong>Installed Packages</strong><ul><li><span>FSharp.Data, 6.4.0</span></li></ul></div></div>


El proveedor de tipos necesita una fuente de datos estructurada para construir el tipo, en nuestro caso tenemos los datos en un archivo:

```F#
[<Literal>]
let exoplanetsFile = "../data/exoplanets.csv"
```

Crear el tipo es tan fácil como

```F#
type ExoPlanetTypeProvider = FSharp.Data.CsvProvider<exoplanetsFile, HasHeaders=true>
```

Como es habitual en la ciencia de datos, uno echa un vistazo a los datos para tener una idea general acerca de qué se tratan, dejando los detalles al código. Se puede notar que el archivo tiene los nombres de las columnas en la primera fila, es por eso que usamos el argumento `HasHeaders=true`.

> Consulte más detalles sobre cómo funciona el proveedor de tipos en [Este episodio](https://github.com/fcolavecchia/fp-course-public/blob/main/en/80_TypeProviders.ipynb).

Ahora creamos efectivamente los datos y el tipo con:

```F#
let exoplanets = ExoPlanetTypeProvider.GetSample()
```

Veamos qué tenemos dentro:

```F#
exoplanets.Headers
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(System.String[])</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>[ pl_name, soltype, disc_refname, hd_name, pl_masse, pl_orbper, discoverymethod, cb_flag, sy_dist, pl_insol ]</pre></div></td></tr></tbody></table></div></details><style>
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


Esto nos da un tipo `Option` con los nombres de los datos que contiene. Imprimamoslo más claramente iterando la secuencia de encabezados:

```F#
exoplanets.Headers
|> Option.map (fun h -> 
                h
                |> Seq.iteri (fun i name -> printfn "Item %d: %s" (i+1) name)
)
```

    Item 1: pl_name
    Item 2: soltype
    Item 3: disc_refname
    Item 4: hd_name
    Item 5: pl_masse
    Item 6: pl_orbper
    Item 7: discoverymethod
    Item 8: cb_flag
    Item 9: sy_dist
    Item 10: pl_insol



<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(null)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details><style>
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


Hay diez columnas que corresponden a la siguiente información, según el sitio de la NASA:

- `pl_name`: este es el nombre del exoplaneta
- `soltype`: El status del exoplaneta referido al conjunto completo de planetas
- `disc_refname`: un fragmento HTML con la URL de la referencia publicada del descubrimiento.
- `hd_name`: El nombre de la estrella que alberga el planeta.
- `pl_masse`: La masa planetaria, medida en unidades de la masa de la Tierra (es decir: `pl_masse` de la Tierra es igual a uno)
- `pl_orbper`: El período orbital (es decir, la duración del año del exoplaneta) medido en años terrestres
- `discoverymethod`: El método utilizado en el descubrimiento.
- `cb_flag`: Si el planeta orbita en un sistema binario (¡eso sí que sería una vista!)
- `sy_dist`: Distancia al sistema planetario en unidades de pársecs (un pársec son unos 3,26 años luz)
- `pl_insol`: Flujo de insolación, la cantidad de energía que el planeta recibe de la estrella anfitriona, expresada en unidades relativas al flujo medido para la Tierra desde el Sol.

Estas son las principales características de un exoplaneta. La idea detrás de esta investigación es encontrar planetas similares a la Tierra que puedan albergar vida tal como la conocemos. Por tanto, es importante conocer la masa del planeta (los planetas grandes suelen ser gaseosos como Jupyter o Saturno); la distancia desde la estrella anfitriona (demasiado lejos es demasiado frío, demasiado cerca sería caliente) y la cantidad de energía que el planeta recibe de la estrella (las estrellas pueden ser muy grandes y brillantes, por lo que aunque el planeta pueda estar lejos, todavía podría recibir mucha luz de la estrella anfitriona, impidiendo la formación de vida tal como la conocemos).

¡Bien! Recuerde también que el proveedor devuelve los datos como una secuencia en la propiedad `.Rows`:

```F#
exoplanets.Rows 
|> Seq.take 2
|> Seq.iteri (fun i s ->  printfn $"{i}: %A{s}")
```

    0: ("OGLE-TR-10 b", "Published Confirmed",
     "<a refstr=KONACKI_ET_AL__2005 href=https://ui.adsabs.harvard.edu/abs/2005ApJ...624..372K/abstract target=ref> Konacki et al. 2005 </a>",
     "", 197.046, 3.101278, "Transit", false, 1344.97, nan)
    1: ("HD 210702 b", "Published Confirmed",
     "<a refstr=JOHNSON_ET_AL__2007 href=https://ui.adsabs.harvard.edu/abs/2007ApJ...665..785J/abstract target=ref> Johnson et al. 2007 </a>",
     "HD 210702", nan, 354.29, "Radial Velocity", false, 54.1963, nan)


Extraigamos el primero a un valor:

```F#
let exo0 = exoplanets.Rows |> Seq.item 0
```

Recuerde también que aunque el nombre de la columna del archivo sea, por ejemplo, `pl_name`, se puede acceder a este valor de un planeta en particular con el campo `Pl_name` del tipo. Aquí el compilador de F# le ayudará a determinar el nombre de cada campo de la fila actual, cuando intente acceder a uno de ellos, simplemente siga adelante y escriba el valor y aparecerán los campos posibles:

<img src="../data/Fields of Exoplanet type.png" alt="" width="400"/>


Mmmm, parece que algunas columnas se leen como `nan`. (Un clásico de la ciencia de datos...). No se preocupe, el proveedor de tipos nos permite cambiar ese campo a un tipo `Option`, dando `Some value` cuando lo haya, y `None`, en lugar de `nan`. Sin embargo, necesitamos recrear el tipo con la opción `PreferOptionals=true`:

```F#
type ExoPlanetType = FSharp.Data.CsvProvider<exoplanetsFile, HasHeaders=true, PreferOptionals=true>
```

```F#
let exoplanets2 = ExoPlanetType.GetSample()
```

> Podríamos haber usado el mismo nombre para el valor aquí, como `let exoplanets = ExoPlanetType.GetSample()` porque el cuaderno nos permite hacerlo. Sin embargo, para mantener las cosas más limpias, uso "exoplanetas2".

Ahora nuestro primer exoplaneta en la lista es:

```F#
let ogleTR10b = 
    exoplanets2.Rows 
    |> Seq.item 0

printfn "Name: %A" ogleTR10b.Pl_name
printfn "Insolation: %A" ogleTR10b.Pl_insol
```

    Name: "OGLE-TR-10 b"
    Insolation: None


¡Ahora sí! Veamos los tipos de cada campo en el `ExoPlanetType`:

```F#
ogleTR10b.GetType().GetProperties()
|> Seq.iter (fun p -> printfn $"{p.PropertyType}")
```

    System.String
    System.String
    System.String
    Microsoft.FSharp.Core.FSharpOption`1[System.String]
    Microsoft.FSharp.Core.FSharpOption`1[System.Decimal]
    Microsoft.FSharp.Core.FSharpOption`1[System.Decimal]
    System.String
    System.Tuple`3[System.Boolean,Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal]]


Hay una función interesante para ver los datos como una tabla, `.DisplayTable()` en este entorno interactivo:

```F#
exoplanets2.Rows
|> Seq.take 4
|> fun r -> r.DisplayTable()
```


<table><thead><tr><td><span>Item1</span></td><td><span>Item2</span></td><td><span>Item3</span></td><td><span>Item4</span></td><td><span>Item5</span></td><td><span>Item6</span></td><td><span>Item7</span></td><td><span>Rest</span></td></tr></thead><tbody><tr><td>OGLE-TR-10 b</td><td>Published Confirmed</td><td>&lt;a refstr=KONACKI_ET_AL__2005 href=https://ui.adsabs.harvard.edu/abs/2005ApJ...624..372K/abstract target=ref&gt; Konacki et al. 2005 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(197.04600)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>197.04600</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(3.10127800)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>3.10127800</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(1344.9700000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(1344.9700000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>1344.9700000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>HD 210702 b</td><td>Published Confirmed</td><td>&lt;a refstr=JOHNSON_ET_AL__2007 href=https://ui.adsabs.harvard.edu/abs/2007ApJ...665..785J/abstract target=ref&gt; Johnson et al. 2007 &lt;/a&gt;</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(HD 210702)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>HD 210702</pre></div></td></tr></tbody></table></div></details></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(354.29000000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>354.29000000</pre></div></td></tr></tbody></table></div></details></td><td>Radial Velocity</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(54.1963000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(54.1963000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>54.1963000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>BD-08 2823 b</td><td>Published Confirmed</td><td>&lt;a refstr=HEBRARD_ET_AL__2010 href=https://ui.adsabs.harvard.edu/abs/2010A%26A...512A..46H/abstract target=ref&gt; H&amp;eacute;brard et al. 2010 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(5.60000000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>5.60000000</pre></div></td></tr></tbody></table></div></details></td><td>Radial Velocity</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(41.3342000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(41.3342000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>41.3342000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>BD-08 2823 c</td><td>Published Confirmed</td><td>&lt;a refstr=HEBRARD_ET_AL__2010 href=https://ui.adsabs.harvard.edu/abs/2010A%26A...512A..46H/abstract target=ref&gt; H&amp;eacute;brard et al. 2010 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(237.60000000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>237.60000000</pre></div></td></tr></tbody></table></div></details></td><td>Radial Velocity</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(41.3342000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(41.3342000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>41.3342000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table><style>
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


Verá que `DisplayTable()` muestra los valores `None` impresos como `<null>`.
La segunda columna es el estado de descubrimiento del exoplaneta, y podemos contar cuántos planetas para cada tipo de estado tiene la lista:

```F#
exoplanets2.Rows 
|> Seq.countBy (fun x -> x.Soltype)
|> Seq.iter (fun (k,v) -> printfn $"{k}: {v}")
```

    Published Confirmed: 17420
    Kepler Project Candidate (q1_q8_koi): 2310
    TESS Project Candidate: 877
    Published Candidate: 776
    Kepler Project Candidate (q1_q17_dr24_koi): 2705
    Kepler Project Candidate (q1_q12_koi): 2683
    Kepler Project Candidate (q1_q16_koi): 2725
    Kepler Project Candidate (q1_q17_dr25_koi): 2719
    Kepler Project Candidate (q1_q17_dr25_sup_koi): 2736


Trabajemos solo con los exoplanetas confirmados, creando una secuencia filtrando los datos originales:

```F#
let confirmed = 
    exoplanets2.Rows 
    |> Seq.filter (fun x -> x.Soltype = "Published Confirmed")

confirmed |> Seq.length    
```


<div class="dni-plaintext"><pre>17420</pre></div><style>
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


Esto es extraño, ya que el sitio de la NASA habla de unos 5.000 planetas. Debe haber algún dato que se repita. Agrupemos los datos por el nombre del planeta, que se puede suponer que es una buena clave única:

```F#
confirmed
|> Seq.groupBy (fun x -> x.Pl_name)
|> Seq.length
```


<div class="dni-plaintext"><pre>5483</pre></div><style>
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


¡Es correcto! (para agosto de 2023...) Entonces, necesitamos ver qué pasa con las repeticiones. Agrupemos por nombre de planeta, y tomemos el que más se repite:

```F#
let exoWithMaxEntriesName, exoWithMaxEntries =
    confirmed
    |> Seq.groupBy (fun x -> x.Pl_name) // Group by name 
    |> Seq.map (fun (name, seq) -> name, seq |> Seq.length, seq) // Map into a tuple of name, count and values
    |> Seq.maxBy (fun (name, count, seq) -> count) // Find the tuple with the highest count
    |> fun (name, count, seq) -> name, seq // Return the name and count

printfn $"Planet {exoWithMaxEntriesName} has {exoWithMaxEntries |> Seq.length} entries"


```

    Planet TrES-2 b has 25 entries


Creamos dos valores a la vez (una tupla) a partir del procesamiento de la secuencia de planetas "confirmados". El primer valor `exoWithMaxEntriesName` contiene el nombre del exoplaneta que más se repite, mientras que el segundo, `exoWithMaxEntries` es la lista de las diferentes filas correspondientes a ese planeta. ¡Parece que el planeta _TrES-2 b_ tiene 25 entradas! Veamos cómo se ven esos datos:

```F#
exoWithMaxEntries.DisplayTable()

```


<table><thead><tr><td><span>Item1</span></td><td><span>Item2</span></td><td><span>Item3</span></td><td><span>Item4</span></td><td><span>Item5</span></td><td><span>Item6</span></td><td><span>Item7</span></td><td><span>Rest</span></td></tr></thead><tbody><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.39600)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.39600</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.10000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.10000</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.44251)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.44251</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061317)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061317</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061320)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061320</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(383.30298)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>383.30298</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061323)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061323</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47062100)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47062100</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061320)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061320</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061010)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061010</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061340)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061340</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(382.03166)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>382.03166</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061892)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061892</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061337)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061337</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.74400)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.74400</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061344)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061344</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(398.24099)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>398.24099</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061010)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061010</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(367.72931)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>367.72931</pre></div></td></tr></tbody></table></div></details></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47062100)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47062100</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(406.82240)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>406.82240</pre></div></td></tr></tbody></table></div></details></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061339)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061339</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061338)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061338</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>TrES-2 b</td><td>Published Confirmed</td><td>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061350)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061350</pre></div></td></tr></tbody></table></div></details></td><td>Transit</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td colspan="8"><i>(5 more)</i></td></tr></tbody></table><style>
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


Parece que los valores numéricos para `pl_masse`, `pl_orbper` y (tal vez) `pl_insol` pueden ser diferentes para todas las entradas de un planeta determinado. Una posible forma de afrontar esta situación es promediar cada uno de ellos.

> No soy un experto en exoplanetas, así que tal vez haya otra forma adecuada de manejar estos datos...

Observe que, por ejemplo, los valores de `pl_masse` son `Opciones`:

```F#
exoWithMaxEntries
|> Seq.map (fun p -> p.Pl_masse)
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>[ Some(381.39600), Some(381.10000), Some(380.44251), Some(457.67520), Some(383.30298), Some(380.76034), Some(380.76034), Some(457.67520), &lt;null&gt;, &lt;null&gt;, Some(382.03166), &lt;null&gt;, Some(380.74400), Some(398.24099), Some(367.72931), Some(380.76034), Some(406.82240), &lt;null&gt;, &lt;null&gt;, &lt;null&gt; ... (more) ]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>f</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Collections.SeqModule+revamp@728[System.Tuple`8[System.String,System.String,System.String,Microsoft.FSharp.Core.FSharpOption`1[System.String],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],System.String,System.Tuple`3[System...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>f</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Collections.SeqModule+Map@748-1[System.Tuple`8[System.String,System.String,System.String,Microsoft.FSharp.Core.FSharpOption`1[System.String],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],System.String,System.Tuple`3[System....</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>mapping</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>FSI_0077+it@2-8</code></span></summary><div><table><thead><tr></tr></thead><tbody></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>ie</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>[ (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.39600), Some(2.47063000), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.10000), Some(2.47063000), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.44251), Some(2.47061317), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(383.30298), Some(2.47061323), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), , Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061010), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061340), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(382.03166), Some(2.47061892), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061337), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.74400), Some(2.47061344), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(398.24099), Some(2.47061010), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(367.72931), , Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(406.82240), Some(2.47063000), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061339), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061338), Transit, False, Some(215.3200000), ), (TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061350), Transit, False, Some(215.3200000), ) ... (more) ]</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>f</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Microsoft.FSharp.Collections.SeqModule+ReadOnly@1294[System.Tuple`8[System.String,System.String,System.String,Microsoft.FSharp.Core.FSharpOption`1[System.String],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],Microsoft.FSharp.Core.FSharpOption`1[System.Decimal],System.String,System.Tuple`3[Sys...</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>source</td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.39600), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(381.39600)</td></tr><tr><td>Item6</td><td>Some(2.47063000)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.10000), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(381.10000)</td></tr><tr><td>Item6</td><td>Some(2.47063000)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.44251), Some(2.47061317), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(380.44251)</td></tr><tr><td>Item6</td><td>Some(2.47061317)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(457.67520)</td></tr><tr><td>Item6</td><td>Some(2.47061320)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(383.30298), Some(2.47061323), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(383.30298)</td></tr><tr><td>Item6</td><td>Some(2.47061323)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(380.76034)</td></tr><tr><td>Item6</td><td>Some(2.47062100)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), , Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(380.76034)</td></tr><tr><td>Item6</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(457.67520)</td></tr><tr><td>Item6</td><td>Some(2.47061320)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061010), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061010)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061340), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061340)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(382.03166), Some(2.47061892), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(382.03166)</td></tr><tr><td>Item6</td><td>Some(2.47061892)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061337), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061337)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.74400), Some(2.47061344), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(380.74400)</td></tr><tr><td>Item6</td><td>Some(2.47061344)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(398.24099), Some(2.47061010), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(398.24099)</td></tr><tr><td>Item6</td><td>Some(2.47061010)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(367.72931), , Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(367.72931)</td></tr><tr><td>Item6</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(380.76034)</td></tr><tr><td>Item6</td><td>Some(2.47062100)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(406.82240), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td>Some(406.82240)</td></tr><tr><td>Item6</td><td>Some(2.47063000)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061339), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061339)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>18</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061338), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061338)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td>19</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061350), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td>TrES-2 b</td></tr><tr><td>Item2</td><td>Published Confirmed</td></tr><tr><td>Item3</td><td><a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref> O'Donovan et al. 2006 </a></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td>Some(2.47061350)</td></tr><tr><td>Item7</td><td>Transit</td></tr><tr><td>Rest</td><td>(False, Some(215.3200000), )</td></tr></tbody></table></div></details></td></tr><tr><td colspan="2"><i>(5 more)</i></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr><tr><td><i>(values)</i></td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.39600), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.39600)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.39600</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(381.10000), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.10000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.10000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.44251), Some(2.47061317), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.44251)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.44251</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061317)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061317</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061320)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061320</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(383.30298), Some(2.47061323), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(383.30298)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>383.30298</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061323)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061323</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47062100)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47062100</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), , Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(457.67520), Some(2.47061320), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061320)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061320</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061010), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061010)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061010</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>9</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061340), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061340)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061340</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(382.03166), Some(2.47061892), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(382.03166)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>382.03166</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061892)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061892</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061337), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061337)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061337</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.74400), Some(2.47061344), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.74400)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.74400</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061344)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061344</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(398.24099), Some(2.47061010), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(398.24099)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>398.24099</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061010)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061010</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(367.72931), , Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(367.72931)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>367.72931</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(380.76034), Some(2.47062100), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47062100)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47062100</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(406.82240), Some(2.47063000), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(406.82240)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>406.82240</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47063000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47063000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061339), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061339)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061339</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>18</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061338), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061338)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061338</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td>19</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , , Some(2.47061350), Transit, False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.47061350)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.47061350</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), )</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td>215.3200000</td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td colspan="2"><i>... (more)</i></td></tr></tbody></table></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr><tr><td><i>(values)</i></td><td><table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.39600)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.39600</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>1</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(381.10000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>381.10000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.44251)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.44251</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>4</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(383.30298)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>383.30298</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>7</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(457.67520)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>457.67520</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>8</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>9</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>10</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(382.03166)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>382.03166</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>11</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>12</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.74400)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.74400</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>13</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(398.24099)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>398.24099</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>14</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(367.72931)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>367.72931</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>15</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(380.76034)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>380.76034</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>16</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(406.82240)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>406.82240</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>17</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>18</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>19</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td colspan="2"><i>... (more)</i></td></tr></tbody></table></td></tr></tbody></table></div></details><style>
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


Promediamos esos valores, teniendo en cuenta sólo aquellos que en realidad son una medida de masa (dada por la opción `Some`), descartando los que no existen (los `None`). En lugar de ir directamente a los datos, puede resultar útil resolver el problema de promediar una lista de valores de `Option` con un ejemplo mínimo:

```F#
let optionsList = 
    [ Some 2.0m; Some 5.0m; None ; None ; Some 2.0m; Some 1.0m]  // decimal option list
    |> List.toSeq

let avg (data: decimal option seq) = 
    let values = 
        data  
        |> Seq.choose id // Discards the None values and keeps the Some values
    if Seq.isEmpty values then None else Some (values |> Seq.average)        

avg optionsList
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.5)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.5</pre></div></td></tr></tbody></table></div></details><style>
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


La aplicación de `Seq.choose id` elimina los `None`s y extrae los valores de la opción `Some`. También evitamos tomar el promedio de una secuencia vacía de datos con la construcción `if...then...else` (recuerde que en F# todo devuelve un valor, e `if` se usa como tal).

Tenga en cuenta que estamos creando una función para una `decimal option list` porque esos son los datos que obtenemos del proveedor para esos valores numéricos. El sufijo `m` convierte un literal en `decimal``. Para las masas de nuestro planeta *TrES-2 b* tenemos:

```F#
exoWithMaxEntries
|> Seq.map (fun p -> p.Pl_masse)
|> avg
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(398.61523058823529411764705882)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>398.61523058823529411764705882</pre></div></td></tr></tbody></table></div></details><style>
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


Ahora necesitamos mapear nuestra secuencia actual de datos para _un_ planeta en una sola entrada de la lista general. Construyamos una función que haga exactamente lo que necesitamos y luego hagamos un `Seq.map` sobre nuestra secuencia de planetas.

```F#
let collapse (planet: seq<ExoPlanetType.Row>) = 
    let masses =
        planet 
        |> Seq.map (fun v -> v.Pl_masse)
        |> avg 

    let orbper =
        planet 
        |> Seq.map (fun v -> v.Pl_orbper)
        |> avg

    let insol =
        planet 
        |> Seq.map (fun v -> v.Pl_insol)
        |> avg

    let planetData = planet |> Seq.head   
        
    let row = ExoPlanetType.Row(
        plName = planetData.Pl_name,
        soltype = planetData.Soltype,
        discRefname = planetData.Disc_refname,
        hdName = planetData.Hd_name,
        plMasse = masses ,
        plOrbper = orbper,
        discoverymethod = planetData.Discoverymethod,
        cbFlag = planetData.Cb_flag,
        syDist = planetData.Sy_dist,
        plInsol = insol
    )

    row

```

Hay algunos puntos a tener en cuenta:

Primero, el argumento de entrada de la función `collapse` es una secuencia de datos (representada por el tipo `ExoPlanetType.Row`) para un planeta determinado que tiene muchas entradas en nuestros datos originales, como hicimos con `TrES-2 b` . Entonces, el tipo del argumento de la función es el proporcionado por el proveedor de tipos, esto es, `ExoPlanetType.Row`. En segundo lugar, en la función calculamos el promedio de la masa `.Pl_masse`, el período orbital `.Pl_orbper` y el flujo de insolación `.Pl_insol`. Luego, dado que todos los datos de la secuencia comparten el resto de la información, extraemos estos datos de la primera entrada de la secuencia, con `planet |> Seq.head`. Finalmente, usamos un constructor `ExoPlanetType.Row` para construir los nuevos datos.

> Aclaremos algunas posibles confusiones sobre los nombres utilizados para cada campo en el Proveedor de tipos. Por ejemplo, tomemos el nombre del planeta. El encabezado de la columna es `pl_name`. Esto se traduce al campo `Pl_name` en el tipo creado por el proveedor, al que se puede acceder mediante la notación `.Pl_name`. Pero, para crear nuevos datos para el tipo, se utiliza `plName = planetData.Pl_name`. Afortunadamente, el compilador de F# siempre nos ayuda, solo recuerda pasar el mouse sobre `ExoPlanetType.Row` para ver cómo mapear cada campo del tipo en el constructor.

> El objetivo de este cuaderno es intentar utilizar el tipo creado por el Proveedor tanto como sea posible. Sin embargo, se pueden evitar las advertencias precedentes creando nuestro propio tipo y transformando `ExoPlanetType.Row` al nuestro. Eso dependerá de cuál será el uso de los datos, veremos un ejemplo en la segunda parte de este episodio.

Probemos esto en `exoWithMaxEntries` y veamos qué obtenemos:


```F#
collapse exoWithMaxEntries
```


<details open="open" class="dni-treeview"><summary><span class="dni-code-hint"><code>(TrES-2 b, Published Confirmed, &lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;, , Some(398.61523058823529411764705882), Some(2.470617085), Transit, False, Some(215.3200000), Some(903.86))</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>TrES-2 b</pre></div></td></tr><tr><td>Item2</td><td><div class="dni-plaintext"><pre>Published Confirmed</pre></div></td></tr><tr><td>Item3</td><td><div class="dni-plaintext"><pre>&lt;a refstr=O_DONOVAN_ET_AL__2006 href=https://ui.adsabs.harvard.edu/abs/2006ApJ...651L..61O/abstract target=ref&gt; O&#39;Donovan et al. 2006 &lt;/a&gt;</pre></div></td></tr><tr><td>Item4</td><td><div class="dni-plaintext"><pre>&lt;null&gt;</pre></div></td></tr><tr><td>Item5</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(398.61523058823529411764705882)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>398.61523058823529411764705882</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item6</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(2.470617085)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>2.470617085</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item7</td><td><div class="dni-plaintext"><pre>Transit</pre></div></td></tr><tr><td>Rest</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>(False, Some(215.3200000), Some(903.86))</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Item1</td><td><div class="dni-plaintext"><pre>False</pre></div></td></tr><tr><td>Item2</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(215.3200000)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>215.3200000</pre></div></td></tr></tbody></table></div></details></td></tr><tr><td>Item3</td><td><details class="dni-treeview"><summary><span class="dni-code-hint"><code>Some(903.86)</code></span></summary><div><table><thead><tr></tr></thead><tbody><tr><td>Value</td><td><div class="dni-plaintext"><pre>903.86</pre></div></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details></td></tr></tbody></table></div></details><style>
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


Ahora podemos volver a nuestra lista completa de planetas (posiblemente repetidos), agruparlos y colapsarlos en una entrada por planeta con nombre:

```F#
let planets = 
    confirmed
    |> Seq.groupBy (fun p -> p.Pl_name)
    |> Seq.map (fun (name, entries) -> collapse entries)
    
```

```F#
planets |> Seq.length
```


<div class="dni-plaintext"><pre>5483</pre></div><style>
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


¡Bien! ¡Ahora tenemos el número correcto de planetas! Incluso podemos usar el proveedor de tipos para escribir los datos nuevos y consolidados en un archivo:

```F#
let myCsv = new ExoPlanetType(planets)
let file = myCsv.SaveToString()
File.WriteAllText("../data/consolidatedExoplanets.csv", file)
```

¡Maravilloso, hemos seleccionado nuestra lista de entrada de exoplanetas, usando solo el Proveedor de tipos y algunas funciones útiles! En la siguiente parte, leeremos los datos consolidados y los graficaremos...
