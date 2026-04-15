# Módulos y Paquetes en Python

## ¿Qué es un módulo?

Empecemos por lo más simple: en Python, **un módulo es un archivo `.py`**. Así de sencillo. Si tienen un archivo llamado `matematicas.py`, eso ya es un módulo. Pueden importarlo desde otro archivo usando `import matematicas` y acceder a todo lo que definieron adentro: funciones, clases, variables, constantes.

¿Por qué importa esto? Porque los módulos son el **mecanismo fundamental** que Python ofrece para organizar código. En la nota de modularización y abstracción hablamos de modularización como concepto de diseño. Ahora vamos a hablar de la **herramienta concreta** que Python nos da para implementar esa modularización: el sistema de módulos y paquetes.

Todo lo que han importado alguna vez (`import math`, `from dataclasses import dataclass`, `from abc import ABC, abstractmethod`) funciona gracias a este sistema. Es hora de entender cómo funciona por dentro.

---

## Analogías del mundo real

### Un módulo es como un libro en una biblioteca

Imaginen una biblioteca. Cada libro trata de un tema específico: uno de cocina, otro de historia, otro de matemáticas. Cuando necesitan información sobre un tema, van y buscan el libro correspondiente. No necesitan leer toda la biblioteca, solo el libro que les interesa.

En Python, cada archivo `.py` es como un libro: contiene conocimiento (funciones, clases, variables) sobre un tema específico. Cuando necesitan algo, lo importan. Y al igual que en una biblioteca, es importante que cada libro (módulo) esté bien organizado y trate de un solo tema.

### Un paquete es como una estantería o sección de la biblioteca

En una biblioteca, los libros no están regados por ahí. Están organizados por secciones: ficción, ciencias, historia. Cada sección agrupa libros relacionados.

Un **paquete** en Python es exactamente eso: una carpeta que agrupa módulos (archivos `.py`) relacionados. Si la biblioteca es el proyecto, las secciones son los paquetes y los libros son los módulos.

---

## Creando y usando módulos

### El módulo más simple

Crear un módulo es tan fácil como crear un archivo Python. Supongamos que crean un archivo llamado `saludos.py`:

```python
# saludos.py

def hola(nombre: str) -> str:
    return f"¡Hola, {nombre}!"

def despedida(nombre: str) -> str:
    return f"¡Hasta luego, {nombre}!"

SALUDO_DEFAULT = "Hola, mundo"
```

Ya tienen un módulo. Desde otro archivo en la misma carpeta pueden usarlo:

```python
# app.py
import saludos

print(saludos.hola("María"))        # → ¡Hola, María!
print(saludos.despedida("Carlos"))   # → ¡Hasta luego, Carlos!
print(saludos.SALUDO_DEFAULT)        # → Hola, mundo
```

Noten que al hacer `import saludos`, acceden a todo lo que hay en el módulo usando el nombre del módulo como prefijo: `saludos.hola()`, `saludos.SALUDO_DEFAULT`.

---

## Las formas de importar

Python ofrece varias formas de importar un módulo o partes de él. Cada una tiene su propósito:

### `import modulo`

Importa el módulo completo. Para acceder a sus contenidos usan `modulo.nombre`:

```python
import math

print(math.sqrt(16))   # → 4.0
print(math.pi)         # → 3.141592653589793
```

**Ventaja:** Queda claro de dónde viene cada cosa. Cuando ven `math.sqrt`, saben que `sqrt` viene del módulo `math`.

**Desventaja:** Puede ser verboso si usan muchas funciones del mismo módulo.

### `from modulo import nombre`

Importa elementos específicos directamente al espacio de nombres actual:

```python
from math import sqrt, pi

print(sqrt(16))   # → 4.0
print(pi)         # → 3.141592653589793
```

**Ventaja:** Más conciso. No necesitan escribir `math.` cada vez.

**Desventaja:** Si importan `sqrt` de un módulo y `sqrt` de otro, se sobreescriben. Además, al leer el código más adelante puede no ser obvio de dónde viene `sqrt`.

### `from modulo import nombre as alias`

Importa con un nombre alternativo:

```python
from math import sqrt as raiz_cuadrada

print(raiz_cuadrada(25))  # → 5.0
```

Se usa cuando hay conflictos de nombres o cuando el nombre original es demasiado largo.

### `import modulo as alias`

Renombra el módulo completo:

```python
import math as m

print(m.sqrt(9))  # → 3.0
```

Es común en la comunidad Python. Seguramente han visto `import numpy as np` o `import pandas as pd`. Esas son convenciones establecidas por las comunidades de esas bibliotecas.

### `from modulo import *` — **No lo hagan**

```python
from math import *  # Importa TODO lo que hay en math

print(sqrt(16))   # → 4.0
print(pi)         # → 3.141592653589793
```

Esto importa **todos** los nombres públicos del módulo al espacio actual. ¿El problema? No saben qué se importó. Pueden tener colisiones de nombres sin darse cuenta. Si dos módulos definen una función con el mismo nombre, el último en ser importado gana y el otro desaparece silenciosamente.

> 💡 **Regla:** Nunca usen `from modulo import *` en código de producción. Es aceptable solo en una sesión interactiva del intérprete para explorar rápidamente.

---

## ¿Cómo encuentra Python los módulos?

Cuando escriben `import saludos`, Python no busca mágicamente en todo el disco duro. Tiene un **orden de búsqueda** bien definido:

1. **El directorio actual** (o el directorio del script que se está ejecutando)
2. **Los directorios listados en la variable de entorno `PYTHONPATH`** (si está definida)
3. **Los directorios de instalación** de la librería estándar y de paquetes de terceros (site-packages)

Todo esto se puede inspeccionar con `sys.path`:

```python
import sys

for ruta in sys.path:
    print(ruta)
```

Esto imprime la lista de directorios donde Python busca módulos, en orden de prioridad. Si su módulo no está en ninguno de esos directorios, Python lanza un `ModuleNotFoundError`.

> 💡 **Nota:** No es recomendable modificar `sys.path` manualmente en su código. Si necesitan que Python encuentre sus módulos, organicen su proyecto como un **paquete** (que es lo que vamos a ver a continuación).

---

## Paquetes: organizando módulos en carpetas

### ¿Qué es un paquete?

Un **paquete** es simplemente una **carpeta que contiene módulos Python** y un archivo especial llamado `__init__.py`. Ese archivo le dice a Python: *"esta carpeta no es una carpeta cualquiera, es un paquete Python y puedes importar cosas de aquí"*.

Veamos la estructura de un paquete:

```
mi_paquete/
├── __init__.py      # Marca la carpeta como paquete
├── modelos.py       # Un módulo
├── excepciones.py   # Otro módulo
└── servicio.py      # Otro módulo
```

Ahora pueden importar desde ese paquete:

```python
from mi_paquete.modelos import Producto
from mi_paquete.excepciones import ProductoNoEncontrado
from mi_paquete.servicio import Tienda
```

### El archivo `__init__.py`

Este archivo puede estar **vacío** o tener contenido. En los ejemplos del curso (la máquina expendedora, la boletería de cine) lo hemos dejado vacío. Eso está perfectamente bien. Su sola presencia ya cumple la función de marcar la carpeta como paquete.

**`__init__.py` vacío:**

```python
# __init__.py
# (vacío — solo marca la carpeta como paquete)
```

Pero si quieren, pueden usarlo para:

**Reexportar nombres importantes:**

```python
# __init__.py
from .modelos import Producto
from .excepciones import ProductoNoEncontrado, StockInsuficiente
from .servicio import Tienda
```

Esto permite que los usuarios del paquete hagan:

```python
from mi_paquete import Producto, Tienda  # Más simple
```

En vez de:

```python
from mi_paquete.modelos import Producto
from mi_paquete.servicio import Tienda
```

Para este curso, lo dejamos vacío por simplicidad. En proyectos más grandes, definir qué se exporta en `__init__.py` es una herramienta poderosa para diseñar la API pública del paquete.

---

## Imports relativos vs. absolutos

Cuando están **dentro de un paquete** e importan de un módulo del mismo paquete, tienen dos opciones:

### Import absoluto

Usa el nombre completo del paquete desde la raíz:

```python
# servicio.py (dentro de mi_paquete/)
from mi_paquete.excepciones import ProductoNoEncontrado
from mi_paquete.modelos import Producto
```

### Import relativo

Usa un punto (`.`) para referirse al paquete actual:

```python
# servicio.py (dentro de mi_paquete/)
from .excepciones import ProductoNoEncontrado
from .modelos import Producto
```

El punto `.` significa "el paquete donde estoy". Dos puntos `..` significarían "el paquete padre" (un nivel arriba).

**¿Cuál usar?** En los ejemplos del curso usamos **imports relativos** (`from .excepciones import ...`), y esa es nuestra recomendación:

| Aspecto | Absoluto | Relativo |
|---------|----------|----------|
| **Claridad** | Más explícito: se ve la ruta completa | Más conciso |
| **Renombrar paquete** | Hay que cambiar todos los imports | No cambia nada |
| **Legibilidad en paquetes grandes** | Puede ser muy largo | Más limpio |
| **Funciona fuera de un paquete** | Sí | No — solo dentro de un paquete |

> 💡 **Convención del curso:** Usamos imports relativos (con `.`) dentro de un paquete. Es la práctica recomendada por la documentación oficial de Python para imports dentro del mismo paquete.

---

## Variables especiales de los módulos

Python define automáticamente algunas variables en cada módulo. Las más importantes son:

### `__name__`

Cada módulo tiene una variable `__name__` que contiene el nombre del módulo. Pero hay un caso especial: **cuando el módulo se ejecuta directamente** (no se importa), `__name__` vale `"__main__"`.

Esto es lo que permite el famoso patrón:

```python
# saludos.py

def hola(nombre: str) -> str:
    return f"¡Hola, {nombre}!"

if __name__ == "__main__":
    # Este código solo se ejecuta si corren: python saludos.py
    # NO se ejecuta si alguien hace: import saludos
    print(hola("Mundo"))
```

**¿Para qué sirve?** Para incluir código de prueba o demostración en un módulo sin que se ejecute cuando otro módulo lo importa. Es una forma de decir: *"si me ejecutas directamente, haz esto; si me importas, no hagas nada automáticamente"*.

### `__main__` y la ejecución con `python -m`

Cuando ejecutan un **paquete** con `python -m`, Python busca un archivo `__main__.py` dentro del paquete (o el `__main__` del módulo):

```
python -m mi_paquete.main
```

Esto ejecuta `mi_paquete/main.py` como script principal. En los ejemplos del curso hemos usado este patrón:

```
python -m vending.main       # Ejecuta la máquina expendedora
python -m cinema.main        # Ejecuta la boletería de cine
```

La ventaja de `python -m` (en comparación con `python mi_paquete/main.py`) es que **configura correctamente el `sys.path`** y permite que los imports relativos funcionen. Si intentan ejecutar directamente `python mi_paquete/main.py`, los imports relativos (`from .excepciones import ...`) van a fallar con un error.

> 💡 **Regla práctica:** Siempre ejecuten sus paquetes con `python -m nombre_paquete.main`, no con `python nombre_paquete/main.py`.

### `__all__`

Esta variable se define en un módulo para controlar qué se exporta cuando alguien hace `from modulo import *`. Es una lista de strings con los nombres que se deben exportar:

```python
# excepciones.py
__all__ = [
    "TiendaError",
    "ProductoDuplicado",
    "ProductoNoEncontrado",
    "StockInsuficiente",
]

class TiendaError(Exception):
    pass

class ProductoDuplicado(TiendaError):
    pass

class ProductoNoEncontrado(TiendaError):
    pass

class StockInsuficiente(TiendaError):
    pass

# Esta clase NO está en __all__, así que no se exporta con import *
class _ErrorInterno(TiendaError):
    pass
```

En la práctica, como ya dijimos que no deberían usar `from modulo import *`, el uso de `__all__` es más relevante en bibliotecas que distribuyen a otros desarrolladores. Para los proyectos del curso, no lo necesitan.

---

## Estructura de un proyecto real

Ahora que entienden las piezas, veamos cómo se ensamblan en un proyecto completo. Usaremos como referencia la estructura que ya conocen de los ejemplos del curso:

```
vending-app/
├── pyproject.toml          # Configuración del proyecto
├── README.md               # Documentación
└── vending/                # Paquete principal
    ├── __init__.py         # Marca la carpeta como paquete (vacío)
    ├── main.py             # Punto de entrada: python -m vending.main
    ├── excepciones.py      # Excepciones del dominio
    ├── modelos.py          # Clases de datos (@dataclass)
    └── servicio.py         # Lógica de negocio (VendingMachine)
```

Desglosemos cada pieza:

### El paquete (`vending/`)

Es la carpeta que contiene todo el código fuente. Tiene `__init__.py` (vacío en este caso) para que Python lo reconozca como paquete.

### Los módulos dentro del paquete

Cada archivo tiene una responsabilidad clara:

* **`excepciones.py`:** Define la jerarquía de excepciones del dominio. Todas heredan de una `ErrorDeDominio` base.
* **`modelos.py`:** Define las clases de datos (como `Producto` con `@dataclass`). Incluye validación en `__post_init__`.
* **`servicio.py`:** Contiene la lógica de negocio. Importa de `excepciones` y `modelos` usando imports relativos.
* **`main.py`:** Es el punto de entrada. Crea las instancias, ejecuta la lógica y muestra resultados.

### El flujo de imports

```
excepciones.py  ← no importa de ningún otro módulo del paquete
      ↑
modelos.py      ← importa de excepciones (para validación)
      ↑
servicio.py     ← importa de excepciones y modelos
      ↑
main.py         ← importa de modelos y servicio
```

Noten que los imports van en **una sola dirección**: de arriba hacia abajo. `excepciones.py` no importa de nadie, `modelos.py` solo de excepciones, y así sucesivamente. Esto evita **dependencias circulares** (que veremos en errores comunes).

### `pyproject.toml`

Este archivo define la **configuración del proyecto**: nombre, versión, dependencias, etc. Es el estándar moderno de Python (reemplaza a los antiguos `setup.py` y `setup.cfg`).

Para nuestros proyectos del curso, un `pyproject.toml` mínimo se ve así:

```toml
[project]
name = "vending-app"
version = "0.1.0"
description = "Simulación de máquina expendedora"
requires-python = ">=3.12"
```

No necesitan más que eso por ahora. El `pyproject.toml` se vuelve más importante cuando quieren distribuir su paquete, definir dependencias externas, o configurar herramientas como linters y formateadores.

---

## Subpaquetes: paquetes dentro de paquetes

Cuando un proyecto crece, un solo nivel de paquete puede no ser suficiente. Pueden crear **subpaquetes**: paquetes dentro de un paquete.

```
mi_proyecto/
├── __init__.py
├── main.py
├── modelos/                 # Subpaquete de modelos
│   ├── __init__.py
│   ├── producto.py
│   └── orden.py
├── servicios/               # Subpaquete de servicios
│   ├── __init__.py
│   ├── ventas.py
│   └── inventario.py
└── excepciones/             # Subpaquete de excepciones
    ├── __init__.py
    ├── ventas.py
    └── inventario.py
```

Los imports se hacen con notación de puntos:

```python
from mi_proyecto.modelos.producto import Producto
from mi_proyecto.servicios.ventas import ServicioVentas
from mi_proyecto.excepciones.ventas import VentaError
```

O con imports relativos dentro del mismo paquete:

```python
# Desde mi_proyecto/servicios/ventas.py
from ..modelos.producto import Producto          # Subir un nivel, entrar a modelos
from ..excepciones.ventas import VentaError      # Subir un nivel, entrar a excepciones
```

> 💡 **Para este curso:** No necesitan subpaquetes. La estructura plana con un solo nivel (`excepciones.py`, `modelos.py`, `servicio.py`, `main.py`) es suficiente para los ejercicios y proyectos que estamos haciendo. Los subpaquetes son para cuando tienen docenas de módulos y necesitan un segundo nivel de organización.

---

## ¿Qué pasa cuando importan un módulo?

Cuando Python encuentra un `import`, ocurren varias cosas por detrás. Es útil entenderlo para diagnosticar problemas:

1. **Busca el módulo** en `sys.path` (en el orden que ya vimos).
2. **Ejecuta el código del módulo de arriba a abajo.** Sí, todo el código a nivel de módulo se ejecuta. Las definiciones de clases y funciones se "registran", y cualquier código suelto (como un `print()` fuera de una función) se ejecuta inmediatamente.
3. **Cachea el módulo** en `sys.modules`. Si otro archivo importa el mismo módulo después, Python **no lo ejecuta de nuevo**: simplemente devuelve la referencia cacheada. Por eso pueden importar el mismo módulo desde 10 archivos diferentes sin que se ejecute 10 veces.

Esto tiene implicaciones importantes:

```python
# configuracion.py
print("¡Cargando configuración!")  # Esto se imprime UNA SOLA VEZ

MODO_DEBUG = True
MAX_INTENTOS = 3
```

```python
# archivo_a.py
import configuracion  # Imprime "¡Cargando configuración!"

# archivo_b.py
import configuracion  # NO imprime nada — ya está cacheado
```

---

## Buenas prácticas

* **Nombres de módulos en minúsculas y snake_case:** `modelos.py`, `excepciones.py`, `servicio_ventas.py`. Nunca `Modelos.py` ni `servicioVentas.py`. Es la convención de PEP 8 y lo que toda la comunidad Python espera.

* **Un módulo, un tema:** Igual que lo vimos en la nota de modularización. `excepciones.py` solo tiene excepciones. `modelos.py` solo tiene clases de datos. No mezclen.

* **Imports al inicio del archivo:** Todos los imports van en las primeras líneas del archivo, después de los docstrings y comentarios del módulo, y antes de cualquier otra definición. El orden convencional es:
  1. Imports de la librería estándar (`import math`, `from abc import ABC`)
  2. Imports de terceros (`import numpy as np`)
  3. Imports del proyecto (`from .modelos import Producto`)

  Separados por una línea en blanco:

  ```python
  from __future__ import annotations

  from abc import ABC, abstractmethod
  from dataclasses import dataclass

  from .excepciones import ProductoNoEncontrado
  from .modelos import Producto
  ```

* **Prefieran `from .modulo import Clase` sobre `from .modulo import *`:** Sean explícitos en lo que importan. Eso hace el código más legible y evita problemas de nombres.

* **Ejecuten paquetes con `python -m`:** Siempre usen `python -m paquete.main` para ejecutar el punto de entrada de un paquete. Esto configura `sys.path` correctamente y permite que los imports relativos funcionen.

* **No pongan lógica de ejecución a nivel de módulo:** Las definiciones (clases, funciones, constantes) van a nivel de módulo. La lógica de ejecución (crear objetos, llamar funciones, imprimir resultados) va dentro de `main()` o protegida por `if __name__ == "__main__":`.

---

## Errores comunes

### `ModuleNotFoundError`

Es el error más frecuente cuando trabajan con módulos. Significa que Python no encontró el módulo que están tratando de importar.

**Causas comunes:**

```python
# Error: ejecutar directamente un archivo dentro de un paquete
# Si hacen: python mi_paquete/main.py
# En vez de: python -m mi_paquete.main
```

```
ModuleNotFoundError: No module named 'mi_paquete'
```

**Solución:** Ejecuten con `python -m` desde el directorio que **contiene** la carpeta del paquete.

Otra causa es un error de tipeo en el nombre del módulo o que el archivo simplemente no existe:

```python
from .exepciones import Error  # Typo: "exepciones" en vez de "excepciones"
```

### Imports circulares

Un import circular ocurre cuando dos módulos se importan mutuamente:

```python
# modelos.py
from .servicio import Tienda  # ← importa servicio

class Producto:
    pass
```

```python
# servicio.py
from .modelos import Producto  # ← importa modelos (que importa servicio → ciclo)

class Tienda:
    pass
```

Python intenta cargar `modelos.py`, que necesita `servicio.py`, que necesita `modelos.py` (que todavía no terminó de cargarse). Resultado: `ImportError` o atributos faltantes.

**Solución:** Reorganicen el código para que los imports vayan en una sola dirección. Generalmente, los módulos de nivel bajo (excepciones, modelos) no deberían importar de módulos de nivel alto (servicios). Si necesitan un tipo solo para una anotación de tipo, pueden usar `from __future__ import annotations` (que hace que las anotaciones se evalúen *lazy*) o importar dentro de un bloque `if TYPE_CHECKING:`:

```python
from __future__ import annotations
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from .servicio import Tienda  # Solo se importa para el type checker, no en ejecución
```

### Confundir script con módulo

Un error frecuente al empezar:

```python
# Archivo: mi_script.py (NO dentro de un paquete)
from .utilidades import algo  # ← Error
```

```
ImportError: attempted relative import with no known parent package
```

Los imports relativos (con `.`) **solo funcionan dentro de un paquete**. Si el archivo no es parte de un paquete (no hay `__init__.py` en su carpeta, no se ejecuta con `python -m`), no pueden usar imports relativos.

**Solución:** Si el archivo es un script independiente, usen imports absolutos. Si es parte de un paquete, ejecútenlo con `python -m`.

### Sombrear módulos de la librería estándar

Si crean un archivo con el mismo nombre que un módulo de la librería estándar, su archivo "tapa" al módulo estándar:

```
mi_proyecto/
├── math.py          # ← ¡Peligro! Sombrea el módulo math de Python
└── app.py
```

```python
# app.py
import math  # Importa SU math.py, no el de Python
print(math.sqrt(16))  # AttributeError: module 'math' has no attribute 'sqrt'
```

**Solución:** Nunca nombren sus archivos como módulos de la librería estándar (`math.py`, `random.py`, `os.py`, `sys.py`, `json.py`, etc.).

---

## Ejemplo completo paso a paso

Vamos a construir un paquete desde cero para consolidar todo lo que vimos. Será un sistema simple de gestión de contactos.

### Paso 1: Crear la estructura

```
contactos_app/
├── contactos/
│   ├── __init__.py
│   ├── excepciones.py
│   ├── modelos.py
│   ├── servicio.py
│   └── main.py
└── pyproject.toml
```

### Paso 2: Definir las excepciones

```python
# contactos/excepciones.py

class ContactoError(Exception):
    """Excepción base para el dominio de contactos."""
    pass

class ContactoDuplicado(ContactoError):
    """Se lanza al agregar un contacto con email ya registrado."""
    pass

class ContactoNoEncontrado(ContactoError):
    """Se lanza al buscar un contacto que no existe."""
    pass

class DatoInvalido(ContactoError):
    """Se lanza cuando un dato del contacto no cumple las validaciones."""
    pass
```

### Paso 3: Definir el modelo

```python
# contactos/modelos.py
from __future__ import annotations

from dataclasses import dataclass

from .excepciones import DatoInvalido


@dataclass
class Contacto:
    nombre: str
    email: str
    telefono: str

    def __post_init__(self) -> None:
        if not self.nombre or self.nombre.strip() == "":
            raise DatoInvalido("El nombre no puede ser vacío.")
        if not self.email or "@" not in self.email:
            raise DatoInvalido(f"Email inválido: {self.email!r}.")
        if not self.telefono or self.telefono.strip() == "":
            raise DatoInvalido("El teléfono no puede ser vacío.")
        # Normalizar
        self.nombre = self.nombre.strip()
        self.email = self.email.strip().lower()
        self.telefono = self.telefono.strip()
```

### Paso 4: Definir el servicio

```python
# contactos/servicio.py
from __future__ import annotations

from .excepciones import ContactoDuplicado, ContactoNoEncontrado
from .modelos import Contacto


class AgendaContactos:
    def __init__(self) -> None:
        self._contactos: dict[str, Contacto] = {}  # email → Contacto

    def agregar(self, contacto: Contacto) -> None:
        if contacto.email in self._contactos:
            raise ContactoDuplicado(
                f"Ya existe un contacto con email '{contacto.email}'."
            )
        self._contactos[contacto.email] = contacto

    def buscar(self, email: str) -> Contacto:
        email = email.strip().lower()
        if email not in self._contactos:
            raise ContactoNoEncontrado(
                f"No se encontró contacto con email '{email}'."
            )
        return self._contactos[email]

    def eliminar(self, email: str) -> None:
        email = email.strip().lower()
        if email not in self._contactos:
            raise ContactoNoEncontrado(
                f"No se encontró contacto con email '{email}'."
            )
        del self._contactos[email]

    def listar(self) -> list[Contacto]:
        return list(self._contactos.values())
```

### Paso 5: Definir el punto de entrada

```python
# contactos/main.py
from .modelos import Contacto
from .servicio import AgendaContactos


def main() -> None:
    agenda = AgendaContactos()

    agenda.agregar(Contacto("Ana García", "ana@correo.com", "300-111-2222"))
    agenda.agregar(Contacto("Luis Pérez", "luis@correo.com", "310-333-4444"))
    agenda.agregar(Contacto("María López", "maria@correo.com", "320-555-6666"))

    print("Todos los contactos:")
    for contacto in agenda.listar():
        print(f"  {contacto}")

    print("\nBuscando a ana@correo.com:")
    encontrado = agenda.buscar("ana@correo.com")
    print(f"  {encontrado}")

    agenda.eliminar("luis@correo.com")
    print("\nDespués de eliminar a Luis:")
    for contacto in agenda.listar():
        print(f"  {contacto}")


if __name__ == "__main__":
    main()
```

### Paso 6: Ejecutar

Desde la carpeta `contactos_app/` (la que **contiene** la carpeta `contactos/`):

```
python -m contactos.main
```

Salida esperada:

```
Todos los contactos:
  Contacto(nombre='Ana García', email='ana@correo.com', telefono='300-111-2222')
  Contacto(nombre='Luis Pérez', email='luis@correo.com', telefono='310-333-4444')
  Contacto(nombre='María López', email='maria@correo.com', telefono='320-555-6666')

Buscando a ana@correo.com:
  Contacto(nombre='Ana García', email='ana@correo.com', telefono='300-111-2222')

Después de eliminar a Luis:
  Contacto(nombre='Ana García', email='ana@correo.com', telefono='300-111-2222')
  Contacto(nombre='María López', email='maria@correo.com', telefono='320-555-6666')
```

---

## Resumen: módulo vs. paquete vs. librería

Para que queden claros los términos:

| Concepto | ¿Qué es? | Ejemplo |
|----------|-----------|---------|
| **Módulo** | Un archivo `.py` | `excepciones.py` |
| **Paquete** | Una carpeta con `__init__.py` que contiene módulos | `vending/` |
| **Librería estándar** | Colección de módulos y paquetes que viene con Python | `math`, `os`, `dataclasses`, `abc` |
| **Paquete de terceros** | Paquete instalado con `pip` | `numpy`, `pandas`, `flask` |

---

## Preguntas para pensar

1. ¿Qué pasaría si crean un archivo llamado `dataclasses.py` en su proyecto y luego intentan hacer `from dataclasses import dataclass`?
2. Si tienen un paquete con 3 módulos y todos se importan entre sí (A importa B, B importa C, C importa A), ¿qué problemas podrían tener?
3. ¿Por qué creen que en los ejemplos del curso siempre dejamos `__init__.py` vacío? ¿En qué situación sería útil ponerle contenido?
4. Si ejecutan `python main.py` directamente (sin `python -m`) y tienen imports relativos, ¿qué sucede? ¿Por qué?
5. ¿Cuál es la diferencia entre `import math` y `from math import sqrt` en términos de qué queda disponible en el espacio de nombres?
