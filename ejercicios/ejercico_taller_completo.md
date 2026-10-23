# Taller — **QuickBite: Plataforma de Delivery**

> **Tipo:** Taller autónomo  
> **Duración estimada:** 2 h 30 min  
> **Temas que evalúa:** Todos los conceptos del curso  
> **Prerrequisitos:** todos los temas de teoría vistos en clase

---

## Contexto

**QuickBite** es una plataforma de delivery de comida (al estilo Rappi o Uber Eats). Los restaurantes publican un menú, los clientes hacen pedidos, y el sistema gestiona el ciclo de vida de cada pedido desde que se recibe hasta que se entrega.

Un desarrollador del equipo implementó la primera versión del sistema. **El código funciona**, pero el equipo de arquitectura lo rechazó en revisión de código y pidió que se rediseñe antes de pasar a producción.

Tu tarea es entender por qué fue rechazado y corregirlo.

---

## Reglas del taller

- Entrega un **paquete Python** llamado `quickbite/`, ejecutable con `python -m quickbite`.
- **Type hints** en todos los parámetros y retornos.
- **Sin `print()`** dentro de las clases del dominio.
- Todos los imports dentro del paquete deben ser **relativos**.
- Guarda el trabajo con el nombre: `apellido_nombre_taller_quickbite/`

---

## El código entregado (versión con problemas)

Lee el siguiente código completo. **Funciona**. Ejecútalo y verifica la salida antes de responder las preguntas.

```python
# quickbite_v1.py — versión inicial (todo en un archivo)

from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from typing import ClassVar


# ══════════════════════════════════════════════════════
# EXCEPCIONES
# ══════════════════════════════════════════════════════

class QuickBiteError(Exception):
    """Base de todos los errores del dominio."""

class ItemNoEncontrado(QuickBiteError):
    """El ítem solicitado no existe en el menú."""

class ClienteNoEncontrado(QuickBiteError):
    """El cliente no está registrado."""

class PedidoNoEncontrado(QuickBiteError):
    """El ID de pedido no existe."""

class PedidoVacio(QuickBiteError):
    """Se intenta confirmar un pedido sin ítems."""

class TransicionInvalida(QuickBiteError):
    """La transición de estado solicitada no está permitida."""

class ItemNoDisponible(QuickBiteError):
    """El ítem existe pero no está disponible actualmente."""


# ══════════════════════════════════════════════════════
# MENÚ — jerarquía de ítems
# ══════════════════════════════════════════════════════

class ItemMenu(ABC):
    """Contrato de todo ítem que puede aparecer en un pedido."""

    def __init__(self, id: str, nombre: str, disponible: bool = True) -> None:
        if not id.strip():
            raise ValueError("El ID del ítem no puede estar vacío")
        if not nombre.strip():
            raise ValueError("El nombre del ítem no puede estar vacío")
        self.id = id
        self.nombre = nombre
        self.disponible = disponible

    @abstractmethod
    def calcular_precio(self) -> float:
        """Precio final del ítem (puede incluir lógica propia)."""

    def resumen(self) -> str:
        estado = "✓" if self.disponible else "✗"
        return f"[{estado}] {self.nombre} — ${self.calcular_precio():.0f}"


class Plato(ItemMenu):
    """Plato individual con precio fijo y lista de adicionales opcionales."""

    def __init__(
        self,
        id: str,
        nombre: str,
        precio_base: float,
        adicionales: list[float] | None = None,
        disponible: bool = True,
    ) -> None:
        super().__init__(id, nombre, disponible)
        if precio_base <= 0:
            raise ValueError(f"El precio base debe ser positivo: {precio_base}")
        self.precio_base = precio_base
        self.adicionales: list[float] = adicionales or []

    def calcular_precio(self) -> float:
        return self.precio_base + sum(self.adicionales)


class Bebida(ItemMenu):
    """Bebida con precio que varía según el tamaño elegido."""

    MULTIPLICADORES: ClassVar[dict[str, float]] = {
        "S": 1.0,
        "M": 1.4,
        "L": 1.8,
    }

    def __init__(
        self,
        id: str,
        nombre: str,
        precio_base: float,
        tamanio: str = "M",
        disponible: bool = True,
    ) -> None:
        super().__init__(id, nombre, disponible)
        if precio_base <= 0:
            raise ValueError(f"El precio base debe ser positivo: {precio_base}")
        if tamanio not in self.MULTIPLICADORES:
            raise ValueError(f"Tamaño '{tamanio}' no válido. Use S, M o L")
        self.precio_base = precio_base
        self.tamanio = tamanio

    def calcular_precio(self) -> float:
        return round(self.precio_base * self.MULTIPLICADORES[self.tamanio], 2)


class Combo(ItemMenu):
    """Conjunto de ítems vendidos juntos con descuento."""

    def __init__(
        self,
        id: str,
        nombre: str,
        items: list[ItemMenu],
        descuento_porcentaje: float = 10.0,
        disponible: bool = True,
    ) -> None:
        super().__init__(id, nombre, disponible)
        if not items:
            raise ValueError("Un combo debe tener al menos un ítem")
        if not (0.0 <= descuento_porcentaje <= 50.0):
            raise ValueError(f"Descuento {descuento_porcentaje}% fuera del rango 0–50")
        self.items = items
        self.descuento_porcentaje = descuento_porcentaje

    def calcular_precio(self) -> float:
        total = sum(item.calcular_precio() for item in self.items)
        return round(total * (1 - self.descuento_porcentaje / 100), 2)


# ══════════════════════════════════════════════════════
# PEDIDO
# ══════════════════════════════════════════════════════

@dataclass
class Pedido:
    """Un pedido realizado por un cliente."""

    FLUJO_VALIDO: ClassVar[dict[str, list[str]]] = {
        "RECIBIDO":       ["EN_PREPARACION"],
        "EN_PREPARACION": ["EN_CAMINO"],
        "EN_CAMINO":      ["ENTREGADO"],
        "ENTREGADO":      [],
    }

    id: int
    cliente: str
    items: list[tuple[ItemMenu, int]] = field(default_factory=list)
    estado: str = "RECIBIDO"

    def __post_init__(self) -> None:
        if not self.cliente.strip():
            raise ValueError("El nombre del cliente no puede estar vacío")
        if self.id <= 0:
            raise ValueError(f"ID de pedido inválido: {self.id}")

    def avanzar_estado(self) -> None:
        siguientes = self.FLUJO_VALIDO.get(self.estado, [])
        if not siguientes:
            raise TransicionInvalida(
                f"El pedido en estado '{self.estado}' no puede avanzar más"
            )
        self.estado = siguientes[0]

    def total_items(self) -> float:
        return sum(item.calcular_precio() * cant for item, cant in self.items)


# ══════════════════════════════════════════════════════
# NOTIFICADOR — implementación concreta (sin abstracción)
# ══════════════════════════════════════════════════════

class NotificadorConsola:
    """Imprime eventos de pedidos en pantalla."""

    def notificar(self, cliente: str, pedido: Pedido) -> None:
        print(f"  📦 [{pedido.estado}] Pedido #{pedido.id} para {cliente}")


# ══════════════════════════════════════════════════════
# CALCULADOR DE DESCUENTO — implementación concreta (sin abstracción)
# ══════════════════════════════════════════════════════

class DescuentoFijo:
    """Aplica siempre el mismo porcentaje de descuento."""

    def __init__(self, porcentaje: float) -> None:
        if not (0.0 <= porcentaje <= 100.0):
            raise ValueError(f"Porcentaje {porcentaje} fuera del rango 0–100")
        self.porcentaje = porcentaje

    def calcular(self, total: float, pedido: Pedido) -> float:
        return round(total * (1 - self.porcentaje / 100), 2)


# ══════════════════════════════════════════════════════
# SERVICIO DE PEDIDOS — ❌ ACOPLADO
# ══════════════════════════════════════════════════════

class ServicioPedidos:
    """Gestiona el ciclo de vida de los pedidos."""

    def __init__(self) -> None:
        self._pedidos: dict[int, Pedido] = {}
        self._contador: int = 0
        # ❌ El servicio crea sus propias dependencias adentro
        self._notificador = NotificadorConsola()
        self._descuento = DescuentoFijo(porcentaje=10)

    def crear_pedido(self, cliente: str, items: list[tuple[ItemMenu, int]]) -> Pedido:
        if not items:
            raise PedidoVacio("No se puede crear un pedido sin ítems")
        for item, _ in items:
            if not item.disponible:
                raise ItemNoDisponible(f"'{item.nombre}' no está disponible")
        self._contador += 1
        pedido = Pedido(id=self._contador, cliente=cliente, items=items)
        self._pedidos[pedido.id] = pedido
        # ❌ acoplado a NotificadorConsola
        self._notificador.notificar(cliente, pedido)
        return pedido

    def avanzar_estado(self, pedido_id: int) -> str:
        pedido = self._obtener_o_lanzar(pedido_id)
        pedido.avanzar_estado()
        self._notificador.notificar(pedido.cliente, pedido)
        return pedido.estado

    def total_con_descuento(self, pedido_id: int) -> float:
        pedido = self._obtener_o_lanzar(pedido_id)
        total = pedido.total_items()
        # ❌ acoplado a DescuentoFijo
        return self._descuento.calcular(total, pedido)

    def pedidos_por_estado(self, estado: str) -> list[Pedido]:
        return [p for p in self._pedidos.values() if p.estado == estado]

    def _obtener_o_lanzar(self, pedido_id: int) -> Pedido:
        pedido = self._pedidos.get(pedido_id)
        if pedido is None:
            raise PedidoNoEncontrado(f"No existe el pedido #{pedido_id}")
        return pedido


# ══════════════════════════════════════════════════════
# DEMO
# ══════════════════════════════════════════════════════

if __name__ == "__main__":
    svc = ServicioPedidos()

    hamburguesa  = Plato("P01", "Hamburguesa Clásica", 18500, adicionales=[2000])
    gaseosa      = Bebida("B01", "Cola", 4500, tamanio="L")
    combo_rapido = Combo("C01", "Combo Rápido", [hamburguesa, gaseosa], descuento_porcentaje=12)

    pedido1 = svc.crear_pedido("Carlos Ruiz", [(hamburguesa, 2), (gaseosa, 1)])
    pedido2 = svc.crear_pedido("Sofía Mora",  [(combo_rapido, 1)])

    print(f"\nTotal pedido 1 con descuento: ${svc.total_con_descuento(pedido1.id):.0f}")
    print(f"Total pedido 2 con descuento: ${svc.total_con_descuento(pedido2.id):.0f}")

    svc.avanzar_estado(pedido1.id)
    svc.avanzar_estado(pedido1.id)

    print("\nPedidos en camino:", [p.id for p in svc.pedidos_por_estado("EN_CAMINO")])
```

---

## Parte 1 — Análisis del problema ✏️ (30 min)

Responde **en comentarios al inicio de tu archivo principal** antes de escribir una línea de código nuevo.

**1.1** Identifica las dos clases concretas que `ServicioPedidos` instancia directamente en su constructor. Escríbelas.

```
Tu respuesta:


```

**1.2** Por cada una, describe en una oración el problema que genera ese acoplamiento. Sé específico: ¿qué le impide al sistema hacer?

```
Tu respuesta:


```

**1.3** El equipo de arquitectura necesita dos capacidades nuevas sin tocar `ServicioPedidos`:
- Notificar por SMS además de por consola (para clientes premium).
- Aplicar descuentos distintos según el día (fin de semana 20%, entre semana 5%).

¿Es posible implementar esto con el código actual sin modificar `ServicioPedidos`? ¿Por qué?

```
Tu respuesta:


```

**1.4** ¿Qué principio de diseño se está violando? Nómbralo y explica en una frase qué dice ese principio.

```
Tu respuesta:


```

---

## Parte 2 — Diseño de la solución ✏️ (20 min)

**2.1** Propón los dos `Protocol` que resolverían el problema. Para cada uno indica: nombre, método que define y firma completa de ese método (parámetros y tipo de retorno).

```
Tu propuesta:


```

**2.2** Dibuja (o describe en texto) cómo quedaría el constructor de `ServicioPedidos` después del refactor. ¿Qué parámetros recibe?

```
Tu respuesta:


```

**2.3** ¿Dónde se creará la instancia concreta de cada protocolo? ¿Dentro de `ServicioPedidos` o fuera?

```
Tu respuesta:


```

---

## Parte 3 — Implementación: el paquete `quickbite/` (90 min)

Reestructura el código como paquete, aplicando el diseño que propusiste.

### Estructura esperada

```
quickbite/
├── __init__.py
├── __main__.py
├── excepciones.py
├── modelos.py        # ItemMenu (ABC), Plato, Bebida, Combo, Pedido
├── protocolos.py     # NotificadorPedido (Protocol), CalculadorDescuento (Protocol)
│                     # + al menos 2 implementaciones de cada uno
└── servicio.py       # ServicioPedidos refactorizado con DI
```

### Reglas de implementación

#### `protocolos.py`

- Define `NotificadorPedido` como `Protocol` con el método que diseñaste en la Parte 2.
- Define `CalculadorDescuento` como `Protocol` con el método que diseñaste en la Parte 2.
- Implementa **dos versiones** de cada protocolo (sin heredar del Protocol):

| Protocol | Implementación 1 | Implementación 2 |
|---|---|---|
| `NotificadorPedido` | `NotificadorConsola` | `NotificadorSilencioso` |
| `CalculadorDescuento` | `DescuentoFijo` — porcentaje fijo configurable | `DescuentoPorDia` — porcentaje diferente entre semana vs. fin de semana |

#### `servicio.py`

- `ServicioPedidos` recibe ambas dependencias por constructor. No las instancia internamente.
- El resto de la lógica permanece igual.

#### Comprehensions requeridas

Agrega en `ServicioPedidos` tres métodos adicionales usando comprehensions:

- Uno que retorne los nombres e cantidades de los ítems de un pedido dado (list comprehension).
- Uno que retorne la cantidad de pedidos agrupados por estado (dict comprehension).
- Uno que retorne el conjunto de clientes únicos que han hecho pedidos (set comprehension).

#### `__main__.py`

Escribe una demo que demuestre:

1. Creación del servicio inyectando implementaciones concretas de cada protocolo.
2. Crear al menos 3 pedidos con distintos ítems (incluyendo al menos un `Combo`).
3. Avanzar el estado de al menos un pedido hasta `ENTREGADO`.
4. Intentar avanzar un pedido ya entregado y capturar `TransicionInvalida`.
5. Mostrar los resultados de los tres métodos con comprehensions.
6. **Cambiar el notificador** por el silencioso y crear un nuevo pedido — demostrar que el sistema funciona sin tocar `ServicioPedidos`.

El punto 6 es la prueba definitiva: si funciona sin modificar `ServicioPedidos`, el diseño es correcto.

---

## Parte 4 — Preguntas de reflexión

Responde en comentarios al final de `__main__.py`.

**4.1** El `Combo` calcula su precio como la suma de los precios de sus ítems con descuento. ¿Qué pasa si uno de los ítems dentro del `Combo` es a su vez otro `Combo`? ¿Funciona el código? ¿Por qué?

**4.2** `Pedido.FLUJO_VALIDO` define las transiciones de estado permitidas como un `ClassVar`. ¿Por qué es más limpio tenerlo como `ClassVar` y no como atributo de instancia? ¿Qué pasaría si cada pedido tuviera su propia copia del diccionario?

**4.3** Compara el constructor de `ServicioPedidos` antes y después del refactor. ¿Qué ganaste? ¿Perdiste algo?

**4.4** ¿Qué cambio mínimo habría que hacer para que QuickBite guarde los pedidos en una base de datos en lugar de en un diccionario? ¿Qué archivo habría que crear? ¿Qué archivo no habría que tocar?

---

## Criterios de evaluación

| Criterio | % |
|---|---|
| Parte 1: análisis correcto del problema de acoplamiento | 10 % |
| Parte 2: diseño de protocolos y refactor propuesto correctamente | 10 % |
| Jerarquía `ItemMenu` (ABC + 3 subclases) con lógica de precio correcta | 15 % |
| `Pedido` dataclass con `FLUJO_VALIDO` y transiciones validadas | 10 % |
| Dos `Protocol` bien definidos + 2 implementaciones cada uno | 20 % |
| `ServicioPedidos` refactorizado con DI (sin acoplamientos concretos) | 15 % |
| Paquete con imports relativos, ejecutable con `python -m quickbite` | 10 % |
| Comprehensions (list, dict, set) integradas | 5 % |
| Reflexiones con argumentos claros | 5 % |
