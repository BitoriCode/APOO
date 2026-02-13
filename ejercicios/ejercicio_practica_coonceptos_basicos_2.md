## Enunciado directo

Diseña una solución usando **POO** para modelar una **Lista de Tareas (To-Do)**.

El sistema debe permitir crear tareas y administrarlas dentro de una lista.

### Requisitos

**Clase `Tarea`**

* Atributos mínimos:

  * `titulo` (str)
  * `descripcion` (str)
  * `completada` (bool) → inicia en `False`
* Métodos:

  * `marcar_completada()` → cambia `completada` a `True`
  * `resumen()` → devuelve un `str` con el título y el estado (pendiente/completada)

**Clase `ListaTareas`**

* Atributos mínimos:

  * `tareas` (list[Tarea]) → inicia como lista vacía
* Métodos:

  * `agregar(tarea: Tarea)` → agrega una tarea a la lista
  * `listar()` → muestra todas las tareas con su índice (posición en la lista)
  * `completar(indice: int)` → marca como completada la tarea en esa posición si existe
  * `eliminar(indice: int)` → elimina la tarea en esa posición si existe

### Reglas

* Si el índice no existe (fuera de rango), el método debe informar el error sin romper el programa.
* Usa **pistas de tipos** (type hints) en parámetros y valores de retorno.

---

## Versión solo prosa (para identificar elementos)

En una aplicación se necesita organizar pendientes personales. Cada pendiente tiene un título corto, una descripción y un estado que indica si ya fue realizado o no. Al crearse un pendiente, se asume que todavía no está realizado.

El sistema debe permitir almacenar muchos pendientes en un mismo lugar, mantenerlos organizados en una colección y operar sobre ellos: añadir nuevos pendientes, mostrar todos los pendientes enumerados para poder referirse a uno por su posición, marcar un pendiente específico como realizado y eliminar pendientes cuando ya no se necesiten.

Cuando el usuario intente operar sobre una posición que no exista en la colección (por ejemplo, un número negativo o un número mayor al último índice), la aplicación debe informar claramente el problema sin detener la ejecución.

**Tarea del estudiante:** a partir del texto, identificar qué sería la clase principal, qué clase representa los elementos individuales, cuáles son los atributos, cuáles son los métodos y qué estructura de datos se requiere para guardar múltiples elementos. Además, implementar la solución usando pistas de tipos.

---
