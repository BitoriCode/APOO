# Ejercicio — **Reestructurar un paquete: Catálogo de Películas**

> **Duración estimada:** 60 minutos  
> **Temas que practica:** Módulos · Paquetes · `__init__.py` · Imports relativos · `__name__` · `python -m`  
> **Prerrequisito:** `modulos_y_paquetes_en_python.md`

---

## Contexto

Un desarrollador creó el siguiente sistema para gestionar un catálogo de películas favoritas.
El programa funciona, pero **todo está en un único archivo** y el equipo ya no puede mantenerlo:
cada vez que alguien quiere importar solo las excepciones, tiene que cargar todo el archivo.

**Tu tarea:** tomar este código y reestructurarlo como un **paquete Python bien organizado**.

---

## Código inicial (un solo archivo)

```python
# catalogo_peliculas.py  ← el archivo que debes reestructurar

from dataclasses import dataclass, field
from operator import attrgetter


# ── Excepciones ──────────────────────────────────────────────────────────────

class CatalogoError(Exception):
    """Error base del catálogo."""

class PeliculaNoEncontrada(CatalogoError):
    """Se lanza cuando se busca un título que no existe."""

class PeliculaYaExiste(CatalogoError):
    """Se lanza cuando se intenta agregar un título duplicado."""


# ── Modelos ───────────────────────────────────────────────────────────────────

@dataclass
class Pelicula:
    titulo: str
    director: str
    anio: int
    genero: str
    puntuacion: float
    etiquetas: list[str] = field(default_factory=list)

    def __post_init__(self) -> None:
        if not self.titulo.strip():
            raise ValueError("El título no puede estar vacío")
        if not (0.0 <= self.puntuacion <= 10.0):
            raise ValueError(f"Puntuación {self.puntuacion} fuera del rango 0–10")
        if self.anio < 1888:
            raise ValueError(f"Año {self.anio} inválido (el cine nació en 1888)")
        self.etiquetas = [t.strip().lower() for t in self.etiquetas if t.strip()]


# ── Servicio ──────────────────────────────────────────────────────────────────

class Catalogo:
    def __init__(self) -> None:
        self._peliculas: dict[str, Pelicula] = {}

    def agregar(self, pelicula: Pelicula) -> None:
        clave = pelicula.titulo.lower()
        if clave in self._peliculas:
            raise PeliculaYaExiste(f"'{pelicula.titulo}' ya está en el catálogo")
        self._peliculas[clave] = pelicula

    def buscar(self, titulo: str) -> Pelicula:
        clave = titulo.strip().lower()
        if clave not in self._peliculas:
            raise PeliculaNoEncontrada(f"No se encontró '{titulo}'")
        return self._peliculas[clave]

    def por_genero(self, genero: str) -> list[Pelicula]:
        return [p for p in self._peliculas.values()
                if p.genero.lower() == genero.lower()]

    def top(self, n: int = 5) -> list[Pelicula]:
        todas = list(self._peliculas.values())
        todas.sort(key=attrgetter("puntuacion"), reverse=True)
        return todas[:n]

    def listar(self) -> list[Pelicula]:
        return list(self._peliculas.values())


# ── Código de ejecución (punto de entrada) ───────────────────────────────────

catalogo = Catalogo()

catalogo.agregar(Pelicula("El Padrino",       "Coppola",   1972, "Drama",   9.2))
catalogo.agregar(Pelicula("Interestelar",      "Nolan",     2014, "Sci-Fi",  8.6))
catalogo.agregar(Pelicula("Parásitos",         "Bong",      2019, "Drama",   8.5))
catalogo.agregar(Pelicula("El Gran Lebowski",  "Coen",      1998, "Comedia", 8.1,
                          etiquetas=["culto", "comedia", "noir"]))

print("=== Top 3 ===")
for p in catalogo.top(3):
    print(f"  {p.titulo} ({p.anio}) — {p.puntuacion}/10")

print("\n=== Dramas ===")
for p in catalogo.por_genero("Drama"):
    print(f"  {p.titulo}")

print("\n=== Búsqueda ===")
try:
    encontrada = catalogo.buscar("Parásitos")
    print(f"  Encontrada: {encontrada.titulo}, dir. {encontrada.director}")
    catalogo.buscar("Matrix")
except PeliculaNoEncontrada as e:
    print(f"  Error esperado: {e}")
```

---

## Tu tarea

### Parte 1 — Diseña la estructura (sin código) ✏️

Antes de tocar el editor, responde:

**1.1** ¿Qué responsabilidades distintas tiene el archivo actual?
Lista cada una en una línea.

```
Tu respuesta:



```

**1.2** Propón la estructura de carpetas y archivos del paquete.
¿Qué va en cada módulo? ¿Qué nombre le darías al paquete?

```
Tu propuesta:

nombre_paquete/
├── ...
├── ...
└── ...
```

**1.3** Dibuja el flujo de imports entre los módulos.
¿Quién importa a quién? ¿Hay algún módulo que no importe a nadie del paquete?

```
Tu diagrama:


```

---

### Parte 2 — Implementa el paquete

Crea la carpeta `peliculas/` con los módulos que diseñaste.

#### Reglas

- El paquete **debe llamarse `peliculas`**.
- Cada módulo tiene **una sola responsabilidad** (excepciones, modelos, servicio, punto de entrada).
- Todos los imports dentro del paquete deben ser **relativos** (`from .modulo import ...`).
- El código de ejecución (crear el `Catalogo`, agregar películas, imprimir) va en `main.py`,
  dentro de una función `main()` protegida por `if __name__ == "__main__":`.
- `__init__.py` puede estar vacío. Si lo rellenas, justifica qué reexportas y por qué.
- **Type hints** en todos los parámetros y retornos.

#### Estructura esperada

```
peliculas/
├── __init__.py
├── excepciones.py
├── modelos.py
├── servicio.py
└── main.py
```

---

### Parte 3 — Verifica que funciona

Ejecuta el paquete desde la carpeta que **contiene** `peliculas/`:

```bash
python -m peliculas.main
```

La salida debe ser exactamente:

```
=== Top 3 ===
  El Padrino (1972) — 9.2/10
  Interestelar (2014) — 8.6/10
  Parásitos (2019) — 8.5/10

=== Dramas ===
  El Padrino
  Parásitos

=== Búsqueda ===
  Encontrada: Parásitos, dir. Bong
  Error esperado: No se encontró 'Matrix'
```

Luego prueba deliberadamente el error que ocurre con el comando incorrecto:

```bash
python peliculas/main.py
```

**Pregunta:** ¿Qué error aparece y por qué?

```
Tu respuesta:


```

---

### Parte 4 — Preguntas de reflexión

Responde en el mismo archivo `.py` que quieras, como comentarios al final de `main.py`.

**4.1** ¿Qué módulo del paquete no tiene ningún import relativo?
¿Por qué tiene sentido que sea así?

**4.2** Si quisieras usar `Catalogo` desde un script externo `prueba.py`
(fuera de la carpeta `peliculas/`), ¿qué línea de import usarías?
Escribe exactamente esa línea.

**4.3** ¿Qué pasaría si mueves el código de ejecución fuera del guardián
`if __name__ == "__main__":`?
¿En qué situación concreta se notaría el problema?

---

## Reto adicional

Si terminas antes de tiempo, agrega a `servicio.py` un método:

```python
def por_etiqueta(self, etiqueta: str) -> list[Pelicula]: ...
```

que retorne todas las películas que tengan `etiqueta` en su lista `etiquetas`
(búsqueda case-insensitive).

Agrégalo al bloque `main()` con al menos un caso de prueba.
