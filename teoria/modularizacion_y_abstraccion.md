# Modularización y Abstracción

## ¿Qué es la modularización?

Imaginen que tienen un programa de 2000 líneas de código. Todo en un solo archivo. Funciones por aquí, clases por allá, variables globales regadas por todas partes. ¿Les suena familiar? Bueno, eso es un **monolito**: un bloque enorme donde todo está mezclado. Y el problema no es solo que se ve feo, es que trabajar así es una pesadilla. ¿Quieren cambiar algo? Buena suerte encontrando dónde está. ¿Apareció un bug? Puede estar en cualquier parte. ¿Otro compañero necesita trabajar en el mismo archivo? Prepárense para conflictos.

La **modularización** es la práctica de dividir un sistema grande en **partes más pequeñas, independientes y manejables** llamadas **módulos**. Cada módulo se encarga de una responsabilidad específica y se comunica con los demás a través de interfaces bien definidas. No es un concepto exclusivo de la programación: es una estrategia fundamental de ingeniería que se aplica en prácticamente cualquier disciplina donde haya que manejar complejidad.

**¿Para qué sirve?** Para poder entender, desarrollar, probar y mantener cada parte del sistema de forma independiente. Cuando un programa está bien modularizado, pueden trabajar en una pieza sin miedo a romper las demás. Pueden reutilizar piezas en otros proyectos. Pueden dividir el trabajo entre varias personas sin pisarse los unos a los otros.

---

## Analogías del mundo real

Antes de entrar al código, veamos cómo este concepto aparece en todas partes:

### Una casa con habitaciones

Una casa no es un espacio vacío gigante donde todo ocurre en el mismo lugar. Tiene habitaciones: cocina, baño, dormitorios, sala. Cada habitación tiene un **propósito claro** y contiene los elementos necesarios para cumplir ese propósito. La cocina tiene los implementos de cocina, el baño tiene las instalaciones sanitarias. Si necesitan arreglar el baño, no tienen que demoler la cocina. Si quieren remodelar la sala, el dormitorio no se ve afectado.

Eso es modularización: cada habitación es un **módulo** con una responsabilidad clara y fronteras definidas.

### Una empresa con departamentos

Una empresa no pone a todos los empleados en un salón a hacer de todo. Tiene departamentos: contabilidad, recursos humanos, producción, ventas. Cada departamento tiene sus propias funciones, sus propios datos y sus propios procesos. Cuando contabilidad necesita información de ventas, no va a hurgar en sus archivos directamente: le **pide** la información a través de un proceso establecido (un reporte, un sistema, una solicitud).

Así funciona un sistema modularizado: cada módulo maneja sus propios datos y expone solo lo necesario para que otros módulos puedan interactuar con él.

### Un equipo de sonido por componentes

Piensen en un equipo de sonido modular: tienen el amplificador, los parlantes, el reproductor de CD, la tornamesa. Cada componente hace **una cosa bien**. ¿Se daña el reproductor de CD? Lo reemplazan sin tocar el amplificador. ¿Quieren mejores parlantes? Los cambian y todo lo demás sigue funcionando. Eso es posible porque cada componente se conecta con los demás a través de **conexiones estándar** (cables, puertos).

En software, esas "conexiones estándar" son las **interfaces**: los métodos públicos, los parámetros, los tipos de datos que un módulo expone para comunicarse con otros.

---

## ¿Por qué modularizar? Propósito y beneficios

Modularizar no es un capricho estético. Es una necesidad práctica que trae beneficios concretos:

* **Comprensión:** Un módulo pequeño y enfocado es más fácil de entender que un archivo de miles de líneas. Cuando abren un archivo llamado `excepciones.py`, ya saben qué esperar adentro.
* **Mantenimiento:** Si las excepciones de dominio están en un solo archivo, cualquier cambio en las excepciones se hace ahí y solo ahí. No hay que buscar en 15 archivos distintos.
* **Reutilización:** Un módulo bien diseñado puede usarse en otro proyecto. Si crearon un módulo de validaciones sólido, pueden copiarlo a otro sistema sin arrastrar código innecesario.
* **Trabajo en equipo:** Cuando el código está dividido en módulos, varios desarrolladores pueden trabajar en paralelo sin interferir. Uno trabaja en `modelos.py`, otro en `servicios.py`, otro en `excepciones.py`.
* **Testing:** Es mucho más fácil escribir pruebas para un módulo pequeño con responsabilidades claras que para un bloque monolítico donde todo depende de todo.
* **Escalabilidad:** A medida que el sistema crece, la modularización permite agregar nuevas funcionalidades como módulos nuevos, sin tener que modificar o entender todo el sistema existente.

---

## Los dos principios clave: cohesión y acoplamiento

Para modularizar bien, hay que entender dos conceptos que van siempre juntos: **cohesión** y **acoplamiento**. Son como las dos caras de una moneda.

### Cohesión: "que cada módulo se enfoque en lo suyo"

La **cohesión** mide qué tan relacionadas están entre sí las responsabilidades dentro de un módulo. Un módulo con **alta cohesión** tiene elementos que trabajan juntos hacia un mismo objetivo. Un módulo con **baja cohesión** tiene cosas mezcladas que no tienen mucho que ver entre sí.

**Alta cohesión (bueno):**

```python
# excepciones.py — todas las excepciones del dominio están juntas
class ErrorDeDominio(Exception):
    """Excepción base para errores del dominio."""
    pass

class ProductoNoEncontrado(ErrorDeDominio):
    """Se lanza cuando se busca un producto que no existe."""
    pass

class StockInsuficiente(ErrorDeDominio):
    """Se lanza cuando no hay suficiente inventario."""
    pass

class MontoInvalido(ErrorDeDominio):
    """Se lanza cuando el monto proporcionado no es válido."""
    pass
```

Todo en este archivo trata de lo mismo: excepciones del dominio. Eso es alta cohesión.

**Baja cohesión (malo):**

```python
# utils.py — un cajón de sastre con cosas que no tienen relación
import math

def calcular_iva(precio: float) -> float:
    return precio * 0.19

def enviar_correo(destinatario: str, mensaje: str) -> None:
    print(f"Enviando correo a {destinatario}")

def formatear_fecha(fecha: str) -> str:
    return fecha.replace("/", "-")

class ConexionBaseDatos:
    def conectar(self) -> None:
        pass
```

¿Qué tiene que ver el IVA con enviar correos? ¿Y la conexión a base de datos qué hace ahí? Este archivo es un cajón de sastre. No tiene un propósito claro. Eso es baja cohesión.

> 💡 **Regla práctica:** Si no pueden describir el propósito de un módulo en una sola oración corta, probablemente tiene baja cohesión.

### Acoplamiento: "que los módulos no dependan demasiado entre sí"

El **acoplamiento** mide qué tan conectados o dependientes están dos módulos entre sí. Un **bajo acoplamiento** significa que los módulos se comunican a través de interfaces claras y mínimas, y cambiar uno no obliga a cambiar el otro. Un **alto acoplamiento** significa que un módulo conoce demasiados detalles internos de otro.

**Alto acoplamiento (malo):**

```python
# servicio.py
class ServicioVentas:
    def __init__(self) -> None:
        # Accede directamente a los datos internos de otro módulo
        from almacen import inventario_global  # variable global de otro módulo
        self.inventario = inventario_global

    def vender(self, producto_id: str, cantidad: int) -> None:
        # Manipula directamente la estructura interna del inventario
        self.inventario[producto_id]["stock"] -= cantidad
        self.inventario[producto_id]["ventas"] += cantidad
```

Este servicio conoce exactamente cómo está organizado el diccionario interno del almacén. Si el almacén cambia su estructura de datos (por ejemplo, pasa de diccionarios a objetos), el servicio de ventas se rompe. Eso es alto acoplamiento.

**Bajo acoplamiento (bueno):**

```python
# servicio.py
class ServicioVentas:
    def __init__(self, repositorio: RepositorioProductos) -> None:
        self._repositorio = repositorio

    def vender(self, producto_id: str, cantidad: int) -> None:
        producto = self._repositorio.obtener(producto_id)
        if producto is None:
            raise ProductoNoEncontrado(f"Producto '{producto_id}' no existe.")
        producto.reducir_stock(cantidad)
        self._repositorio.actualizar(producto)
```

Aquí el servicio no sabe cómo se almacenan los productos. Solo sabe que tiene un repositorio con métodos `obtener` y `actualizar`. Si la implementación interna del repositorio cambia, el servicio ni se entera. Eso es bajo acoplamiento.

> 💡 **La meta:** Alta cohesión + Bajo acoplamiento. Cada módulo enfocado en lo suyo y comunicándose con los demás solo a través de interfaces claras.

---

## Separación de responsabilidades

La **separación de responsabilidades** (o *separation of concerns*) es el principio que dice: cada módulo, clase o función debe encargarse de **una sola cosa**. Esto está directamente relacionado con lo que ya vimos como **Principio de Responsabilidad Única (SRP)** en las notas de clases y atributos.

Pero la separación de responsabilidades aplica a **todos los niveles** del código:

| Nivel | Ejemplo |
|-------|---------|
| **Función** | Una función que calcula el total, no debería también imprimir el resultado |
| **Clase** | Una clase `Producto` modela un producto, no gestiona la base de datos |
| **Módulo (archivo)** | Un archivo `excepciones.py` contiene excepciones, no lógica de negocio |
| **Paquete (carpeta)** | Una carpeta `modelos/` agrupa las clases de dominio, no los servicios |

Cuando la separación de responsabilidades se respeta en todos los niveles, el resultado es un sistema organizado donde cada pieza tiene un rol claro.

---

## La abstracción como herramienta de modularización

Ya hablamos de la **abstracción** como uno de los cuatro pilares de la POO (en la nota de los pilares vimos cómo funciona con clases abstractas y el módulo `abc`). Ahora vamos a verla desde otra perspectiva: la abstracción como **herramienta para modularizar** mejor.

### ¿Cómo se conectan?

La abstracción permite definir **qué hace** un componente sin especificar **cómo lo hace**. Y eso es exactamente lo que necesitamos para que los módulos se comuniquen sin acoplarse: si un módulo depende de una abstracción (una interfaz, un protocolo, una clase abstracta) en vez de una implementación concreta, podemos cambiar la implementación sin afectar al módulo que la usa.

Veámoslo con un ejemplo concreto. Supongamos que tenemos un sistema de notificaciones:

**Sin abstracción (acoplado):**

```python
class ServicioRegistro:
    def __init__(self) -> None:
        self._notificador = NotificadorEmail()  # Depende de una clase concreta

    def registrar_usuario(self, nombre: str, correo: str) -> None:
        # ... lógica de registro ...
        self._notificador.enviar_email(correo, "Bienvenido", f"Hola {nombre}")
```

Si mañana quieren enviar la notificación por SMS en vez de email, tienen que modificar `ServicioRegistro`. El servicio está **acoplado** a la implementación de email.

**Con abstracción (desacoplado):**

```python
from abc import ABC, abstractmethod

class Notificador(ABC):
    @abstractmethod
    def notificar(self, destinatario: str, asunto: str, mensaje: str) -> None:
        pass

class NotificadorEmail(Notificador):
    def notificar(self, destinatario: str, asunto: str, mensaje: str) -> None:
        print(f"[EMAIL] {asunto} → {destinatario}: {mensaje}")

class NotificadorSMS(Notificador):
    def notificar(self, destinatario: str, asunto: str, mensaje: str) -> None:
        print(f"[SMS] {mensaje} → {destinatario}")

class ServicioRegistro:
    def __init__(self, notificador: Notificador) -> None:
        self._notificador = notificador  # Depende de la abstracción

    def registrar_usuario(self, nombre: str, correo: str) -> None:
        # ... lógica de registro ...
        self._notificador.notificar(correo, "Bienvenido", f"Hola {nombre}")
```

Ahora `ServicioRegistro` depende de `Notificador` (la abstracción), no de `NotificadorEmail` (la implementación). Si quieren cambiar a SMS, solo cambian qué implementación inyectan:

```python
# Con email
servicio = ServicioRegistro(NotificadorEmail())

# Con SMS — el servicio no cambia ni una línea
servicio = ServicioRegistro(NotificadorSMS())
```

Esto es lo que en los ejemplos del curso hemos visto como **Inyección de Dependencias (DI)** y el **Principio de Inversión de Dependencia (DIP)**. La abstracción es la pieza que lo hace posible.

### Niveles de abstracción

La abstracción no solo aplica a clases. Aplica a **todos los niveles** de organización del código, y cada nivel ofrece una capa de simplificación:

```
Nivel más bajo                                    Nivel más alto
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ Funciones │ → │  Clases  │ → │ Módulos  │ → │ Paquetes │
│           │   │          │   │ (.py)    │   │ (carpeta)│
└──────────┘   └──────────┘   └──────────┘   └──────────┘
```

* **Funciones:** Abstraen una secuencia de instrucciones bajo un nombre. En vez de repetir 10 líneas, llaman a `calcular_total()`.
* **Clases:** Abstraen datos y comportamiento en una sola unidad. Un `Producto` encapsula nombre, precio, stock y las operaciones sobre ellos.
* **Módulos:** Abstraen un conjunto de clases y funciones relacionadas en un archivo. `excepciones.py` agrupa todas las excepciones.
* **Paquetes:** Abstraen un conjunto de módulos relacionados en una carpeta. La carpeta `vending/` agrupa todo lo de la máquina expendedora.

Cada nivel esconde detalles del nivel inferior. Cuando importan `from vending.excepciones import ProductoNoEncontrado`, no necesitan saber cómo está implementada esa clase internamente. Solo necesitan saber qué hace y cuándo se lanza.

---

## Patrones de organización de código

Cuando deciden modularizar un proyecto, surge la pregunta: **¿cómo organizo los archivos?** Hay dos enfoques principales:

### Organización por capas (técnica)

Agrupa los archivos por su **rol técnico** en el sistema:

```
mi_proyecto/
├── modelos.py          # Clases de dominio (Product, Order, etc.)
├── excepciones.py      # Excepciones personalizadas
├── repositorios.py     # Acceso a datos (ABCs e implementaciones)
├── servicios.py        # Lógica de negocio
└── main.py             # Punto de entrada
```

**Ventajas:** Fácil de entender para proyectos pequeños. Cada archivo tiene un propósito técnico claro.

**Desventajas:** A medida que el proyecto crece, cada archivo se hace muy grande. Si tienen 20 modelos, 15 excepciones y 10 servicios, los archivos se vuelven difíciles de navegar.

### Organización por dominio (funcional)

Agrupa los archivos por el **área de negocio** que representan:

```
mi_proyecto/
├── productos/
│   ├── __init__.py
│   ├── modelos.py
│   ├── excepciones.py
│   ├── repositorio.py
│   └── servicio.py
├── ordenes/
│   ├── __init__.py
│   ├── modelos.py
│   ├── excepciones.py
│   ├── repositorio.py
│   └── servicio.py
└── main.py
```

**Ventajas:** Cada carpeta es un "mini-mundo" autocontenido. Si necesitan trabajar en órdenes, todo está en `ordenes/`. Escala mejor en proyectos grandes.

**Desventajas:** Puede parecer excesivo para proyectos pequeños. Puede haber duplicación de patrones entre carpetas.

> 💡 **Recomendación para este curso:** Para los ejercicios y proyectos que estamos haciendo, la organización por capas es suficiente y más clara. Es la que hemos estado usando en los ejemplos (como la máquina expendedora y la boletería de cine). A medida que los proyectos crezcan, podrán migrar a organización por dominio.

---

## De un script monolítico a código modular: ejemplo paso a paso

Veamos cómo se ve el proceso de modularización en la práctica. Vamos a tomar un script que hace todo en un solo archivo y a descomponerlo en módulos.

### El monolito: todo en un archivo

```python
# tienda.py — TODO en un solo archivo

class Producto:
    def __init__(self, codigo: str, nombre: str, precio: int, stock: int) -> None:
        if not codigo or codigo.strip() == "":
            raise ValueError("El código no puede ser vacío.")
        if precio <= 0:
            raise ValueError("El precio debe ser positivo.")
        if stock < 0:
            raise ValueError("El stock no puede ser negativo.")
        self.codigo = codigo
        self.nombre = nombre
        self.precio = precio
        self.stock = stock

    def __repr__(self) -> str:
        return f"Producto(codigo={self.codigo!r}, nombre={self.nombre!r}, precio={self.precio}, stock={self.stock})"


class TiendaError(Exception):
    pass

class ProductoDuplicado(TiendaError):
    pass

class ProductoNoEncontrado(TiendaError):
    pass

class StockInsuficiente(TiendaError):
    pass


class Tienda:
    def __init__(self) -> None:
        self._catalogo: dict[str, Producto] = {}

    def agregar_producto(self, producto: Producto) -> None:
        if producto.codigo in self._catalogo:
            raise ProductoDuplicado(f"Ya existe un producto con código '{producto.codigo}'.")
        self._catalogo[producto.codigo] = producto

    def buscar_producto(self, codigo: str) -> Producto:
        if codigo not in self._catalogo:
            raise ProductoNoEncontrado(f"No se encontró producto con código '{codigo}'.")
        return self._catalogo[codigo]

    def vender(self, codigo: str, cantidad: int) -> int:
        producto = self.buscar_producto(codigo)
        if cantidad <= 0:
            raise ValueError("La cantidad debe ser positiva.")
        if producto.stock < cantidad:
            raise StockInsuficiente(
                f"Stock insuficiente para '{producto.nombre}': "
                f"disponible={producto.stock}, solicitado={cantidad}."
            )
        producto.stock -= cantidad
        return producto.precio * cantidad

    def listar_productos(self) -> list[Producto]:
        return list(self._catalogo.values())


# --- Código principal ---
tienda = Tienda()
tienda.agregar_producto(Producto("A01", "Café", 3500, 20))
tienda.agregar_producto(Producto("A02", "Galletas", 2000, 15))
tienda.agregar_producto(Producto("A03", "Jugo", 4000, 10))

print("Catálogo:")
for p in tienda.listar_productos():
    print(f"  {p}")

total = tienda.vender("A01", 3)
print(f"\nVenta: 3 unidades de Café → Total: ${total:,}")

print(f"\nStock actualizado de Café: {tienda.buscar_producto('A01').stock}")
```

¿Funciona? Sí. ¿Es mantenible a largo plazo? No tanto. Si este sistema crece, todo se vuelve difícil de manejar.

### La versión modularizada

Ahora veamos cómo separar el mismo código en módulos con responsabilidades claras:

**Estructura de archivos:**

```
tienda/
├── __init__.py          # Marca la carpeta como paquete Python
├── main.py              # Punto de entrada (python -m tienda.main)
├── excepciones.py       # Excepciones del dominio
├── modelos.py           # Clases de datos (Producto)
└── servicio.py          # Lógica de negocio (Tienda)
```

**`excepciones.py`** — Solo excepciones:

```python
class TiendaError(Exception):
    """Excepción base para errores del dominio de la tienda."""
    pass

class ProductoDuplicado(TiendaError):
    """Se lanza al intentar agregar un producto con código ya existente."""
    pass

class ProductoNoEncontrado(TiendaError):
    """Se lanza al buscar un producto que no existe en el catálogo."""
    pass

class StockInsuficiente(TiendaError):
    """Se lanza cuando no hay suficiente stock para la venta."""
    pass
```

**`modelos.py`** — Solo la clase de datos:

```python
from __future__ import annotations

from .excepciones import ProductoNoEncontrado  # No se necesita aquí, pero importamos lo que sí

class Producto:
    def __init__(self, codigo: str, nombre: str, precio: int, stock: int) -> None:
        if not codigo or codigo.strip() == "":
            raise ValueError("El código no puede ser vacío.")
        if precio <= 0:
            raise ValueError("El precio debe ser positivo.")
        if stock < 0:
            raise ValueError("El stock no puede ser negativo.")
        self.codigo = codigo
        self.nombre = nombre
        self.precio = precio
        self.stock = stock

    def __repr__(self) -> str:
        return (
            f"Producto(codigo={self.codigo!r}, nombre={self.nombre!r}, "
            f"precio={self.precio}, stock={self.stock})"
        )
```

**`servicio.py`** — Solo lógica de negocio:

```python
from __future__ import annotations

from .excepciones import ProductoDuplicado, ProductoNoEncontrado, StockInsuficiente
from .modelos import Producto

class Tienda:
    def __init__(self) -> None:
        self._catalogo: dict[str, Producto] = {}

    def agregar_producto(self, producto: Producto) -> None:
        if producto.codigo in self._catalogo:
            raise ProductoDuplicado(
                f"Ya existe un producto con código '{producto.codigo}'."
            )
        self._catalogo[producto.codigo] = producto

    def buscar_producto(self, codigo: str) -> Producto:
        if codigo not in self._catalogo:
            raise ProductoNoEncontrado(
                f"No se encontró producto con código '{codigo}'."
            )
        return self._catalogo[codigo]

    def vender(self, codigo: str, cantidad: int) -> int:
        producto = self.buscar_producto(codigo)
        if cantidad <= 0:
            raise ValueError("La cantidad debe ser positiva.")
        if producto.stock < cantidad:
            raise StockInsuficiente(
                f"Stock insuficiente para '{producto.nombre}': "
                f"disponible={producto.stock}, solicitado={cantidad}."
            )
        producto.stock -= cantidad
        return producto.precio * cantidad

    def listar_productos(self) -> list[Producto]:
        return list(self._catalogo.values())
```

**`main.py`** — Solo el punto de entrada:

```python
from .modelos import Producto
from .servicio import Tienda

def main() -> None:
    tienda = Tienda()
    tienda.agregar_producto(Producto("A01", "Café", 3500, 20))
    tienda.agregar_producto(Producto("A02", "Galletas", 2000, 15))
    tienda.agregar_producto(Producto("A03", "Jugo", 4000, 10))

    print("Catálogo:")
    for p in tienda.listar_productos():
        print(f"  {p}")

    total = tienda.vender("A01", 3)
    print(f"\nVenta: 3 unidades de Café → Total: ${total:,}")
    print(f"\nStock actualizado de Café: {tienda.buscar_producto('A01').stock}")

if __name__ == "__main__":
    main()
```

### ¿Qué ganamos?

| Aspecto | Monolito | Modularizado |
|---------|----------|-------------|
| **Archivos** | 1 archivo grande | 4 archivos pequeños y enfocados |
| **Comprensión** | Hay que leer todo para encontrar algo | Cada archivo tiene un propósito claro |
| **Mantenimiento** | Cambio en excepciones puede romper lógica adyacente | Cada cambio está aislado en su módulo |
| **Trabajo en equipo** | Todos editan el mismo archivo | Cada persona puede trabajar en un módulo diferente |
| **Reutilización** | Difícil extraer piezas | Pueden importar `modelos.py` desde otro proyecto |

El código es exactamente el mismo. La lógica no cambió. Lo que cambió es la **organización**, y eso hace toda la diferencia en un proyecto real.

---

## Relación con los conceptos previos del curso

La modularización no es un concepto aislado. Se conecta con varios temas que ya hemos visto:

* **Los cuatro pilares de POO:** El encapsulamiento y la abstracción son la base de la modularización. Sin encapsulamiento (ocultar detalles internos) no hay fronteras claras entre módulos. Sin abstracción (depender de interfaces, no de implementaciones) no hay bajo acoplamiento.
* **Principio de responsabilidad única (SRP):** Cada clase debería tener una sola razón para cambiar. La modularización lleva este principio al nivel de archivos y carpetas: cada módulo debería tener una sola razón para cambiar.
* **Inyección de Dependencias (DI) y DIP:** Inyectar dependencias a través de abstracciones es la forma práctica de lograr bajo acoplamiento entre módulos. Veremos este tema en profundidad en las notas de DI y DIP.
* **Dataclasses:** En los ejercicios hemos usado `@dataclass` para modelar datos con validación en `__post_init__`. Esos modelos de datos son candidatos naturales a vivir en su propio módulo (`modelos.py`).

---

## Buenas prácticas

* **Un módulo, un propósito:** Si un archivo se llama `excepciones.py`, solo debería contener excepciones. Si se llama `modelos.py`, solo clases de datos. Si no pueden describir su propósito en una frase, probablemente necesiten dividirlo.
* **No crear módulos prematuramente:** Si el proyecto es pequeño (menos de 100 líneas), un solo archivo puede ser perfectamente adecuado. Modularicen cuando la complejidad lo justifique, no antes.
* **Nombres descriptivos:** El nombre del archivo debe comunicar su contenido. `utils.py` no dice nada. `excepciones.py`, `modelos.py`, `servicio.py` dicen exactamente qué hay adentro.
* **Minimizar las dependencias entre módulos:** Cada import que agregan es una conexión entre módulos. Mientras menos imports necesite un módulo, más independiente es. Si `modelos.py` necesita importar de `servicio.py` Y `servicio.py` importa de `modelos.py`, tienen una **dependencia circular**, que es una señal de que algo está mal organizado.
* **Favorecer las abstracciones en las fronteras:** Cuando un módulo depende de otro, que dependa de una abstracción (ABC, Protocol) y no de la implementación concreta. Esto es lo que permite cambiar una pieza sin afectar las demás.

---

## Errores comunes

* **El archivo `utils.py`:** Es tentador crear un archivo "de utilidades" donde van todas las funciones que "no saben dónde poner". Ese archivo tiende a crecer sin control y termina con baja cohesión. Si tienen funciones de utilidad, piensen a qué dominio pertenecen y pónganlas en el módulo correspondiente.
* **Modularizar demasiado pronto:** Un archivo de 30 líneas no necesita ser dividido en 5 módulos. Eso agrega complejidad innecesaria. Modularicen cuando sientan que el archivo está difícil de navegar o que mezcla responsabilidades.
* **Clases/funciones que conocen demasiado del otro módulo:** Si la función `vender()` necesita saber que el inventario es un diccionario con claves de tipo string y valores que tienen un campo `stock` de tipo entero... eso es alto acoplamiento. La función solo debería interactuar con métodos públicos del otro módulo.
* **Confundir separación física con separación lógica:** Poner código en archivos diferentes no es suficiente. Si dos módulos acceden a las mismas variables globales o se importan mutuamente todo, están separados físicamente pero acoplados lógicamente. La separación real viene de **interfaces claras y dependencias mínimas**.

---

## Preguntas para pensar

1. ¿Cuántos módulos usarían para un sistema de biblioteca que maneja libros, usuarios, préstamos y multas? ¿Qué iría en cada uno?
2. Si tienen un archivo `servicio.py` con 800 líneas y 6 clases de servicio diferentes, ¿qué harían?
3. ¿Por qué creen que en los ejemplos del curso (`vending/`, `cinema/`) las excepciones siempre van en un archivo separado?
4. ¿Es posible tener alta cohesión pero también alto acoplamiento? ¿Cómo se vería eso?
5. Piensen en una aplicación que usen todos los días (WhatsApp, Spotify, un juego). ¿Qué módulos creen que tiene? ¿Cómo creen que están organizados?
