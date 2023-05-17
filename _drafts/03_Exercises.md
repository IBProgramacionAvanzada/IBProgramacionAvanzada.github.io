# Ejercicios

### Funciones en la vida real 

- Escriba diez ejemplos de funciones en la vida real. 

- Escriba tres ejemplos de funciones que puedan componerse en la vida real. 

- Escriba tres ejemplos de la vida real donde se vea la aplicación parcial de funciones.

### Una introducción a un _Kata_ 

Un _kata_ es un ejercicio de programación que puede resolverse en una o dos horas. En general
se presenta como un ejercicio con varias partes, que deben ser resueltas en el orden indicado. A 
diferencia de un ejercicio _a secas_, hay algunas reglas para seguir.

1. En cada parte del ejercicio, se busca mejorar la forma en que se implementó el código, 
respecto de la parte anterior.
2. Es fundamental escribir _tests_. Esto es, pequeños programas para probar el código en
casos conocidos. El programa final que resuelve el ejercicio debe ser capaz de _pasar_ todos
los tests escritos.



### FizzBuzz _Kata_ 

Este es un ejercicio clásico de programación. Use su lenguaje de programación habitual.

- Escriba un programa que imprime los números del 1 al 100. Pero, cuando un número es múltiplo de tres, tiene que imprimir `Fizz` en lugar del número. Y para 
números múltiplos de cinco, tiene que imprimir `Buzz` en lugar del número. además, si el número 
es múltiplo de tres y cinco, debe imprimir `FizzBuzz`. 

Por ejemplo,

- 9 es divisible por 3, con lo cual debe imprimirse `Fizz`.
- 15 es divisible por 3 y por 5, con lo cual debe imprimirse `FizzBuzz`.
- 25 es divisible por 5, con lo cual debe imprimirse `Buzz`.
- 34 no es divisible ni por 3 ni por 5, con lo cual debe imprimirse 34.

El resultado esperado hasta desde el 1 hasta el 15 es:
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
```

- Ahora considere que se quiere imprimir los números desde 1 hasta un número arbitrario cualquiera 
_N_ mayor que 1. 

- Siguiendo la idea anterior, agregue ahora el caso de reemplazar los múltiplos de siete en 
la lista por `Whizz`. Por supuesto, en caso en que el número sea múltiplo de 3 y 7, deberá imprimir
`FizzWhizz`, mientras que en el caso en que sea múltiplo de 3, 5 y 7, debería decir `FizzBuzzWhizz`, etc.

- ¿Cuál sería la mejor manera de implementar el código para que sea sencillo agregar un caso arbitrario
de múltiplos en el futuro? 

### El _Kata_ de los precios en el supermercado

En general, la mayoría de los productos en el supermercado tienen un precio definido en pesos, por ejemplo,
un paquete de galletas puede costar 123,75\$. 
Pero también hay productos que se venden por peso, como un kilo de manzanas o naranjas.
Además, se ven frecuentemente algunas promociones como:

- Si lleva dos del mismo producto, 70% de descuento en la segunda unidad.
- Una lata de atún puede estar de oferta, llevando tres , paga por dos.
- O puede ser que por 1000 pesos uno pueda llevarse tres paquetes de yerba.

Este es un kata para la ducha. No es necesario en este punto implementarlo como código, sino más bien
pensar el modelo que uno usaría para describir, por ejemplo, cómo sumar los distintos precios 
de los productos en la caja. Algunas cosas para pensar

- ¿Se puede tener valores de precio representados por números periódicos?
- ¿Cómo manejar el redondeo?
- ¿Cómo diseñar los tests para los casos de arriba?






