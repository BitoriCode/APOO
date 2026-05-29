# Ejercicio: Inventario de Productos

---

## Descripción del problema

El dueño de una tienda pequeña te pide ayuda para organizar su inventario. Actualmente lleva todo en papel y quiere pasarlo a un sistema en Python.

Te cuenta lo siguiente:

> *"Cada producto que tenemos en bodega tiene un código que lo identifica, un nombre, el precio unitario y cuántas unidades hay disponibles. Necesito poder registrar productos nuevos, pero si alguien intenta ingresar un código que ya existe, el sistema debería avisarme. También quiero poder buscar un producto por su código, saber cuáles están agotados y conocer cuánto vale en total todo lo que tengo en bodega. Eso sí, un precio negativo o una cantidad negativa no tiene sentido, así que el sistema no debería permitirlo."*

---

## Análisis del problema

Antes de escribir cualquier línea de código, responde estas preguntas:

1. ¿Qué **entidades** (sustantivos del dominio) puedes identificar en la descripción?
2. ¿Qué **información** caracteriza a cada entidad?
3. ¿Qué **comportamientos o consultas** se mencionan? ¿A qué entidad le corresponde cada uno?
4. ¿Qué **reglas de negocio** deben validarse y en qué momento?
5. ¿Cómo se **relacionan** las entidades entre sí?

---

## Tu tarea

### Parte 1 — Diagrama de clases

Propone el diagrama de clases  que represente las entidades con sus atributos, métodos y la relación entre ellas.


---

### Parte 2 — Implementación

Implementa en Python las clases que identificaste en tu análisis. Usa type hints y valida las reglas de negocio.


---

## Casos de prueba

El siguiente código debe ejecutarse **sin errores** y producir las salidas indicadas en los comentarios:

```python
# --- Creación de productos válidos ---
p1 = Producto(codigo="A001", nombre="Arroz", precio=3500.0, cantidad=10)
p2 = Producto(codigo="A002", nombre="Aceite", precio=8900.0, cantidad=0)
p3 = Producto(codigo="A003", nombre="Sal", precio=1200.0, cantidad=5)

print(p1.valor_total())   # 35000.0
print(p2.tiene_stock())   # False
print(p3.tiene_stock())   # True

# --- Inventario ---
inv = Inventario()
inv.agregar_producto(p1)
inv.agregar_producto(p2)
inv.agregar_producto(p3)

print(inv.buscar_producto("A001"))   # Producto(codigo='A001', ...)
print(inv.buscar_producto("X999"))   # None

sin_stock = inv.listar_sin_stock()
print(len(sin_stock))                # 1
print(sin_stock[0].nombre)          # Aceite

print(inv.valor_total_inventario())  # 41000.0  (35000 + 0 + 6000)

# --- Validaciones (deben lanzar ValueError) ---
try:
    Producto(codigo="", nombre="Test", precio=100.0, cantidad=1)
except ValueError as e:
    print("Error esperado:", e)   # Error esperado: ...

try:
    Producto(codigo="B001", nombre="Test", precio=-50.0, cantidad=1)
except ValueError as e:
    print("Error esperado:", e)   # Error esperado: ...

try:
    inv.agregar_producto(p1)      # codigo "A001" ya existe
except ValueError as e:
    print("Error esperado:", e)   # Error esperado: ...
```

---

> **Nota:** Propone la solución completa desde cero. No hay una única respuesta correcta; lo importante es que los casos de prueba pasen y el diseño respete los principios de encapsulamiento y responsabilidad única.
