## 1) Cuenta bancaria(enucniado claro)
Diseña una solución usando Programación Orientada a Objetos para representar una **Cuenta Bancaria**.

Una cuenta bancaria debe tener como información mínima:

* el **titular** (nombre de la persona dueña),
* el **número de cuenta**,
* el **saldo** actual.

La cuenta debe permitir realizar estas acciones:

* **Depositar** un monto: solo se acepta si el monto es mayor que cero. Si no, se debe informar que el monto es inválido.
* **Retirar** un monto: solo se permite si el monto es mayor que cero y si el saldo es suficiente. Si no se cumple alguna condición, se debe informar la razón.
* **Mostrar resumen**: debe mostrar titular, número de cuenta y saldo actual.

Además, al crear una cuenta se debe permitir indicar un **saldo inicial** (opcional). Si no se indica, inicia en cero.

**Tarea del estudiante:** implementar la solución en Python usando una clase, atributos y métodos.

---

## 2) Cuenta bancaria(eneunciado en prosa)

En una aplicación se quiere modelar el comportamiento básico de una cuenta bancaria. Cada cuenta pertenece a una persona y se identifica con un número único. A lo largo del tiempo, el saldo de la cuenta cambia cuando se realizan movimientos: en algunos casos se agrega dinero y en otros se descuenta.

Cuando se agrega dinero, el sistema solo debe aceptar valores positivos; si se intenta agregar un valor no válido, debe informarse el problema. Cuando se descuenta dinero, el sistema debe asegurarse de que el valor solicitado sea positivo y que exista saldo suficiente para realizar la operación; si no se cumple, también debe informarse.

En cualquier momento, debe poder consultarse la información principal de la cuenta para conocer a quién pertenece, cómo se identifica y cuánto dinero tiene actualmente. Al iniciar una cuenta, puede definirse un saldo inicial, pero si no se especifica, se asume que empieza en cero.

**Tarea del estudiante:** leer el texto y determinar qué corresponde a clase, atributos, métodos y reglas.

---


