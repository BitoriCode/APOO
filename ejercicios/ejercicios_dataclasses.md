
## Ejercicio 1 — Registro de Mascotas

**Objetivo:** practicar la definición básica de una dataclass con campos, valores por defecto y `__post_init__`.

### Enunciado

Crea una dataclass llamada `Mascota` que represente el registro de una mascota en una veterinaria.

**Campos requeridos:**

- `nombre: str` — nombre de la mascota
- `especie: str` — por ejemplo: "Perro", "Gato", "Conejo"
- `edad: int` — edad en años
- `vacunada: bool` — con valor por defecto `False`

**Validaciones en `__post_init__`:**

- `nombre` no puede estar vacío (usar `.strip()`)
- `especie` no puede estar vacía
- `edad` debe ser mayor o igual a 0

**Método adicional:**

- `descripcion(self) -> str` que retorne un texto como: `"Luna (Gato, 3 años) - Vacunada"` o `"Luna (Gato, 3 años) - No vacunada"` según corresponda.

**Pruebas sugeridas:**

```python
m1 = Mascota("Luna", "Gato", 3, True)
m2 = Mascota("Rocky", "Perro", 5)

print(m1)               # repr automático
print(m1.descripcion())  # Luna (Gato, 3 años) - Vacunada
print(m2.descripcion())  # Rocky (Perro, 5 años) - No vacunada
print(m1 == m2)          # False

# Esto debería lanzar ValueError:
m3 = Mascota("", "Gato", 2)
```

---

## Ejercicio 2 — Inventario de Libros

**Objetivo:** practicar `field(default_factory=...)` para listas, campos calculados con `init=False`, y `__post_init__`.

### Enunciado

Crea una dataclass llamada `Libro` que represente un libro en el inventario de una librería.

**Campos requeridos:**

- `titulo: str`
- `autor: str`
- `precio_base: float` — precio sin impuestos
- `cantidad: int` — unidades en stock
- `etiquetas: list[str]` — lista de etiquetas (por ejemplo: `["ficción", "aventura"]`). Debe tener su propia lista por instancia usando `field(default_factory=list)`.
- `valor_total: float` — campo **calculado** (no se pasa en el `__init__`). Se calcula en `__post_init__` como `precio_base * cantidad`.

> **Pista:** para que `valor_total` no aparezca en el `__init__`, usa `field(init=False)`.

**Validaciones en `__post_init__`:**

- `titulo` y `autor` no pueden estar vacíos
- `precio_base` debe ser mayor a 0
- `cantidad` debe ser mayor o igual a 0

**Métodos adicionales:**

- `agregar_etiqueta(self, etiqueta: str) -> None` — agrega una etiqueta a la lista (solo si no está repetida, comparar en minúsculas).
- `tiene_stock(self) -> bool` — retorna `True` si `cantidad > 0`.

**Pruebas sugeridas:**

```python
libro1 = Libro("Cien Años de Soledad", "García Márquez", 45000.0, 10)
libro2 = Libro("El Principito", "Saint-Exupéry", 30000.0, 5, ["infantil", "clásico"])

print(libro1)
print(libro1.valor_total)  # 450000.0
print(libro2.etiquetas)    # ['infantil', 'clásico']

libro1.agregar_etiqueta("novela")
libro1.agregar_etiqueta("Novela")  # no debería duplicarse
print(libro1.etiquetas)            # ['novela']

print(libro1.tiene_stock())  # True

# Esto debería lanzar ValueError:
libro_malo = Libro("", "Autor", 10000.0, 5)
```

---

## Ejercicio 3 — Notas de Estudiantes (dataclass inmutable)

**Objetivo:** practicar `frozen=True` y la función `replace()` del módulo dataclasses.

### Enunciado

Crea una dataclass **inmutable** (usando `frozen=True`) llamada `NotaEstudiante` que represente la nota de un estudiante en una materia.

**Campos requeridos:**

- `estudiante: str` — nombre del estudiante
- `materia: str` — nombre de la materia
- `nota: float` — calificación (entre 0.0 y 5.0)

**Validaciones en `__post_init__`:**

- `estudiante` y `materia` no pueden estar vacíos
- `nota` debe estar entre 0.0 y 5.0 (inclusive)

**Método adicional:**

- `aprobo(self) -> bool` — retorna `True` si `nota >= 3.0`.

**Lo que deben explorar:**

1. Crear una instancia y verificar que **no se puede modificar** directamente (intentar `n.nota = 4.5` debería lanzar un error).
2. Usar `replace()` del módulo `dataclasses` para crear una **copia** con la nota corregida.

> **Pista:** `replace()` se importa así: `from dataclasses import dataclass, replace`

**Pruebas sugeridas:**

```python
from dataclasses import replace

n1 = NotaEstudiante("Carlos", "Programación", 4.2)
print(n1)
print(n1.aprobo())  # True

# Esto debería lanzar FrozenInstanceError:
# n1.nota = 5.0

# Usar replace para "corregir" la nota:
n2 = replace(n1, nota=4.8)
print(n2)            # NotaEstudiante(estudiante='Carlos', materia='Programación', nota=4.8)
print(n1.nota)       # 4.2 — el original no cambió
print(n2.nota)       # 4.8

# Esto debería lanzar ValueError (nota fuera de rango):
n_mala = NotaEstudiante("Ana", "Cálculo", 5.5)
```

---

## Ejercicio 4 — Registro de Tareas Pendientes

**Objetivo:** integrar varios conceptos: dataclass con `field()`, `__post_init__`, valores por defecto, y usar una **clase contenedora normal** que gestione una lista de dataclasses.

### Enunciado

#### Parte A — Dataclass `Tarea`

Crea una dataclass `Tarea` con los siguientes campos:

- `id: int`
- `titulo: str`
- `descripcion: str`
- `completada: bool` — valor por defecto `False`

**Validaciones en `__post_init__`:**

- `titulo` no puede estar vacío
- `id` debe ser mayor a 0

**Método adicional:**

- `marcar_completada(self) -> None` — cambia `completada` a `True`.

#### Parte B — Clase `GestorTareas`

Crea una clase **normal** (no dataclass) llamada `GestorTareas` con:

- **Atributo:** `tareas: list[Tarea]` (inicia como lista vacía)

- **Métodos:**
  - `agregar(tarea: Tarea) -> None` — agrega una tarea. Si ya existe una tarea con el mismo `id`, lanza `ValueError`.
  - `eliminar(id_tarea: int) -> None` — elimina la tarea con ese `id`. Si no existe, lanza `ValueError`.
  - `pendientes() -> list[Tarea]` — retorna una lista con las tareas donde `completada == False`.
  - `completadas() -> list[Tarea]` — retorna una lista con las tareas donde `completada == True`.
  - `buscar(id_tarea: int) -> Tarea` — retorna la tarea con ese `id`. Si no existe, lanza `ValueError`.

- **Dunder:** `__repr__` que muestre algo como `"GestorTareas(3 tareas)"`.

**Pruebas sugeridas:**

```python
gestor = GestorTareas()
gestor.agregar(Tarea(1, "Estudiar dataclasses", "Leer la teoría y hacer ejercicios"))
gestor.agregar(Tarea(2, "Hacer mercado", "Comprar frutas y verduras"))
gestor.agregar(Tarea(3, "Ejercicio de Python", "Resolver el ejercicio 5"))

print(gestor)  # GestorTareas(3 tareas)

# Marcar una tarea como completada
tarea = gestor.buscar(1)
tarea.marcar_completada()

print(len(gestor.pendientes()))    # 2
print(len(gestor.completadas()))   # 1

# Eliminar una tarea
gestor.eliminar(2)
print(gestor)  # GestorTareas(2 tareas)

# Esto debería lanzar ValueError (id duplicado):
gestor.agregar(Tarea(1, "Duplicada", "No debería funcionar"))

# Esto debería lanzar ValueError (id inválido):
t_mala = Tarea(0, "Tarea inválida", "El id debe ser > 0")
```
