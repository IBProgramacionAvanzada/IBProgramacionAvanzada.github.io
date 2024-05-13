## Ejercicios

### El video de la semana

[_The lazy programmer's guide to writing 1000's of tests_, Scott Wlaschin](https://www.youtube.com/watch?v=IYzDFHx6QPY&ab_channel=NDCConferences)


### Interpolación Lineal

Nos han encomendado que escribamos una función que dadas dos listas `x` e `y` de números de punto flotante,
y dado un valor específico `x0`, encontremos el correspondiente valor de `y0` mediante una interpolación lineal.

Debido a la explosión de AI, decidimos pedirle a [ChatGPT](https://openai.com/blog/chatgpt) que nos escriba el código
en F#, y nos concentraremos en realizar test unitarios para probar que el código generado funciona correctamente.

Entonces:

- Cree una solución para organizar el código. En dicha solución cree
  
  - un proyecto que contenga la biblioteca de funciones que se usarán en la interpolación lineal.
  - un proyecto de tests de NUnit para probarlas. 

- Obtenga el código de ChatGPT, (o escríbalo usted mismo...)
  
- Escriba los tests para probar el código, en condiciones normales: 
  
  - `x` e `y` tienen el mismo tamaño `n`. 
  - `x` tiene todos elementos diferentes, y en orden creciente.
  - `x0` está entre los valores `x[0]` y `x[n-1]`.

- Modifique ahora el código para que retorne un tipo `Result<float,string>`, de modo tal que se emita un error si 

  - `x` e `y` tienen distintos tamaños.
  - `x` tiene elementos iguales
  
  y devuelva el valor `Ok y0` interpolado si las validaciones son exitosas.

- Modifique los tests para contemplar los casos anteriores.


#### Extrapolación

Nos avisan ahora que el código tiene que contemplar el caso de la extrapolación,
que ocurre cuando `x0 < x[0]` o `x0 > x[n-1]` de forma tal que:

- Si `x0 < x[0]`, retorne el valor `y0 = y[0]`.
- Retorne un nuevo error si `x0 > x[n-1]`.

Modifique la biblioteca y los tests de acuerdo a este nuevo requerimiento.

> Tenga en cuenta que no es necesario crear un proyecto que _use_ la biblioteca,
> basta con el proyecto para testearla, y el de la biblioteca propiamente dicha.

