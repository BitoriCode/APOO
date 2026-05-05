# 6. Inyección de Dependencias y DIP

> **Tema de teoría:** [`teoria/5.3 inyeccion_de_dependencias_y_DIP.md`](../teoria/inyeccion_de_dependencias_y_DIP.md)

**Objetivo:** aprender a **desacoplar** servicios de sus detalles concretos usando **Protocol** (contrato estructural), **ABC** (contrato nominal) e **inyección de dependencias** (DI) junto con el **Principio de Inversión de Dependencias** (DIP).

El ejemplo avanza en **tres escenarios progresivos**:

| # | Patrón | Dominio |
|---|--------|---------|
| 1 | Protocol + DI | Notificaciones (`ReportService`) |
| 2 | ABC + DI | Repositorio de productos |
| 3 | ABC + Protocol + DI | Checkout con estrategias de precio y logging |

---

## Escenario 1 — Protocol + DI: Desacoplar notificaciones

### 🔴 ANTES (acoplado a una clase concreta)

```python
# protocol_demo.py (versión ANTES)
class EmailNotifier:
    def send(self, to: str, subject: str, body: str) -> None:
        print(f"[EMAIL] {subject} → {to}: {body}")

class ReportService:
    def __init__(self) -> None:
        #  acoplado: crea el detalle concreto
        self.notifier = EmailNotifier()

    def send_report(self, email: str, text: str) -> None:
        self.notifier.send(email, "Reporte", text)

if __name__ == "__main__":
    svc = ReportService()
    svc.send_report("ana@acme.com", "Hola")
```

```mermaid
classDiagram
  class ReportService {
    +send_report(email: str, text: str): None
  }
  class EmailNotifier {
    +send(to: str, subject: str, body: str): None
  }
  ReportService --> EmailNotifier : depende de detalle
```

### 🟢 DESPUÉS (Protocol + DI + DIP)

```python
# protocol_demo.py (versión DESPUÉS)
from typing import Protocol

class Notifier(Protocol):
    def send(self, to: str, subject: str, body: str) -> None: ...

class EmailNotifier:
    def send(self, to: str, subject: str, body: str) -> None:
        print(f"[EMAIL] {subject} → {to}: {body}")

class SMSNotifier:
    def send(self, to: str, subject: str, body: str) -> None:
        print(f"[SMS] {body} → {to}")

class ReportService:
    # DI: inyecta dependencia
    # DIP: depende de la ABSTRACCIÓN (Notifier)
    def __init__(self, notifier: Notifier) -> None:
        self.notifier = notifier

    def send_report(self, email: str, text: str) -> None:
        self.notifier.send(email, "Reporte", text)

if __name__ == "__main__":
    svc = ReportService(EmailNotifier())   # prod
    # svc = ReportService(SMSNotifier())  # cambio trivial
    svc.send_report("ana@acme.com", "Hola")
```

```mermaid
classDiagram
  class Notifier {
    +send(to: str, subject: str, body: str): None
  }
  <<interface>> Notifier

  class EmailNotifier {
    +send(to: str, subject: str, body: str): None
  }
  class SMSNotifier {
    +send(to: str, subject: str, body: str): None
  }
  class ReportService {
    +send_report(email: str, text: str): None
  }

  EmailNotifier ..|> Notifier
  SMSNotifier ..|> Notifier
  ReportService --> Notifier : depende de abstracción
```

### 💡 Qué aprender aquí

* **Protocol** = contrato **estructural** (sin herencia).
* **DI** (inyectar) + **DIP** (depender de abstracciones) → reemplazos fáciles.

### Ejercicios

1. Crea `PushNotifier` con `send()` compatible y pruébalo en `ReportService`.
2. Escribe un doble de pruebas (`FakeNotifier`) que acumule mensajes y verifica que `send_report` envía lo esperado.
3. Cambia la firma de `Notifier` (agrega un campo) y observa cómo el *type checker* detecta implementaciones incompatibles.

---

## Escenario 2 — ABC + DI: Repositorio de productos

### 🔴 ANTES (acoplado a SQL)

```python
# abc_repo_demo.py (versión ANTES)
from dataclasses import dataclass

@dataclass(slots=True)
class Product:
    id: int
    name: str
    price: float

class SqlProductRepo:
    def __init__(self, dsn: str) -> None:
        self.dsn = dsn
    def add(self, p: Product) -> None: ...
    def get(self, pid: int) -> Product | None: ...
    def list_all(self) -> list[Product]: return []

class ProductService:
    def __init__(self) -> None:
        # acoplamiento directo al detalle
        self.repo = SqlProductRepo("postgres://...")

    def create(self, pid: int, name: str, price: float) -> None:
        self.repo.add(Product(pid, name, price))

if __name__ == "__main__":
    svc = ProductService()
    svc.create(1, "Teclado", 20.0)
```

```mermaid
classDiagram
  class Product {
    +id: int
    +name: str
    +price: float
  }
  class SqlProductRepo {
    +add(p: Product): None
    +get(pid: int): Product | None
    +list_all(): list[Product]
  }
  class ProductService {
    +create(pid:int, name:str, price:float): None
  }

  ProductService --> SqlProductRepo : depende de detalle
  SqlProductRepo o-- Product : gestiona
```

### 🟢 DESPUÉS (ABC + DI + DIP)

```python
# abc_repo_demo.py (versión DESPUÉS)
from dataclasses import dataclass
from abc import ABC, abstractmethod

@dataclass(slots=True)
class Product:
    id: int
    name: str
    price: float

class ProductRepository(ABC):
    @abstractmethod
    def add(self, p: Product) -> None: ...
    @abstractmethod
    def get(self, pid: int) -> Product | None: ...
    @abstractmethod
    def list_all(self) -> list[Product]: ...

class InMemoryProductRepo(ProductRepository):
    def __init__(self) -> None:
        self._data: dict[int, Product] = {}
    def add(self, p: Product) -> None:
        if p.id in self._data: raise ValueError("duplicado")
        self._data[p.id] = p
    def get(self, pid: int) -> Product | None:
        return self._data.get(pid)
    def list_all(self) -> list[Product]:
        return list(self._data.values())

class SqlProductRepo(ProductRepository):
    def __init__(self, dsn: str) -> None:
        self.dsn = dsn
    def add(self, p: Product) -> None: ...
    def get(self, pid: int) -> Product | None: ...
    def list_all(self) -> list[Product]:
        return []

class ProductService:
    # DIP: depende de la ABC
    # DI: inyectamos la implementación
    def __init__(self, repo: ProductRepository) -> None:
        self.repo = repo
    def create(self, pid: int, name: str, price: float) -> None:
        self.repo.add(Product(pid, name, price))

if __name__ == "__main__":
    svc = ProductService(InMemoryProductRepo())      # tests/dev
    # svc = ProductService(SqlProductRepo("postgres://..."))  # prod
    svc.create(1, "Teclado", 20.0)
    print([p.name for p in svc.repo.list_all()])
```

```mermaid
classDiagram
  class Product {
    +id: int
    +name: str
    +price: float
  }

  class ProductRepository {
    +add(p: Product): None
    +get(pid: int): Product | None
    +list_all(): list[Product]
  }
  <<abstract>> ProductRepository

  class InMemoryProductRepo {
    +add(p: Product): None
    +get(pid: int): Product | None
    +list_all(): list[Product]
  }
  class SqlProductRepo {
    +add(p: Product): None
    +get(pid: int): Product | None
    +list_all(): list[Product]
  }

  class ProductService {
    +create(pid:int, name:str, price:float): None
  }

  ProductRepository <|-- InMemoryProductRepo
  ProductRepository <|-- SqlProductRepo
  ProductService --> ProductRepository : depende de abstracción
  InMemoryProductRepo o-- Product : gestiona
  SqlProductRepo o-- Product : gestiona
```

### 💡 Qué aprender aquí

* **ABC** = contrato nominal + posibilidad de **lógica común** compartida.
* Con **DI + DIP**, el servicio **no** conoce detalles de persistencia.

### Ejercicios

1. Añade `update(p: Product) -> None` al contrato. Implementa en memoria y SQL.
2. Implementa `find_by_name(sub: str) -> list[Product]` con **list comprehension**.
3. Crea un decorador `LoggingRepo(ProductRepository)` que envuelva otro repo y loguee llamadas (patrón decorador).

---

## Escenario 3 — ABC + Protocol combinados: Checkout con estrategia y logging

### 🔴 ANTES (acoplado a concreciones)

```python
# combined_demo.py (versión ANTES)
class ConsoleLogger:
    def log(self, msg: str) -> None:
        print("[LOG]", msg)

class TenPercentOff:
    def price_for(self, base: float) -> float:
        return base * 0.9

class CheckoutService:
    def __init__(self) -> None:
        # crea detalles concretos
        self.logger = ConsoleLogger()
        self.rule = TenPercentOff()

    def total(self, items: list[float]) -> float:
        t = sum(self.rule.price_for(x) for x in items)
        self.logger.log(f"total = {t}")
        return t

if __name__ == "__main__":
    svc = CheckoutService()
    print(svc.total([100, 50]))
```

```mermaid
classDiagram
  class ConsoleLogger {
    +log(msg: str): None
  }
  class TenPercentOff {
    +price_for(base: float): float
  }
  class CheckoutService {
    +total(items: list[float]): float
  }

  CheckoutService --> ConsoleLogger : depende de detalle
  CheckoutService --> TenPercentOff : depende de detalle
```

### 🟢 DESPUÉS (ABC + Protocol + DI + DIP)

```python
# combined_demo.py (versión DESPUÉS)
from abc import ABC, abstractmethod
from typing import Protocol

# ABC: contrato + posible lógica común
class PricingRule(ABC):
    @abstractmethod
    def price_for(self, base: float) -> float: ...
    def apply_all(self, items: list[float]) -> list[float]:
        return [self.price_for(x) for x in items]

class TenPercentOff(PricingRule):
    def price_for(self, base: float) -> float:
        return base * 0.9

class Flat5(PricingRule):
    def price_for(self, base: float) -> float:
        return base - 5

# Protocolo: contrato ligero
class Logger(Protocol):
    def log(self, msg: str) -> None: ...

class ConsoleLogger:
    def log(self, msg: str) -> None:
        print("[LOG]", msg)

class CheckoutService:
    # inyecta abstracciones (rule y logger)
    def __init__(self, rule: PricingRule, logger: Logger) -> None:
        self.rule = rule
        self.logger = logger

    def total(self, items: list[float]) -> float:
        t = sum(self.rule.apply_all(items))
        self.logger.log(f"total = {t}")
        return t

if __name__ == "__main__":
    svc = CheckoutService(TenPercentOff(), ConsoleLogger())   # prod
    # svc = CheckoutService(Flat5(), ConsoleLogger())         # cambiar regla
    print(svc.total([100, 50]))
```

```mermaid
classDiagram
  class PricingRule {
    +price_for(base: float): float
    +apply_all(items: list[float]): list[float]
  }
  <<abstract>> PricingRule

  class TenPercentOff {
    +price_for(base: float): float
  }
  class Flat5 {
    +price_for(base: float): float
  }

  class Logger {
    +log(msg: str): None
  }
  <<interface>> Logger

  class ConsoleLogger {
    +log(msg: str): None
  }

  class CheckoutService {
    +total(items: list[float]): float
  }

  PricingRule <|-- TenPercentOff
  PricingRule <|-- Flat5
  ConsoleLogger ..|> Logger
  CheckoutService --> PricingRule : depende de ABC
  CheckoutService --> Logger : depende de Protocol
```

### 💡 Qué aprender aquí

* **Usa ABC** cuando hay **lógica común/plantilla** (ej. `apply_all`).
* **Usa Protocol** cuando solo necesitas un **contrato mínimo** (ej. `log`).
* Un mismo servicio puede depender de **ambos tipos de abstracción**.

### Ejercicios

1. Implementa `ComboRule(PricingRule)` que combine varias reglas (usa `list[PricingRule]`).
2. Crea `FileLogger` (escribe en archivo) y pruébalo con `CheckoutService`.
3. Escribe pruebas con un `FakeLogger` que acumule mensajes y verifica el texto del `total`.
4. (Opcional) Agrega `@dataclass` a una entidad `CartItem` y crea `total(items: list[CartItem])`.

---

## Tabla resumen — ¿Cuándo Protocol vs ABC?

| Criterio | Protocol | ABC |
|----------|----------|-----|
| **Vínculo** | Estructural (forma) | Nominal (herencia explícita) |
| **Lógica compartida** | No | Sí (métodos concretos) |
| **Ideal para** | Contratos ligeros, duck typing | Repositorios, estrategias con plantilla |
| **Detección de errores** | Type checker (`mypy`) | Runtime + Type checker |

---

## Referencias

- Teoría: [`teoria/inyeccion_de_dependencias_y_DIP.md`](../teoria/inyeccion_de_dependencias_y_DIP.md)
- Protocolos: [`ejemplos/protocolos_e_interfaces.md`](protocolos_e_interfaces.md)

