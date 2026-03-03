
# Trabajo en clase

## Reglas
- Cada punto debe resolverse usando **comprehensions** (list o dict).
- Se permite usar funciones básicas: `sum`, `min`, `max`, `len`, `sorted`, `set` (solo si el punto lo pide), y métodos de string como `strip`, `lower`, `upper`, `replace`.
- Evite `for` tradicional (la idea es practicar comprehensions).

---

## Dataset base (cópialo tal cual)
```python
gastos = [
    {"id": "G001", "categoria": "comida",        "valor": 18000, "dia": 1,  "metodo": "tarjeta",       "lugar": "Cafeteria",     "nota": "almuerzo"},
    {"id": "G002", "categoria": "transporte",    "valor":  4200, "dia": 1,  "metodo": "tarjeta",       "lugar": "Metro",         "nota": "pasaje"},
    {"id": "G003", "categoria": "servicios",     "valor": 65000, "dia": 2,  "metodo": "transferencia", "lugar": "EPM",           "nota": "energia"},
    {"id": "G004", "categoria": "comida",        "valor":  9500, "dia": 2,  "metodo": "efectivo",      "lugar": "Panaderia",     "nota": "onces"},
    {"id": "G005", "categoria": "entretenimiento","valor": 28000,"dia": 3,  "metodo": "tarjeta",       "lugar": "Cine",          "nota": "entrada"},
    {"id": "G006", "categoria": "transporte",    "valor": 12000, "dia": 3,  "metodo": "efectivo",      "lugar": "Taxi",          "nota": "carrera"},
    {"id": "G007", "categoria": "educacion",     "valor": 35000, "dia": 4,  "metodo": "transferencia", "lugar": "Plataforma",    "nota": "curso"},
    {"id": "G008", "categoria": "salud",         "valor": 16000, "dia": 4,  "metodo": "tarjeta",       "lugar": "Farmacia",      "nota": "medicina"},
    {"id": "G009", "categoria": "comida",        "valor": 22000, "dia": 5,  "metodo": "tarjeta",       "lugar": "Restaurante",   "nota": "cena"},
    {"id": "G010", "categoria": "hogar",         "valor":  8700, "dia": 5,  "metodo": "efectivo",      "lugar": "Tienda",        "nota": "aseo"},
    {"id": "G011", "categoria": "servicios",     "valor": 54000, "dia": 6,  "metodo": "transferencia", "lugar": "Claro",         "nota": "internet"},
    {"id": "G012", "categoria": "transporte",    "valor":  4200, "dia": 6,  "metodo": "tarjeta",       "lugar": "Metro",         "nota": "pasaje"},
    {"id": "G013", "categoria": "entretenimiento","valor": 15000,"dia": 7,  "metodo": "efectivo",      "lugar": "Parque",        "nota": "snacks"},
    {"id": "G014", "categoria": "comida",        "valor":  7600, "dia": 7,  "metodo": "efectivo",      "lugar": "Cafeteria",     "nota": "tinto"},
    {"id": "G015", "categoria": "hogar",         "valor": 39000, "dia": 8,  "metodo": "tarjeta",       "lugar": "Supermercado",  "nota": "mercado"},
]
````

---

# Parte A — List Comprehension (8)

### 1) Lista de valores

Construya una lista con **solo los valores** de todos los gastos.

### 2) Lista de lugares (en mayúscula)

Construya una lista con los `lugar` en **mayúscula**.

### 3) Gastos pagados con tarjeta (ids)

Construya una lista con los `id` de los gastos cuyo `metodo` sea `"tarjeta"`.

### 4) Valores de transporte

Construya una lista con los `valor` de los gastos cuya `categoria` sea `"transporte"`.

### 5) Resumen de cada gasto (string)

Construya una lista de strings con el formato:
`"D{dia} | {categoria} | ${valor} | {lugar}"`

> Ejemplo: `"D1 | comida | $18000 | Cafeteria"`

### 6) Etiquetar gasto (ternaria)

Construya una lista con `"ALTO"` si `valor >= 30000`, si no `"NORMAL"` (en el mismo orden del dataset).

### 7) Aplicar recargo por método

Construya una lista con el **valor final** donde:

* si `metodo == "tarjeta"` entonces valor final = `valor * 1.02` (2% recargo)
* en otro caso valor final = `valor`

> Puede usar `int(...)` si quiere manejar enteros.

### 8) Filtrar “comida” y normalizar nota

Construya una lista con las `nota` de los gastos de `"comida"`, dejando cada nota en `strip().lower()`.

---

# Parte B — Dict Comprehension (8)

### 9) Índice por id → gasto

Construya un diccionario `{id: gasto}` (donde `gasto` es el diccionario completo del dataset).

### 10) id → valor

Construya un diccionario `{id: valor}`.

### 11) id → “permitido/no permitido”

Construya un diccionario `{id: "OK" o "REVISAR"}` donde:

* `"REVISAR"` si `valor > 50000`
* `"OK"` en caso contrario

### 12) categoria → total gastado

Construya un diccionario `{categoria: total}` donde `total` sea la suma de `valor` de esa categoría.

> Pista: use `sum(...)` y dentro un `for` (generator) filtrando por categoría.

### 13) metodo → cantidad de gastos

Construya un diccionario `{metodo: cantidad}` con cuántos gastos hay por método.

### 14) dia → total gastado en el día

Construya un diccionario `{dia: total_del_dia}` sumando los valores que correspondan a cada día.

### 15) categoria → gasto máximo

Construya un diccionario `{categoria: max_valor}` donde `max_valor` sea el gasto más alto de esa categoría.

### 16) categoria → promedio (redondeado a int)

Construya un diccionario `{categoria: promedio}` donde promedio sea `int(total / cantidad)`.

---

# Parte C — Mixtos (4)

### 17) Lista de ids “entretenimiento” ordenados por valor (menor a mayor)

Construya una lista con los `id` de la categoría `"entretenimiento"`, ordenados por `valor`.

> Nota: puede usar `sorted(...)` con `key=...` y una comprehension.

### 18) “Top 3” gastos (id, valor)

Construya una lista con tuplas `(id, valor)` de los **3 gastos más altos**.

### 19) Detectar días “caros”

Construya una lista con los `dia` donde el total gastado del día sea `>= 50000`.

> Esto conecta con el dict del punto 14 (pero aquí hazlo con comprehension).

### 20) Presupuesto restante por categoría (reto)

Suponga que el presupuesto del mes por categoría es:

```python
presupuesto = {
    "comida": 120000,
    "transporte": 60000,
    "servicios": 130000,
    "entretenimiento": 80000,
    "educacion": 70000,
    "salud": 50000,
    "hogar": 90000,
}
```

Construya un diccionario `{categoria: restante}` donde `restante = presupuesto[categoria] - total_gastado_categoria`.

> Si una categoría del presupuesto no aparece en `gastos`, entonces su total gastado es 0.

