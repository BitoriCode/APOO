# Ejercicios – Archivos y serialización en POO

## Objetivo

Estos ejercicios conectan directamente con la teoría de lectura y escritura de archivos, serialización con JSON y pickle, y persistencia de objetos en clases orientadas a objetos.

La idea es que cada ejercicio modele una entidad del mundo real con una clase, y que luego esa información se guarde y se recupere desde un archivo.

---

## Ejercicio 1: Guardar datos de un estudiante

### Enunciado

Crea una clase llamada `Estudiante` con estos atributos:

- `nombre: str`
- `codigo: str`
- `promedio: float`

La clase debe tener:

- un constructor,
- un método `mostrar_info()` que imprima los datos del estudiante,
- un método `to_dict()` que retorne un diccionario con los atributos.

Luego crea una segunda clase llamada `RegistroEstudiantes` que tenga:

- un atributo `archivo`
- un método `guardar(estudiante)` que guarde el estudiante en un archivo JSON,
- un método `leer_todos()` que lea todos los estudiantes desde el archivo y los devuelva.

### Requisitos

- usa `json.dump()` y `json.load()`
- cada estudiante debe guardarse como un objeto JSON dentro de una lista
- al leer el archivo, deben crearse objetos de tipo `Estudiante`
- cada estudiante debe mantenerse en una estructura tipo lista

### Enunciado para resolver

Diseña la solución completa en Python. Debes implementar la clase `Estudiante` y la clase `RegistroEstudiantes` según las condiciones del enunciado.

Tu solución debe incluir:

- constructor de `Estudiante`
- método `mostrar_info()`
- método `to_dict()`
- clase `RegistroEstudiantes` con `guardar()` y `leer_todos()`
- lectura/escritura con JSON
- creación de objetos desde archivo
- manejo de archivos inexistentes


---

## Ejercicio 2: Inventario de productos

### Enunciado

Modela una clase `Producto` con:

- `nombre`
- `precio`
- `stock`

Luego crea una clase `Inventario` que permita:

- agregar un producto,
- guardar la lista de productos en un archivo JSON,
- cargar los productos nuevamente desde ese archivo.

Además, cada producto debe poder:

- mostrar su información,
- reducir su stock si se vende una cantidad,
- validar que la cantidad vendida sea positiva y no supere el stock.

### Enunciado para resolver

Diseña la solución completa en Python. Debes implementar la clase `Producto` y la clase `Inventario` según las condiciones del enunciado.

Tu solución debe incluir:

- constructor de `Producto`
- validación de venta
- método `vender(cantidad)`
- método `to_dict()`
- método `mostrar()`
- clase `Inventario` con `cargar()`, `guardar()` y `agregar()`
- uso de JSON para almacenamiento permanente
- reconstrucción de objetos desde archivo


---

## Ejercicio 3: Agenda de contactos con pickle

### Enunciado

Crea una clase `Contacto` con:

- `nombre`
- `telefono`
- `correo`

Luego crea una clase `Agenda` con:

- un archivo binario para guardar los contactos,
- método `agregar_contacto(contacto)`,
- método `cargar_contactos()`,
- método `mostrar_contactos()`.

El sistema debe guardar toda la agenda usando `pickle`.

### Enunciado para resolver

Diseña la solución completa en Python. Debes implementar la clase `Contacto` y la clase `Agenda` según las condiciones del enunciado.

Tu solución debe incluir:

- constructor de `Contacto`
- método `mostrar()`
- clase `Agenda` con carga y guardado con `pickle`
- método `agregar_contacto(contacto)`
- método `mostrar_contactos()`
- tratamiento de archivo inexistente
- persistencia de objetos Python en archivo binario


---

## Ejercicio 4: Registro de biblioteca digital

### Enunciado

Crea una clase `Libro` con:

- `titulo`
- `autor`
- `anio_publicacion`
- `disponible` (True o False)

Luego crea una clase `Biblioteca` que:

- guarde la colección de libros en un archivo JSON,
- cargue la colección al iniciar,
- permita prestar un libro,
- permita devolver un libro,
- permita mostrar los libros que están disponibles.

### Requisitos

- el archivo debe guardar una lista de diccionarios,
- debes usar `json.dump()` y `json.load()`,
- el método de préstamo debe validar que el libro exista y esté disponible,
- el método de devolución debe cambiar el estado a disponible.

### Enunciado para resolver

Diseña la solución completa en Python. Debes implementar la clase `Libro` y la clase `Biblioteca` según las condiciones del enunciado.

Tu solución debe incluir:

- constructor de `Libro`
- atributo `disponible`
- método `to_dict()`
- clase `Biblioteca` con `cargar()`, `guardar()`, `agregar()`, `prestar()` y `devolver()`
- validación de existencia y disponibilidad del libro
- uso de JSON como almacenamiento persistente
- listado de libros disponibles


---

## Ejercicio 5: Sistema básico de usuarios

### Enunciado

Crea una clase `Usuario` con:

- `usuario`
- `contrasena`
- `rol`

Luego crea una clase `SistemaUsuarios` que:

- guarde una lista de usuarios en un archivo JSON,
- permita registrar un nuevo usuario,
- permita mostrar todos los usuarios,
- permita buscar un usuario por nombre de usuario.

### Requisitos

- cada usuario debe convertirse en diccionario antes de ser guardado,
- al cargar el archivo, se deben reconstruir objetos `Usuario`,
- la búsqueda debe devolver el objeto encontrado o un mensaje si no existe.

### Enunciado para resolver

Diseña la solución completa en Python. Debes implementar la clase `Usuario` y la clase `SistemaUsuarios` según las condiciones del enunciado.

Tu solución debe incluir:

- constructor de `Usuario`
- método `to_dict()`
- clase `SistemaUsuarios` con `cargar()`, `guardar()`, `registrar()` y `buscar()`
- almacenamiento persistente en archivo JSON
- reconstrucción de objetos desde el archivo
- búsqueda por nombre de usuario


---

## Ejercicio 6: Caso integrador final

### Enunciado

Diseña un sistema orientado a objetos para gestionar una pequeña tienda. Debe incluir:

- clase `Producto`
- clase `Cliente`
- clase `Factura`
- clase `Tienda`

La clase `Tienda` debe:

- guardar sus productos en un archivo JSON,
- guardar las facturas en otro archivo JSON,
- cargar la información al iniciar,
- permitir vender un producto,
- permitir registrar un cliente,
- generar una factura en formato diccionario.

### Requisitos

- cada `Producto` debe tener nombre, precio y stock,
- cada `Cliente` debe tener nombre, identificacion y email,
- cada `Factura` debe almacenar cliente, productos comprados y total,
- la tienda debe manejar archivos JSON y persistencia,
- la validación de stock debe hacerse antes de vender,
- el sistema debe trabajar con listas y diccionarios.

### Enunciado para resolver

Diseña la solución completa en Python para un sistema de tienda con estas clases:

- `Producto`
- `Cliente`
- `Factura`
- `Tienda`

Debes implementar:

- atributos básicos de cada clase
- conversión a diccionario para serializar
- persistencia en archivos JSON
- carga y guardado de productos y facturas
- validación de stock antes de vender
- registro de clientes
- generación de facturas

No es necesario entregar una solución hecha; la idea es que el estudiante lo desarrolle por completo.

---

## Ejercicio 7: Retos de pensamiento

### 1) ¿Qué hace una clase de gestión de archivos?

Explica con tus palabras para qué sirve una clase como `GestorArchivo`, `Inventario`, o `Repositorio` en un sistema orientado a objetos.

### 2) ¿Por qué se usa JSON en lugar de guardar texto libre?

Explica ventajas de JSON frente a un archivo de texto plano.

### 3) ¿Cuándo conviene usar pickle?

Describe un caso concreto en el que pickle sea útil y otro caso en el que no lo sea.

### 4) ¿Qué pasa si un archivo no existe al intentar leerlo?

Explica cómo puedes manejarlo con `try/except`.

### 5) ¿Por qué es útil convertir un objeto a diccionario antes de guardar?

Responde desde la perspectiva de serialización y persistencia.

---

## Ejercicio 8: Mini proyecto para entregar

### Enunciado

Diseña un sistema para gestionar notas de curso. Debe tener:

- clase `Nota`
- clase `GestorNotas`

Cada nota debe tener:

- `titulo`
- `contenido`
- `fecha`
- `categoria`

El gestor debe:

- guardar notas en un archivo JSON,
- leer notas desde el archivo,
- agregar una nota,
- listar todas las notas,
- buscar una nota por título.

### Objetivo

Practicar todo lo visto:

- clases,
- atributos,
- métodos,
- archivos,
- JSON,
- persistencia,
- recuperación de objetos.

---

## Criterios de evaluación

Se evaluará que el estudiante pueda:

- crear clases con atributos y métodos,
- persistir datos de esos objetos en archivos,
- convertir objetos a diccionarios o estructuras serializables,
- usar `json.dump` y `json.load`,
- usar `pickle.dump` y `pickle.load` cuando corresponda,
- manejar errores al trabajar con archivos,
- crear una arquitectura simple y ordenada en POO.

---

## Conclusión

Estos ejercicios buscan que el estudiante entienda algo muy importante: los objetos no viven siempre en memoria. A veces necesitan guardarse para poder volver a cargarse después. Ahí aparece la persistencia y la serialización.

Cuando se combina POO con archivos, se obtiene una base sólida para crear sistemas más reales, organizados y reutilizables.
