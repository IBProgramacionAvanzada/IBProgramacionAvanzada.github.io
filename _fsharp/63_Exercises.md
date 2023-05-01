---
layout: post
title: Más sobre colecciones y tipos de datos
tagline: Un poco de mob programming 
categories: 
- F# as your first functional programming language
tags:
- fsharp
---

### Alumnos en materias

Considere los siguientes tipo de datos:

```fsharp 

type Career = 
    | Engineering of string 
    | Physics


type Student = 
    {
        Name: string 
        Career: Career 
    }

type Participant = 
    | NonRegular of string 
    | Regular of Student 

type Course = 
    {
        Name: string
        Participants: Participant list 
    }
```

Escriba funciones asociadas a estos tipos de datos que permitan:

- Crear un curso con una lista de estudiantes
- Agregar un estudiante a un curso
- Dar de baja un estudiante de un curso
- Cambiar la carrera de un estudiante 

Suponga ahora que se requiere modificar el tipo `Course`, para considerar la posibilidad de que un curso esté aprobado para una serie determinada de carreras, o
para todas ellas.

```fsharp

type AcceptedCareers = 
    // Your code here 

type Course = 
    {
        Name: string
        Participants: Participant list 
        AcceptedCareers: AcceptedCareers 
    }
```

- Complete el tipo de dato `AcceptedCareers` 
- Modifique las funciones de la primera parte, de modo tal que validen si un participante puede inscribirse en un curso particular, de acuerdo a si es estudiante 
  o no, y a la carrera que sigue. 