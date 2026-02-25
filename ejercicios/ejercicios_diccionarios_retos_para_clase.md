## Práctica estudiantes — Diccionarios (para GitHub)

> **Reglas generales (para todos los retos)**
> 
* Usa `dict` para resolver.
* Puedes elegir entre:

  * **Versión manual:** `if clave in diccionario: ... else: ...`
  * **Versión con `.get()`:** `diccionario.get(clave, valor_por_defecto)`
* Evita `KeyError`.
* Imprime resultados en consola de forma clara.

---

### Reto A — Conteo de frecuencias

**Objetivo:** construir un diccionario de frecuencias `dict[str, int]`.

**Contexto:** tienes una lista con categorías repetidas (por ejemplo, categorías de gastos o etiquetas).

**Dataset:**

```python
categorias = [
    "Comida", "Transporte", "Comida", "Ocio", "Servicios",
    "Comida", "Salud", "Ocio", "Comida", "Educación",
    "Transporte", "Comida", "Servicios", "Ocio", "Salud",
]
```

**Tareas:**

1. Construye `frecuencias: dict[str, int]` donde cada clave sea una categoría y el valor sea cuántas veces aparece.
2. Imprime el diccionario resultante.
3. Imprime la categoría más frecuente y su conteo (si hay empate, muestra cualquiera).

**Pistas:**

* El patrón aquí es **conteo**.
* Si usas `.get()`, el default debe ser `0`.

---

### Reto B — Totales por categoría (acumulación)

**Objetivo:** construir un diccionario de totales `dict[str, float]`.

**Contexto:** tienes movimientos (categoría, valor). Varias categorías se repiten y debes sumar sus valores.

**Dataset:**

```python
movimientos = [
    ("Comida", 18000.0), ("Transporte", 9500.0), ("Comida", 22000.0),
    ("Ocio", 40000.0), ("Servicios", 89000.0), ("Transporte", 12000.0),
    ("Educación", 55000.0), ("Comida", 15500.0), ("Servicios", 42000.0),
]
```

**Tareas:**

1. Construye `totales: dict[str, float]` donde cada clave sea una categoría y el valor sea la suma total de sus movimientos.
2. Imprime el diccionario resultante.
3. Imprime la categoría con el total más alto y su valor.

**Pistas:**

* El patrón aquí es **acumulación**.
* Si usas `.get()`, el default debe ser `0.0`.

---

### Reto C — Mini-analizador con POO (diccionarios como núcleo)

**Objetivo:** usar POO básica para organizar datos, y diccionarios para generar reportes.

**Contexto:** vas a modelar gastos y construir un analizador que genere reportes por categoría y por método de pago.

**Dataset:**

```python
datos = [
    ("G001", "Comida", 18000.0, 1, "Tarjeta"),
    ("G002", "Transporte", 9500.0, 1, "Efectivo"),
    ("G003", "Comida", 22000.0, 2, "Efectivo"),
    ("G004", "Ocio", 40000.0, 2, "Tarjeta"),
    ("G005", "Servicios", 89000.0, 3, "Transferencia"),
    ("G006", "Comida", 15500.0, 5, "Tarjeta"),
]
```

**Requisitos de diseño:**

1. Crea una clase `Gasto` con atributos:

   * `codigo: str`
   * `categoria: str`
   * `valor: float`
   * `dia: int`
   * `metodo: str`

2. Crea una clase `AnalizadorGastos` con estos métodos:

   * `totales_por_categoria(gastos: list[Gasto]) -> dict[str, float]`
   * `conteo_por_metodo(gastos: list[Gasto]) -> dict[str, int]`

**Tareas:**

1. Convierte `datos` en una lista de objetos `Gasto`.
2. Calcula e imprime:

   * Totales por categoría (diccionario)
   * Conteo por método (diccionario)
3. Imprime el **Top 2** categorías por total (puedes ordenar una lista de tuplas).

**Pistas:**

* El patrón se repite: **conteo** (enteros, default `0`) y **acumulación** (floats, default `0.0`).
* Para Top 2: usa `list(diccionario.items())` y ordena por el valor.

---
