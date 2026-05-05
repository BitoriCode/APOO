# GastoTrack — Ejercicio guiado (paso → solución)


---

## Imports comunes

```python
from dataclasses import dataclass, field
from datetime import date
```

---

## Paso 1 — Crear la dataclass `Gasto`

**Pide:**
Define `@dataclass Gasto` con campos públicos:

* `id: int`, `descripcion: str`, `monto: float`, `categoria: str`
* `fecha: date` con valor por defecto **hoy** usando `field(default_factory=date.today)`

**Solución:**

```python
@dataclass
class Gasto:
    id: int
    descripcion: str
    monto: float
    categoria: str
    fecha: date = field(default_factory=date.today)
```

---

## Paso 2 — Validación con `__post_init__`

**Pide:**
Valida al construir un `Gasto`:

* `descripcion` no vacía
* `monto > 0`
* `categoria` no vacía
  Si algo falla, lanza `ValueError` con un mensaje claro.

**Solución:**

```python
    def __post_init__(self) -> None:
        if not self.descripcion.strip():
            raise ValueError("La descripción no puede estar vacía")
        if self.monto <= 0:
            raise ValueError("El monto debe ser > 0")
        if not self.categoria.strip():
            raise ValueError("La categoría no puede estar vacía")
```

---

## Paso 3 — Clase contenedora `GestorGastos` (atributos públicos)

**Pide:**
Crea una clase normal **sin herencia**:

* Atributo **público**: `gastos: list[Gasto] = []`
* Métodos:

  * `agregar(g: Gasto) -> None` (rechazar id duplicado; **sin** `any`)
  * `eliminar(id_gasto: int) -> None` (**sin** `enumerate`)
  * `listar() -> list[Gasto]` (devuelve copia)
* Único dunder: `__repr__(self) -> str` (p.ej. `"GestorGastos(5 gastos)"`)

**Solución:**

```python
class GestorGastos:
    def __init__(self) -> None:
        self.gastos: list[Gasto] = []  # público

    def agregar(self, g: Gasto) -> None:
        # sin 'any': verificación explícita
        for x in self.gastos:
            if x.id == g.id:
                raise ValueError(f"Ya existe un gasto con id={g.id}")
        self.gastos.append(g)

    def eliminar(self, id_gasto: int) -> None:
        # sin 'enumerate': usar índice por rango
        for i in range(len(self.gastos)):
            if self.gastos[i].id == id_gasto:
                self.gastos.pop(i)
                return
        raise ValueError(f"No existe gasto con id={id_gasto}")

    def listar(self) -> list[Gasto]:
        return list(self.gastos)  # copia superficial

    def __repr__(self) -> str:
        return f"GestorGastos({len(self.gastos)} gastos)"
```

---

## Paso 4 — Consultas con comprehensions

**Pide:**
Implementa en `GestorGastos`:

* `filtrar_por_categoria(cat: str) -> list[Gasto]` (case-insensitive)
* `total() -> float` (suma de montos)
* `total_por_categoria() -> dict[str, float]` (**dict comprehension**)
* `top_n(n: int) -> list[Gasto]` (n mayores montos)

**Solución:**

```python
    def filtrar_por_categoria(self, cat: str) -> list[Gasto]:
        cat = cat.lower().strip()
        return [g for g in self.gastos if g.categoria.lower() == cat]

    def total(self) -> float:
        return sum(g.monto for g in self.gastos)

    def total_por_categoria(self) -> dict[str, float]:
        categorias = {g.categoria for g in self.gastos}
        return {c: sum(g.monto for g in self.gastos if g.categoria == c) for c in categorias}

    def top_n(self, n: int) -> list[Gasto]:
        return sorted(self.gastos, key=lambda g: g.monto, reverse=True)[:max(0, n)]
```

---

## Paso 5 — Funciones utilitarias (type hints + comprehensions)

**Pide:**
Fuera de la clase, define:

1. `buscar_texto(gestor: GestorGastos, texto: str) -> list[Gasto]`
   Devuelve los gastos cuya **descripción** contenga `texto` (case-insensitive).

2. `resumen_diario(gestor: GestorGastos) -> dict[date, float]`
   Mapa `fecha → total gastado ese día`.

3. `promedio_por_categoria(gestor: GestorGastos) -> dict[str, float]`
   Total y conteo por categoría; devuelve promedio.

**Solución:**

```python
def buscar_texto(gestor: GestorGastos, texto: str) -> list[Gasto]:
    t = texto.lower().strip()
    return [g for g in gestor.listar() if t in g.descripcion.lower()]

def resumen_diario(gestor: GestorGastos) -> dict[date, float]:
    dias = {g.fecha for g in gestor.listar()}
    return {d: sum(g.monto for g in gestor.listar() if g.fecha == d) for d in dias}

def promedio_por_categoria(gestor: GestorGastos) -> dict[str, float]:
    totales: dict[str, float] = {}
    conteos: dict[str, int] = {}
    for g in gestor.listar():
        if g.categoria in totales:
            totales[g.categoria] += g.monto
            conteos[g.categoria] += 1
        else:
            totales[g.categoria] = g.monto
            conteos[g.categoria] = 1
    return {c: (totales[c] / conteos[c]) for c in totales}
```

---

## Paso 6 — Demo mínima (prueba rápida)

**Pide:**
Crea algunos datos, ejecútalo y muestra resultados clave.

**Solución:**

```python

gg = GestorGastos()
gg.agregar(Gasto(1, "Almuerzo menú", 6.5, "Comida"))
gg.agregar(Gasto(2, "Bus ida", 1.2, "Transporte"))
gg.agregar(Gasto(3, "Cine", 5.0, "Ocio"))
gg.agregar(Gasto(4, "Supermercado", 18.3, "Comida"))
gg.agregar(Gasto(5, "Bus vuelta", 1.2, "Transporte"))

print(gg)  # -> GestorGastos(5 gastos)

# Acceso directo al atributo público
for g in gg.gastos:
    print(g)  # repr auto de dataclass

print("\nTotal general: $", gg.total())
print("Por categoría:", gg.total_por_categoria())
print("Top 3:", gg.top_n(3))

print("\nBuscar 'bus':")
for g in buscar_texto(gg, "bus"):
    print("-", g)

print("\nResumen diario:", resumen_diario(gg))
print("Promedio por categoría:", promedio_por_categoria(gg))
```