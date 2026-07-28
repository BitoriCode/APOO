# Ejercicios de práctica – Conceptos de POO

---

## 1) Producto en tienda

Diseña una solución usando Programación Orientada a Objetos para representar un **Producto** dentro de una tienda.

Un producto debe tener como información mínima:

* el **nombre** del producto,
* el **precio** unitario,
* la **cantidad en inventario** (stock disponible).

El producto debe permitir realizar estas acciones:

* **Vender** una cantidad: solo se acepta si la cantidad es mayor que cero y si hay suficiente stock. Si no se cumple alguna condición, se debe informar la razón.
* **Reabastecer** una cantidad: solo se acepta si la cantidad es mayor que cero. Si no, se debe informar que la cantidad es inválida.
* **Mostrar información**: debe mostrar el nombre, el precio y el stock actual.

Además, al crear un producto se debe permitir indicar una **cantidad inicial en inventario** (opcional). Si no se indica, el stock inicia en cero.

**Tarea del estudiante:** implementar la solución en Python usando una clase, atributos y métodos.

---

## 2) Estudiante universitario

Diseña una solución usando Programación Orientada a Objetos para representar un **Estudiante** universitario.

Un estudiante debe tener como información mínima:

* el **nombre** completo,
* el **código de estudiante**,
* la **lista de calificaciones** obtenidas en sus materias.

El estudiante debe permitir realizar estas acciones:

* **Registrar calificación**: solo se acepta si el valor está entre 0.0 y 5.0. Si está fuera de ese rango, se debe informar que la calificación es inválida.
* **Calcular promedio**: retorna el promedio de todas las calificaciones registradas. Si no hay calificaciones, debe informarse que aún no hay notas para calcular.
* **Mostrar resumen**: debe mostrar el nombre, el código y el promedio actual del estudiante.

Además, al crear un estudiante la lista de calificaciones inicia vacía por defecto.

**Tarea del estudiante:** implementar la solución en Python usando una clase, atributos y métodos.

---

## 3) Mascota en clínica veterinaria

En una clínica veterinaria se necesita llevar registro de las mascotas que son atendidas. Cada mascota tiene un nombre, pertenece a una especie (por ejemplo, perro o gato) y tiene una edad expresada en años. Además, se sabe a quién pertenece la mascota, es decir, el nombre de su dueño.

Cada vez que una mascota visita la clínica, se debe registrar el motivo de esa visita para llevar un historial de consultas. Este historial puede consultarse en cualquier momento para saber todas las razones por las que la mascota ha sido atendida. Si se intenta registrar un motivo vacío o en blanco, el sistema debe informar que el motivo no es válido.

En cualquier momento debe poder mostrarse la información principal de la mascota: su nombre, su especie, su edad y el nombre de su dueño. Al crear el registro de una mascota, el historial de visitas comienza vacío.

**Tarea del estudiante:** leer el texto y determinar qué corresponde a clase, atributos, métodos y reglas.
