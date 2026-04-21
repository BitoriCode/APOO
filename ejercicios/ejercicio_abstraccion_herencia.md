# Taller — **Rental Ruedas: Sistema de Alquiler de Vehículos**

> **Duración estimada:** 2 horas (120 minutos)  
> **Temas que practica:** Herencia · Polimorfismo · Clases abstractas (ABC) · `super().__init__()`  
> **Prerrequisito:** Nota `herencia_y_polimorfismo.md`

---

## Contexto

*Rental Ruedas* es una empresa que alquila vehículos por días. Su catálogo tiene tres tipos de unidades: **autos**, **camionetas** y **motos**. El precio diario varía por tipo y cada uno tiene reglas distintas. Deben construir el sistema que gestiona el catálogo y las reservas, todo en un **único archivo Python**.

El objetivo del taller **no** es solo que el código funcione, sino demostrar dos cosas que hemos visto en clase:

1. **Herencia y abstracción** — una clase base `Vehiculo` con un contrato que todas las unidades deben cumplir.
2. **Polimorfismo** — el catálogo trabaja con `Vehiculo` sin saber si es auto, camioneta o moto.

---

## Reglas generales

* Todo en **un solo archivo**: `apellido_nombre_taller_rental.py`
* **Type hints** en todos los parámetros y valores de retorno.
* **Dinero** siempre en pesos enteros (`int`).
* **Sin `print()`** dentro de clases de dominio (ni en `Vehiculo` ni en `CatalogoRental`). Solo al final en el bloque de pruebas.
* **Sin herencia múltiple, mixins ni Protocol** — no los necesitan aquí.
* La clase base `Vehiculo` **debe ser abstracta** (`ABC`). No se puede instanciar directamente.
* Usar `super().__init__(...)` en todas las subclases.

---

## Excepciones — ya están dadas, solo úsenlas

Copien este bloque al inicio de su archivo. **No tienen que modificarlo**, solo entender cuándo lanzar cada una:

```python
# ── Excepciones del dominio ────────────────────────────────────────────────

class RentalError(Exception):
    """Base de todos los errores del dominio de Rental Ruedas."""

class VehiculoNoEncontrado(RentalError):
    """Se lanza cuando se busca una placa que no existe en el catálogo."""

class VehiculoNoDisponible(RentalError):
    """Se lanza cuando se intenta alquilar un vehículo ya reservado,
    o devolver uno que no estaba alquilado."""

class DiasInvalidos(RentalError):
    """Se lanza cuando se pide un alquiler por 0 días o menos."""

class PlacaDuplicada(RentalError):
    """Se lanza al intentar agregar al catálogo una placa ya existente."""
```

> 💡 Todas heredan de `RentalError`. Eso significa que con un solo `except RentalError` pueden atrapar cualquiera de ellas. Verán eso en el bloque de pruebas al final.

---

## Bloque 1 — Diseño en papel (0 – 20 min)

**Antes de escribir código**, respondan en papel o como comentarios en el archivo:

1. ¿Qué atributos son comunes a todos los vehículos? Escríbanlos.
2. ¿Qué método calcula el precio? ¿Su resultado varía según el tipo de vehículo? (Eso va abstracto.)
3. ¿Qué método puede estar implementado en el padre y funcionar para todos? (Eso va concreto.)
4. Dibujen la jerarquía: quién hereda de quién.

> 💡 No salten este bloque. En un examen les pediremos el diagrama antes del código.

---

## Bloque 2 — Clase abstracta `Vehiculo` (20 – 45 min)

### Paso 1 — Definir `Vehiculo`

La clase `Vehiculo` es la base de toda la jerarquía. Define el **contrato** que todos los vehículos deben cumplir.

#### Atributos (comunes a todos los vehículos)

| Atributo | Tipo | Restricción |
|----------|------|-------------|
| `placa` | `str` | No vacía; convertir a mayúsculas con `.upper()` |
| `marca` | `str` | No vacía |
| `modelo` | `str` | No vacío |
| `año` | `int` | Entre 2000 y 2026 (inclusive) |
| `disponible` | `bool` | `True` por defecto |

Si alguna restricción no se cumple, lanzar `ValueError` con un mensaje descriptivo. Ejemplo: `"La placa no puede estar vacía."`.

#### Métodos abstractos

Cada subclase los implementa a su manera. La clase padre solo declara que deben existir:

```python
@abstractmethod
def costo_por_dia(self) -> int:
    """Retorna el precio de alquiler por un día en pesos."""
    ...

@abstractmethod
def tipo(self) -> str:
    """Retorna el nombre del tipo: 'AUTO', 'CAMIONETA' o 'MOTO'."""
    ...
```

#### Métodos concretos

Estos van implementados en `Vehiculo` y **todas las subclases los heredan sin cambiar nada**:

```python
def calcular_costo_total(self, dias: int) -> int:
    """Retorna costo_por_dia() * dias."""
    ...

def resumen(self) -> str:
    """
    Retorna una línea con la información principal. Ejemplos:
    '[AUTO] ABC-123 — Toyota Corolla 2022 | $90,000/día | Disponible'
    '[MOTO] XYZ-999 — Honda CB500 2020 | $50,000/día | Reservada'
    """
    ...
```

> 💡 `resumen()` llama internamente a `tipo()` y `costo_por_dia()`. Aunque esos métodos son abstractos, cuando se ejecuta sobre una instancia concreta (`Auto`, `Moto`, etc.), Python sabe a cuál versión llamar. Eso es polimorfismo.

---

## Bloque 3 — Subclases (45 – 80 min)

Implementen las tres subclases. **Todas** deben:
- Llamar a `super().__init__(placa, marca, modelo, año)` como primera instrucción.
- Implementar `costo_por_dia()` y `tipo()`.

### `Auto(Vehiculo)`

**Atributo adicional:**
- `num_puertas: int` — número de puertas. Solo se permiten `2` o `4`; cualquier otro valor lanza `ValueError`.

**`costo_por_dia()`:** precio base **$85,000** más **$5,000** si tiene 4 puertas.

---

### `Camioneta(Vehiculo)`

**Atributo adicional:**
- `capacidad_carga_kg: int` — capacidad de carga en kilos (`> 0`; si es 0 o negativo, lanzar `ValueError`).

**`costo_por_dia()`:** precio base **$120,000** más **$500** por cada 100 kg de capacidad (usar división entera `//`).

> **Ejemplo:** 350 kg → $120,000 + (350 // 100) × $500 = $120,000 + 3 × $500 = **$121,500**

---

### `Moto(Vehiculo)`

**Atributo adicional:**
- `cilindrada: int` — cilindraje en cc (`> 0`; si no, lanzar `ValueError`).

**`costo_por_dia()`:**
- Hasta 200 cc inclusive: **$35,000**
- 201 cc hasta 600 cc inclusive: **$50,000**
- Más de 600 cc: **$70,000**

---

### Checkpoint de polimorfismo

Antes de continuar, instancien un objeto de cada tipo y llamen a `resumen()` con el mismo código para los tres:

```python
vehiculos: list[Vehiculo] = [
    Auto("ABC-123", "Toyota", "Corolla", 2022, num_puertas=4),
    Camioneta("DEF-456", "Ford", "Ranger", 2021, capacidad_carga_kg=800),
    Moto("GHI-789", "Honda", "CB500", 2020, cilindrada=500),
]

for v in vehiculos:
    print(v.resumen())  # mismo método, salida distinta por tipo → polimorfismo
```

Salida esperada:

```
[AUTO] ABC-123 — Toyota Corolla 2022 | $90,000/día | Disponible
[CAMIONETA] DEF-456 — Ford Ranger 2021 | $124,000/día | Disponible
[MOTO] GHI-789 — Honda CB500 2020 | $50,000/día | Disponible
```

> ¿El código del `for` menciona `Auto`, `Camioneta` o `Moto`? No. Solo habla de `Vehiculo`. Eso es exactamente lo que queremos.

---

## Bloque 4 — Catálogo y pruebas (80 – 110 min)

### Paso 4 — Clase `CatalogoRental`

Esta clase **no hereda de nadie**. Gestiona el catálogo de vehículos y las operaciones de alquiler.

#### Estado interno

```python
self._catalogo: dict[str, Vehiculo]  # placa → Vehiculo (iniciar vacío en __init__)
```

#### Métodos

**`agregar(vehiculo: Vehiculo) -> None`**  
Agrega el vehículo al catálogo usando su placa como clave.  
→ Si la placa ya existe: lanzar `PlacaDuplicada`.

---

**`buscar(placa: str) -> Vehiculo`**  
Retorna el vehículo con esa placa.  
→ Si no existe: lanzar `VehiculoNoEncontrado`.

---

**`alquilar(placa: str, dias: int) -> int`**  
Registra el alquiler y retorna el costo total.  
Validaciones en orden:
1. Si `dias <= 0` → lanzar `DiasInvalidos`.
2. Si la placa no existe → lanzar `VehiculoNoEncontrado`.
3. Si `disponible == False` → lanzar `VehiculoNoDisponible`.
4. Si todo está bien: poner `disponible = False`, retornar `calcular_costo_total(dias)`.

---

**`devolver(placa: str) -> None`**  
Registra la devolución.  
→ Si la placa no existe → lanzar `VehiculoNoEncontrado`.  
→ Si ya está disponible → lanzar `VehiculoNoDisponible("El vehículo no estaba alquilado.")`.  
→ Si todo está bien: poner `disponible = True`.

---

**`listar_disponibles() -> list[Vehiculo]`**  
Retorna solo los vehículos con `disponible == True`.

---

**`resumen_catalogo() -> str`**  
Retorna una línea por vehículo (usando `resumen()`), unidas con `\n`.

---

### Paso 5 — Pruebas al final del archivo

Agreguen al final del archivo (fuera de cualquier clase) el siguiente bloque de pruebas. Su solución debe producir exactamente esta salida:

```python
if __name__ == "__main__":
    catalogo = CatalogoRental()

    # 1. Agregar vehículos
    catalogo.agregar(Auto("ABC-123", "Toyota", "Corolla", 2022, num_puertas=4))
    catalogo.agregar(Camioneta("DEF-456", "Ford", "Ranger", 2021, capacidad_carga_kg=800))
    catalogo.agregar(Moto("GHI-789", "Honda", "CB500", 2020, cilindrada=500))

    # 2. Ver catálogo completo
    print("=== Catálogo ===")
    print(catalogo.resumen_catalogo())

    # 3. Alquilar un auto
    costo = catalogo.alquilar("ABC-123", 3)
    print(f"Alquiler Toyota Corolla por 3 días → ${costo:,}")

    # 4. Ver solo disponibles (quedan 2)
    print("\n=== Disponibles ===")
    for v in catalogo.listar_disponibles():
        print(f"  {v.resumen()}")

    # 5. Intentar alquilar el mismo (ya reservado)
    try:
        catalogo.alquilar("ABC-123", 1)
    except RentalError as e:
        print(f"\nError esperado → {e}")

    # 6. Devolver el auto
    catalogo.devolver("ABC-123")
    print(f"Devolución. Disponible ahora: {catalogo.buscar('ABC-123').disponible}")

    # 7. Días inválidos
    try:
        catalogo.alquilar("DEF-456", 0)
    except RentalError as e:
        print(f"Error esperado → {e}")

    # 8. Placa inexistente
    try:
        catalogo.buscar("ZZZ-000")
    except RentalError as e:
        print(f"Error esperado → {e}")
```

**Salida esperada:**

```
=== Catálogo ===
[AUTO] ABC-123 — Toyota Corolla 2022 | $90,000/día | Disponible
[CAMIONETA] DEF-456 — Ford Ranger 2021 | $124,000/día | Disponible
[MOTO] GHI-789 — Honda CB500 2020 | $50,000/día | Disponible
Alquiler Toyota Corolla por 3 días → $270,000

=== Disponibles ===
  [CAMIONETA] DEF-456 — Ford Ranger 2021 | $124,000/día | Disponible
  [MOTO] GHI-789 — Honda CB500 2020 | $50,000/día | Disponible

Error esperado → El vehículo 'ABC-123' no está disponible.
Devolución. Disponible ahora: True
Error esperado → Los días de alquiler deben ser mayores a 0.
Error esperado → No se encontró vehículo con placa 'ZZZ-000'.
```

---

## Bloque 5 — Extensión opcional (110 – 120 min)

Si terminaron antes, elijan **una** de las dos extensiones:

### Extensión A — Descuento por temporada baja

Agreguen a `Vehiculo` un método concreto:

```python
def aplicar_descuento(self, porcentaje: float, dias: int) -> int:
    """
    Retorna costo_por_dia() * dias con un descuento aplicado.
    porcentaje: valor entre 0.0 y 1.0 (ej: 0.15 = 15%).
    Si porcentaje está fuera de ese rango, lanzar ValueError.
    """
    ...
```

Y en `CatalogoRental`:

```python
def alquilar_con_descuento(self, placa: str, dias: int, descuento: float) -> int:
    """
    Igual que alquilar(), pero usa aplicar_descuento() en vez de calcular_costo_total().
    """
    ...
```

---

### Extensión B — Historial de movimientos

Agreguen a `CatalogoRental` un registro de todas las operaciones:

```python
self._historial: list[str]   # cada entrada: "ALQUILER | ABC-123 | 3 días | $270,000"
```

- `alquilar()` agrega una entrada al historial cuando tiene éxito.
- `devolver()` agrega una entrada al historial cuando tiene éxito.
- Nuevo método: `ver_historial() -> list[str]` que retorna una **copia** de la lista (no la lista interna directamente).

---

## Casos de aceptación — verifiquen antes de entregar

Pueden agregar estos `assert` al final del archivo para autocorregir:

```python
# Costos por día
assert Auto("P1", "R", "S", 2023, num_puertas=2).costo_por_dia() == 85_000
assert Auto("P2", "T", "C", 2022, num_puertas=4).costo_por_dia() == 90_000
assert Camioneta("P3", "F", "R", 2021, capacidad_carga_kg=800).costo_por_dia() == 124_000
assert Moto("P4", "Y", "F", 2021, cilindrada=125).costo_por_dia() == 35_000
assert Moto("P5", "H", "C", 2020, cilindrada=500).costo_por_dia() == 50_000
assert Moto("P6", "B", "G", 2023, cilindrada=750).costo_por_dia() == 70_000

# Costo total
assert Auto("P7", "T", "C", 2022, num_puertas=4).calcular_costo_total(3) == 270_000

# Vehiculo no instanciable
try:
    Vehiculo("X", "X", "X", 2020)  # type: ignore
    assert False, "Debió lanzar TypeError"
except TypeError:
    pass

# Servicio
cat = CatalogoRental()
cat.agregar(Auto("AAA-111", "Toyota", "Yaris", 2023, num_puertas=2))

try:
    cat.agregar(Auto("AAA-111", "Honda", "Civic", 2022, num_puertas=4))
    assert False
except PlacaDuplicada:
    pass

try:
    cat.alquilar("AAA-111", 0)
    assert False
except DiasInvalidos:
    pass

assert cat.alquilar("AAA-111", 2) == 170_000

try:
    cat.alquilar("AAA-111", 1)
    assert False
except VehiculoNoDisponible:
    pass

try:
    cat.buscar("ZZZ-999")
    assert False
except VehiculoNoEncontrado:
    pass

print("Todos los casos de aceptación pasaron ✓")
```

---

## Criterios de evaluación

| Criterio | Puntaje | Cómo se verifica |
|----------|---------|------------------|
| **`Vehiculo` es abstracta** — no se instancia directamente | 10 % | `Vehiculo(...)` lanza `TypeError` |
| **`super().__init__(...)` en cada subclase** — los atributos comunes se inicializan | 15 % | `placa`, `marca`, `modelo`, `año` existen en instancias de `Auto`, `Camioneta` y `Moto` |
| **`costo_por_dia()` correcto por tipo** — cada subclase aplica sus propias reglas | 25 % | `assert` del bloque de casos de aceptación |
| **`resumen()` sin duplicar código** — implementado solo en `Vehiculo`, usa `tipo()` y `costo_por_dia()` | 15 % | El método no aparece en las subclases |
| **Polimorfismo demostrado** — iteración sobre `list[Vehiculo]` sin `isinstance` | 15 % | El `for` del checkpoint no menciona `Auto`, `Camioneta` ni `Moto` |
| **Servicio con errores correctos** — cada método lanza la excepción adecuada | 20 % | `assert` del bloque de casos de aceptación |

---

## Preguntas de reflexión

Antes de entregar, asegúrense de poder responder estas:

1. Si mañana agregan `Bus` al sistema, ¿qué tienen que escribir? ¿Tienen que tocar `CatalogoRental`? ¿Por qué?
2. `resumen()` está en `Vehiculo` pero llama a `tipo()` y `costo_por_dia()` que son abstractos. ¿Cómo sabe Python a cuál versión llamar?
3. ¿Por qué `CatalogoRental` recibe `Vehiculo` en sus métodos en vez de `Auto`, `Camioneta` o `Moto`?
4. ¿Qué pasaría si en vez de `except RentalError` usaran `except VehiculoNoEncontrado` en el bloque de pruebas? ¿Seguirían capturando todos los errores?
5. ¿En qué se diferencia `calcular_costo_total()` de `costo_por_dia()`? ¿Por qué uno es concreto y el otro abstracto?
