# Inyección de Dependencias y Principio de Inversión de Dependencia

## ¿Qué es una dependencia?

Antes de hablar de inyección ni de inversión, necesitamos tener claro qué es una **dependencia**.

Una **dependencia** es cualquier objeto externo que una clase necesita para hacer su trabajo. Si `ServicioMensajes` necesita un objeto que sepa enviar correos para poder funcionar, entonces ese objeto es una **dependencia** de `ServicioMensajes`.

Piénsenlo así: si eliminan esa otra clase del proyecto y `ServicioMensajes` ya no funciona, era una dependencia.

```python
class ServicioMensajes:
    def __init__(self) -> None:
        self.correo = ClienteSMTP("smtp.gmail.com", 587)  # ← Dependencia

    def enviar_bienvenida(self, email: str) -> None:
        self.correo.enviar(email, "¡Bienvenido!")
```

`ServicioMensajes` depende de `ClienteSMTP`. No puede existir sin él. No puede probarse sin él. No puede cambiar de proveedor de correo sin ser modificado.

---

## Analogías del mundo real

### La batería del control remoto

Un control remoto necesita baterías para funcionar. Esa es su dependencia. Pero el control no **fabrica** sus baterías — viene con un compartimiento donde ustedes las ponen. Si el control fabricara sus propias baterías internamente, sería imposible cambiarlas cuando se agoten, probar el control con baterías recargables, o usar una marca diferente.

El compartimiento de baterías es la **interfaz**. La batería que ustedes ponen es la **implementación inyectada**.

### Los instrumentos de un músico

Un guitarrista profesional no fabrica su guitarra. Le dan una guitarra (o él la elige) y toca con ella. Si mañana necesita un sonido diferente, cambia de guitarra, no se reconstruye como músico. La guitarra es una dependencia **inyectada** — viene de afuera, no se crea adentro.

Si el guitarrista naciera con una guitarra soldada a las manos, nunca podría cambiarla. Eso es una clase que crea sus propias dependencias internamente.

### El laboratorio de química

En un laboratorio, los reactivos no se fabrican en el momento del experimento. Vienen preparados en frascos etiquetados. El científico **recibe** los reactivos y los usa según el protocolo. Si necesita ácido clorhídrico, alguien se lo provee. Él no necesita saber cómo se fabricó — solo necesita que cumpla con la especificación (concentración, pureza). En las pruebas de laboratorio de una universidad, incluso pueden usar simulaciones o sustancias sustitutas para practicar sin riesgo.

---

## Inyección de Dependencias (DI)

### Definición

La **Inyección de Dependencias** (DI, *Dependency Injection*) es una técnica donde las dependencias de un objeto se **pasan desde afuera** en lugar de **crearse adentro**.

Así de simple. No es un framework. No es magia. Es decidir *dónde* se crean los objetos.

### Antes: dependencia creada adentro

```python
class GeneradorInforme:
    def __init__(self) -> None:
        self.repo = RepositorioMySQL("localhost", 3306, "ventas")  # ← Crea su dependencia

    def informe_mensual(self, mes: int) -> str:
        datos = self.repo.obtener_ventas(mes)
        total = sum(v.monto for v in datos)
        return f"Total ventas mes {mes}: ${total:,}"
```

**Problemas:**

1. **Acoplamiento fuerte** — `GeneradorInforme` está soldado a MySQL. Si cambian a PostgreSQL, deben modificar esta clase.
2. **Imposible de probar** — Para correr una prueba necesitan MySQL corriendo con datos.
3. **Configuración oculta** — El host, puerto y base de datos están escondidos adentro. ¿Quién los configuró? ¿De dónde vienen?

### Después: dependencia inyectada

```python
from typing import Protocol


class RepositorioVentas(Protocol):
    def obtener_ventas(self, mes: int) -> list[dict]: ...


class GeneradorInforme:
    def __init__(self, repo: RepositorioVentas) -> None:  # ← Recibe su dependencia
        self.repo = repo

    def informe_mensual(self, mes: int) -> str:
        datos = self.repo.obtener_ventas(mes)
        total = sum(v["monto"] for v in datos)
        return f"Total ventas mes {mes}: ${total:,}"
```

Ahora `GeneradorInforme`:
- **No sabe** de MySQL, PostgreSQL ni de nada.
- **Recibe** cualquier objeto que tenga `obtener_ventas(mes) -> list[dict]`.
- **Se puede probar** con un repositorio falso.
- **Se puede cambiar** de base de datos sin tocarlo.

### Formas de inyectar

En este curso usamos **inyección por constructor** — la forma más común y recomendada:

```python
class GeneradorInforme:
    def __init__(self, repo: RepositorioVentas) -> None:  # ← Por constructor
        self.repo = repo
```

Existen otras formas (inyección por setter, por método, por propiedad), pero la inyección por constructor tiene una ventaja clara: **las dependencias son obligatorias y visibles**. Si `GeneradorInforme` necesita un repositorio, lo pide en el constructor. No hay forma de crear un `GeneradorInforme` sin darle uno.

> 💡 **Regla del curso:** Inyecten siempre por constructor. Es explícito, es simple, y el type checker verifica que pasaron todo lo necesario.

---

## El beneficio más poderoso: testing con fakes

Este es el beneficio más tangible de la Inyección de Dependencias y el que a menudo convence a los escépticos.

### El problema sin DI

Imaginen un servicio de pedidos para una cafetería:

```python
class ServicioPedidos:
    def __init__(self) -> None:
        self.inventario = InventarioPostgreSQL("db.cafeteria.com")
        self.cobro = PasarelaBancolombia("api-key-real")

    def hacer_pedido(self, producto: str, cantidad: int) -> str:
        stock = self.inventario.consultar(producto)
        if stock < cantidad:
            return f"No hay suficiente {producto} en inventario."
        self.cobro.cobrar(producto, cantidad)
        self.inventario.descontar(producto, cantidad)
        return f"Pedido de {cantidad}x {producto} procesado."
```

¿Cómo prueban esto? Necesitan PostgreSQL corriendo, Bancolombia respondiendo, datos reales de inventario. Las pruebas son **lentas**, **frágiles** y **peligrosas** (¿y si cobra de verdad?).

### La solución con DI + fakes

Primero, definimos los contratos:

```python
from typing import Protocol


class Inventario(Protocol):
    def consultar(self, producto: str) -> int: ...
    def descontar(self, producto: str, cantidad: int) -> None: ...


class PasarelaPago(Protocol):
    def cobrar(self, producto: str, cantidad: int) -> None: ...
```

Ahora el servicio recibe sus dependencias:

```python
class ServicioPedidos:
    def __init__(self, inventario: Inventario, pago: PasarelaPago) -> None:
        self.inventario = inventario
        self.pago = pago

    def hacer_pedido(self, producto: str, cantidad: int) -> str:
        stock = self.inventario.consultar(producto)
        if stock < cantidad:
            return f"No hay suficiente {producto} en inventario."
        self.pago.cobrar(producto, cantidad)
        self.inventario.descontar(producto, cantidad)
        return f"Pedido de {cantidad}x {producto} procesado."
```

Para las pruebas, creamos **fakes** — objetos simples que cumplen el contrato pero son controlables:

```python
class InventarioFalso:
    """Inventario en memoria para pruebas."""
    def __init__(self, stock: dict[str, int]) -> None:
        self.stock = stock
        self.descontados: list[tuple[str, int]] = []

    def consultar(self, producto: str) -> int:
        return self.stock.get(producto, 0)

    def descontar(self, producto: str, cantidad: int) -> None:
        self.stock[producto] -= cantidad
        self.descontados.append((producto, cantidad))


class PasarelaFalsa:
    """Pasarela de pago que no cobra de verdad."""
    def __init__(self) -> None:
        self.cobros: list[tuple[str, int]] = []

    def cobrar(self, producto: str, cantidad: int) -> None:
        self.cobros.append((producto, cantidad))
```

Ahora las pruebas son **rápidas**, **deterministas** y **seguras**:

```python
def test_pedido_exitoso() -> None:
    inventario = InventarioFalso({"Café": 10, "Brownie": 5})
    pago = PasarelaFalsa()
    servicio = ServicioPedidos(inventario, pago)

    resultado = servicio.hacer_pedido("Café", 3)

    assert resultado == "Pedido de 3x Café procesado."
    assert inventario.stock["Café"] == 7              # Se descontó
    assert inventario.descontados == [("Café", 3)]    # Se registró
    assert pago.cobros == [("Café", 3)]               # Se cobró (pero no de verdad)


def test_pedido_sin_stock() -> None:
    inventario = InventarioFalso({"Café": 2})
    pago = PasarelaFalsa()
    servicio = ServicioPedidos(inventario, pago)

    resultado = servicio.hacer_pedido("Café", 5)

    assert resultado == "No hay suficiente Café en inventario."
    assert inventario.stock["Café"] == 2    # No se descontó
    assert pago.cobros == []                # No se cobró
```

```python
test_pedido_exitoso()    # ✓ Pasa — sin base de datos, sin internet
test_pedido_sin_stock()  # ✓ Pasa — sin riesgo de cobrar de verdad
print("Todas las pruebas pasaron.")
```

**¿Qué logramos?**

| Sin DI | Con DI + fakes |
|--------|---------------|
| Necesita PostgreSQL corriendo | No necesita ninguna base de datos |
| Necesita Bancolombia disponible | No necesita ninguna pasarela real |
| Pruebas lentas (conexiones de red) | Pruebas instantáneas |
| Podría cobrar de verdad | Nunca cobra de verdad |
| Los datos cambian entre ejecuciones | Los datos son controlados y predecibles |

> 💡 La Inyección de Dependencias no es solo un patrón de diseño elegante. Es una herramienta **práctica** que hace posible probar código de forma **segura** y **rápida**. Si solo se llevan una cosa de este tema, que sea esto.

---

## Principio de Inversión de Dependencia (DIP)

Ahora pasamos de la **técnica** (DI) al **principio** (DIP). Son cosas diferentes y es fundamental que no las confundan.

### Definición formal

El **Principio de Inversión de Dependencia** (DIP, *Dependency Inversion Principle*) es el quinto principio de SOLID y dice:

1. **Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.**
2. **Las abstracciones no deben depender de los detalles. Los detalles deben depender de las abstracciones.**

Eso suena abstracto. Vamos a descomponerlo.

### ¿Qué es "alto nivel" y "bajo nivel"?

- **Alto nivel** = la lógica de negocio. Las reglas. Lo que le importa al cliente. Ejemplos: "procesar un pedido", "calcular el salario", "matricular un estudiante".
- **Bajo nivel** = los detalles técnicos. Cómo se implementa algo. Ejemplos: "guardar en MySQL", "enviar por SMTP", "cobrar con Stripe".

### El problema sin DIP

Sin DIP, la lógica de negocio depende directamente de los detalles técnicos:

```
┌─────────────────────────────┐
│    ServicioPedidos           │  ← Alto nivel (lógica de negocio)
│    (procesar pedidos)        │
└──────────┬──────────────────┘
           │ depende de
           ▼
┌─────────────────────────────┐
│    InventarioPostgreSQL      │  ← Bajo nivel (detalle técnico)
│    (acceso a base de datos)  │
└─────────────────────────────┘
```

La flecha va de arriba (alto nivel) hacia abajo (bajo nivel). Eso parece natural, pero tiene un problema: **si cambian el bajo nivel, se rompe el alto nivel**. Si migran de PostgreSQL a MongoDB, tienen que modificar `ServicioPedidos`. La lógica de negocio está **esclavizada** por los detalles técnicos.

### La inversión

Con DIP, introducimos una **abstracción** (interfaz) en el medio. Ambos niveles dependen de ella:

```
┌─────────────────────────────┐
│    ServicioPedidos           │  ← Alto nivel
│    (procesar pedidos)        │
└──────────┬──────────────────┘
           │ depende de
           ▼
┌─────────────────────────────┐
│    Inventario (Protocol)     │  ← Abstracción (contrato)
│    consultar, descontar      │
└──────────▲──────────────────┘
           │ implementa
┌──────────┴──────────────────┐
│    InventarioPostgreSQL      │  ← Bajo nivel
│    (acceso a base de datos)  │
└─────────────────────────────┘
```

**¿Qué cambió?**

1. `ServicioPedidos` depende de `Inventario` (abstracción), no de `InventarioPostgreSQL` (detalle).
2. `InventarioPostgreSQL` implementa `Inventario` (abstracción).
3. La flecha del bajo nivel **apunta hacia arriba** — hacia la abstracción. Por eso se llama **inversión**: la dirección de la dependencia se invirtió.

Ahora si cambian de PostgreSQL a MongoDB, crean `InventarioMongoDB` que implementa `Inventario`. `ServicioPedidos` no cambia. La lógica de negocio es **independiente** de los detalles técnicos.

---

## DI ≠ DIP

Este es el punto que más confusión genera. Son cosas **distintas** que se complementan:

| | DI (Inyección de Dependencias) | DIP (Principio de Inversión de Dependencia) |
|---|---|---|
| **¿Qué es?** | Una **técnica** | Un **principio** |
| **¿Qué dice?** | "Pasa las dependencias desde afuera" | "Depende de abstracciones, no de implementaciones concretas" |
| **¿Se puede hacer uno sin el otro?** | Sí | Sí |
| **Beneficio principal** | Flexibilidad, testabilidad | Diseño desacoplado, independencia de detalles |

### DI sin DIP (inyectar concretos)

```python
class Reporte:
    def __init__(self, repo: RepositorioMySQL) -> None:  # ← Inyecta, pero es concreto
        self.repo = repo
```

Aquí hay **inyección** (se pasa desde afuera), pero **no hay inversión** (sigue dependiendo de MySQL, una implementación concreta). Si cambian a PostgreSQL, hay que modificar `Reporte`.

### DI + DIP (inyectar abstracciones)

```python
class Reporte:
    def __init__(self, repo: RepositorioVentas) -> None:  # ← Inyecta + abstracción
        self.repo = repo
```

Aquí hay **inyección** (se pasa desde afuera) **y** **inversión** (depende de una abstracción). Puede recibir MySQL, PostgreSQL, un fake, cualquier cosa que cumpla el contrato.

### DIP sin DI (abstracción pero sin inyección)

```python
class Reporte:
    def __init__(self) -> None:
        repo: RepositorioVentas = RepositorioMySQL()  # ← Abstracción en tipo, concreto en creación
        self.repo = repo
```

El type hint dice `RepositorioVentas` (abstracción), pero la instancia es `RepositorioMySQL()` creada adentro. Hay inversión en la declaración del tipo, pero en la práctica sigue acoplado a MySQL.

> 💡 **Lo ideal es usar DI + DIP juntos:** inyectar desde afuera **y** depender de abstracciones. Esa combinación es la que produce diseños verdaderamente flexibles.

---

## Ensamblaje: ¿dónde se crean las instancias concretas?

Si los servicios no crean sus dependencias, ¿quién las crea? **Alguien tiene que hacerlo.** Ese "alguien" es el **punto de ensamblaje**, que normalmente es la función `main()` o el archivo de entrada del programa.

La idea es simple: existe **un solo lugar** donde se crean todas las instancias concretas y se conectan entre sí. El resto del sistema solo conoce abstracciones.

```python
# ── main.py (punto de ensamblaje) ──────────────────

from cafeteria.servicios import ServicioPedidos
from cafeteria.infraestructura.inventario_postgres import InventarioPostgreSQL
from cafeteria.infraestructura.pasarela_bancolombia import PasarelaBancolombia


def main() -> None:
    # Aquí se crean las instancias CONCRETAS
    inventario = InventarioPostgreSQL(host="localhost", puerto=5432)
    pago = PasarelaBancolombia(api_key="key-produccion")

    # Aquí se INYECTAN en el servicio
    servicio = ServicioPedidos(inventario, pago)

    # Aquí se usa el sistema
    resultado = servicio.hacer_pedido("Café", 2)
    print(resultado)


if __name__ == "__main__":
    main()
```

Y para las pruebas, el ensamblaje usa fakes:

```python
# ── test_pedidos.py (ensamblaje de pruebas) ────────

from cafeteria.servicios import ServicioPedidos
from tests.fakes import InventarioFalso, PasarelaFalsa


def test_pedido_exitoso() -> None:
    # Ensamblaje con fakes
    inventario = InventarioFalso({"Café": 10})
    pago = PasarelaFalsa()
    servicio = ServicioPedidos(inventario, pago)

    resultado = servicio.hacer_pedido("Café", 3)
    assert resultado == "Pedido de 3x Café procesado."
```

**La estructura conceptual del proyecto queda así:**

```
cafeteria/
├── servicios.py              ← Lógica de negocio (depende de abstracciones)
├── contratos.py              ← Protocols / ABCs (las abstracciones)
├── infraestructura/
│   ├── inventario_postgres.py ← Implementación concreta (depende de abstracciones)
│   └── pasarela_bancolombia.py
├── main.py                   ← Punto de ensamblaje (crea y conecta todo)
└── tests/
    ├── fakes.py              ← Fakes para pruebas
    └── test_pedidos.py       ← Pruebas (ensamblaje alterno)
```

La lógica de negocio (`servicios.py`) nunca importa nada de `infraestructura/`. Solo importa de `contratos.py`. Los detalles técnicos (`infraestructura/`) implementan los contratos. Y `main.py` los conecta.

> 💡 El punto de ensamblaje es el **único lugar** donde el código sabe qué implementaciones concretas se usan. Si mañana cambian de PostgreSQL a MongoDB, solo se modifica `main.py`.

---

## Ejemplo integrador: Sistema de alertas para un edificio

Veamos un ejemplo completo que combina todo: contratos (Protocol), implementaciones, DI, DIP, ensamblaje y testing. Usamos un dominio diferente a los ejemplos prácticos del curso.

### Los contratos

```python
from typing import Protocol
from dataclasses import dataclass


@dataclass
class Alerta:
    """Representa una alerta del edificio."""
    zona: str
    nivel: str          # "info", "advertencia", "critico"
    descripcion: str


class SensorAlertas(Protocol):
    """Contrato: obtiene las alertas activas."""
    def alertas_activas(self) -> list[Alerta]: ...


class CanalNotificacion(Protocol):
    """Contrato: envía una notificación."""
    def notificar(self, mensaje: str) -> None: ...


class RegistroEventos(Protocol):
    """Contrato: registra un evento."""
    def registrar(self, evento: str) -> None: ...
```

Tres contratos simples. Cada uno define **una sola responsabilidad**.

### El servicio (alto nivel)

```python
class MonitorEdificio:
    """Servicio de alto nivel: revisa alertas y notifica."""

    def __init__(
        self,
        sensor: SensorAlertas,
        canales: list[CanalNotificacion],
        registro: RegistroEventos,
    ) -> None:
        self.sensor = sensor
        self.canales = canales
        self.registro = registro

    def revisar(self) -> int:
        """Revisa alertas activas, notifica las críticas y registra todo.
        Retorna la cantidad de alertas críticas encontradas.
        """
        alertas = self.sensor.alertas_activas()
        criticas = [a for a in alertas if a.nivel == "critico"]

        for alerta in criticas:
            mensaje = f"🚨 ALERTA CRÍTICA en {alerta.zona}: {alerta.descripcion}"
            for canal in self.canales:
                canal.notificar(mensaje)

        self.registro.registrar(
            f"Revisión completada: {len(alertas)} alertas, {len(criticas)} críticas."
        )
        return len(criticas)
```

`MonitorEdificio` **no sabe** de dónde vienen las alertas, ni cómo se envían las notificaciones, ni dónde se registran los eventos. Solo conoce los contratos.

### Las implementaciones concretas (bajo nivel)

```python
class SensorTemperatura:
    """Sensor real que lee temperaturas (simulado)."""
    def __init__(self, umbrales: dict[str, float]) -> None:
        self.umbrales = umbrales
        self._lecturas: dict[str, float] = {}

    def registrar_lectura(self, zona: str, temperatura: float) -> None:
        self._lecturas[zona] = temperatura

    def alertas_activas(self) -> list[Alerta]:
        alertas: list[Alerta] = []
        for zona, temp in self._lecturas.items():
            umbral = self.umbrales.get(zona, 30.0)
            if temp > umbral + 10:
                alertas.append(Alerta(zona, "critico", f"Temperatura: {temp}°C"))
            elif temp > umbral:
                alertas.append(Alerta(zona, "advertencia", f"Temperatura: {temp}°C"))
        return alertas


class NotificacionConsola:
    """Envía notificaciones a la consola."""
    def notificar(self, mensaje: str) -> None:
        print(f"[CONSOLA] {mensaje}")


class NotificacionArchivo:
    """Guarda notificaciones en un archivo."""
    def __init__(self, ruta: str) -> None:
        self.ruta = ruta

    def notificar(self, mensaje: str) -> None:
        with open(self.ruta, "a") as f:
            f.write(mensaje + "\n")


class RegistroEnMemoria:
    """Registra eventos en una lista (útil para pruebas y demos)."""
    def __init__(self) -> None:
        self.eventos: list[str] = []

    def registrar(self, evento: str) -> None:
        self.eventos.append(evento)
```

Ninguna de estas clases hereda de los Protocols. Solo tienen los métodos correctos.

### El ensamblaje (main)

```python
def main() -> None:
    # ── Crear implementaciones concretas ───────────
    sensor = SensorTemperatura(umbrales={"Sótano": 25.0, "Oficina 3": 28.0, "Bodega": 22.0})
    consola = NotificacionConsola()
    registro = RegistroEnMemoria()

    # ── Inyectar en el servicio ────────────────────
    monitor = MonitorEdificio(
        sensor=sensor,
        canales=[consola],
        registro=registro,
    )

    # ── Simular lecturas ──────────────────────────
    sensor.registrar_lectura("Sótano", 26.0)       # Advertencia (> 25)
    sensor.registrar_lectura("Oficina 3", 29.0)     # Advertencia (> 28)
    sensor.registrar_lectura("Bodega", 40.0)         # Crítico (> 22 + 10)

    # ── Ejecutar ──────────────────────────────────
    criticas = monitor.revisar()
    print(f"\nAlertas críticas: {criticas}")
    print(f"Registro: {registro.eventos}")


if __name__ == "__main__":
    main()

# → [CONSOLA] 🚨 ALERTA CRÍTICA en Bodega: Temperatura: 40.0°C
# →
# → Alertas críticas: 1
# → Registro: ['Revisión completada: 3 alertas, 1 críticas.']
```

### Las pruebas con fakes

```python
class SensorFalso:
    """Sensor controlable para pruebas."""
    def __init__(self, alertas: list[Alerta]) -> None:
        self._alertas = alertas

    def alertas_activas(self) -> list[Alerta]:
        return self._alertas


class CanalFalso:
    """Canal que solo registra lo que se intentó enviar."""
    def __init__(self) -> None:
        self.mensajes_enviados: list[str] = []

    def notificar(self, mensaje: str) -> None:
        self.mensajes_enviados.append(mensaje)


class RegistroFalso:
    """Registro que solo acumula eventos."""
    def __init__(self) -> None:
        self.eventos: list[str] = []

    def registrar(self, evento: str) -> None:
        self.eventos.append(evento)


# ── Prueba 1: sin alertas ─────────────────────────
def test_sin_alertas() -> None:
    monitor = MonitorEdificio(
        sensor=SensorFalso([]),
        canales=[CanalFalso()],
        registro=RegistroFalso(),
    )

    criticas = monitor.revisar()

    assert criticas == 0


# ── Prueba 2: con alertas críticas ────────────────
def test_con_criticas() -> None:
    canal = CanalFalso()
    registro = RegistroFalso()
    alertas = [
        Alerta("Bodega", "critico", "Temperatura: 45°C"),
        Alerta("Oficina", "advertencia", "Temperatura: 30°C"),
        Alerta("Sótano", "critico", "Temperatura: 50°C"),
    ]

    monitor = MonitorEdificio(
        sensor=SensorFalso(alertas),
        canales=[canal],
        registro=registro,
    )

    criticas = monitor.revisar()

    assert criticas == 2
    assert len(canal.mensajes_enviados) == 2
    assert "Bodega" in canal.mensajes_enviados[0]
    assert "Sótano" in canal.mensajes_enviados[1]
    assert "3 alertas, 2 críticas" in registro.eventos[0]


# ── Prueba 3: múltiples canales ───────────────────
def test_multiples_canales() -> None:
    canal_1 = CanalFalso()
    canal_2 = CanalFalso()
    alertas = [Alerta("Lobby", "critico", "Humo detectado")]

    monitor = MonitorEdificio(
        sensor=SensorFalso(alertas),
        canales=[canal_1, canal_2],
        registro=RegistroFalso(),
    )

    criticas = monitor.revisar()

    assert criticas == 1
    assert len(canal_1.mensajes_enviados) == 1  # Ambos canales recibieron
    assert len(canal_2.mensajes_enviados) == 1


# ── Ejecutar pruebas ─────────────────────────────
test_sin_alertas()
test_con_criticas()
test_multiples_canales()
print("Todas las pruebas pasaron.")
# → Todas las pruebas pasaron.
```

Sin base de datos. Sin archivos. Sin red. Sin efectos secundarios. **Todo controlado y predecible**.

---

## Anti-patrones

### 1. Dependencias ocultas (el anti-patrón más peligroso)

```python
# ❌ Las dependencias se crean adentro — invisibles desde afuera
class Procesador:
    def __init__(self) -> None:
        self.db = ConexionPostgres()      # ← ¿De dónde sale esto?
        self.cache = ClienteRedis()        # ← ¿Y esto?
        self.logger = LoggerElastic()      # ← ¿Y esto?

    def procesar(self, datos: dict) -> None:
        ...
```

Quien usa `Procesador()` no tiene idea de que necesita PostgreSQL, Redis y Elasticsearch disponibles. No se puede probar, no se puede configurar desde afuera, y los errores son difíciles de diagnosticar.

```python
# ✅ Dependencias explícitas y visibles
class Procesador:
    def __init__(
        self,
        db: BaseDatos,
        cache: Cache,
        logger: Logger,
    ) -> None:
        self.db = db
        self.cache = cache
        self.logger = logger
```

### 2. Service Locator oculto

```python
# ❌ Un "localizador" global que esconde las dependencias
class Registro:
    _servicios: dict[str, object] = {}

    @classmethod
    def obtener(cls, nombre: str) -> object:
        return cls._servicios[nombre]


class Procesador:
    def procesar(self) -> None:
        db = Registro.obtener("base_datos")   # ← Dependencia oculta
        db.guardar(...)                        # ← ¿Qué tipo es db? No se sabe
```

El Service Locator esconde las dependencias detrás de un diccionario global. Es tan malo como crearlas adentro: no se ve qué necesita la clase solo mirando su constructor.

### 3. Inyectar demasiado

```python
# ❌ Si una clase necesita 7 dependencias, el problema es la clase
class SuperServicio:
    def __init__(
        self,
        repo_usuarios: RepoUsuarios,
        repo_pedidos: RepoPedidos,
        repo_productos: RepoProductos,
        notificador: Notificador,
        cobrador: Cobrador,
        logger: Logger,
        cache: Cache,
    ) -> None:
        ...
```

Si el constructor tiene demasiadas dependencias, la clase tiene **demasiadas responsabilidades**. La solución no es quitar DI, sino dividir la clase en clases más pequeñas y cohesivas.

> 💡 **Regla práctica:** Si una clase tiene más de 3-4 dependencias, es señal de que necesita ser dividida.

### 4. DI sin abstracción (inyectar concretos)

```python
# ❌ Inyecta desde afuera, pero sigue acoplado a una implementación
class Reporte:
    def __init__(self, repo: RepositorioMySQL) -> None:  # ← Concreto
        self.repo = repo
```

Hay inyección, pero no hay inversión. Si cambian de MySQL, deben modificar `Reporte`. Siempre inyecten **abstracciones** (Protocol o ABC), no clases concretas.

---

## Buenas prácticas

1. **Siempre inyecten por constructor.** Hace las dependencias explícitas y obligatorias.

2. **Las dependencias deben ser abstracciones** (Protocol o ABC), no clases concretas.

3. **Un solo punto de ensamblaje.** Las instancias concretas se crean en `main()`, no repartidas por todo el código.

4. **Los fakes son ciudadanos de primera clase.** Si no pueden crear un fake simple para una dependencia, el contrato es demasiado complejo.

5. **Cada contrato debe tener una sola responsabilidad.** Un Protocol con 10 métodos probablemente debería ser 2 o 3 Protocols.

6. **Los servicios no crean nada.** Un servicio **recibe** lo que necesita y **hace** su trabajo. Si ven `self.x = ClaseConcreta()` dentro de un servicio, es una señal de alerta.

7. **Nombren los contratos por su función, no por su implementación.** `Inventario`, no `InventarioInterface`. `CanalNotificacion`, no `ICanalNotificacion`.

---

## Errores comunes

### 1. Confundir DI con DIP

"Estoy usando Inyección de Dependencias, así que ya estoy cumpliendo el Principio de Inversión de Dependencia."

**No.** DI es cómo pasas las dependencias. DIP es de qué tipo son. Si inyectas `RepositorioMySQL` (concreto), hay DI pero no DIP.

### 2. Crear interfaces innecesarias

```python
# ❌ Si solo hay UNA implementación y NUNCA habrá otra, no necesitan abstracción
class Calculadora(Protocol):
    def sumar(self, a: int, b: int) -> int: ...

class CalculadoraReal:
    def sumar(self, a: int, b: int) -> int:
        return a + b
```

Las abstracciones solo valen la pena cuando hay **más de una implementación posible** (incluyendo fakes para testing). Si una clase es puramente lógica sin efectos secundarios, probablemente no necesita una interfaz.

### 3. Inyectar valores simples como dependencias

```python
# ❌ No todo necesita ser inyectado
class Calculadora:
    def __init__(self, operador_suma: Sumador) -> None:  # ← Exceso
        self.sumador = operador_suma

# ✅ Los valores y operaciones simples van directamente
class Calculadora:
    def sumar(self, a: int, b: int) -> int:
        return a + b
```

Solo inyecten **servicios y recursos externos**: bases de datos, APIs, sistemas de archivos, notificadores. No inyecten lógica trivial.

### 4. Pensar que DI requiere un framework

DI es solo pasar argumentos al constructor. Python no necesita frameworks especiales para eso. Lo que mostramos en esta nota es DI pura — sin librerías, sin decoradores mágicos, sin configuraciones XML.

---

## Preguntas para pensar

1. **¿Por qué el punto de ensamblaje debe estar en `main.py` y no repartido por el código?** ¿Qué pasaría si cada módulo creara sus propias instancias concretas?

2. **¿Cuándo tiene sentido inyectar una clase concreta (DI sin DIP) en lugar de una abstracción?** ¿Hay casos legítimos?

3. **Si los fakes son tan simples, ¿por qué no usarlos directamente en producción?** ¿Qué diferencia hay entre un fake y una implementación real?

4. **¿Cómo afecta DI/DIP al trabajo en equipo?** ¿Puede un desarrollador trabajar en `ServicioPedidos` mientras otro trabaja en `InventarioPostgreSQL`?

5. **En el ejemplo del edificio, ¿qué pasaría si `MonitorEdificio` necesitara también enviar alertas por WhatsApp?** ¿Qué habría que cambiar y qué no?

---

## Resumen

| Concepto | Idea central |
|----------|-------------|
| **Dependencia** | Objeto externo que una clase necesita para funcionar |
| **DI (Inyección de Dependencias)** | Pasar dependencias desde afuera en vez de crearlas adentro |
| **DIP (Inversión de Dependencia)** | Depender de abstracciones, no de implementaciones concretas |
| **DI ≠ DIP** | DI es la técnica (cómo se pasan), DIP es el principio (de qué tipo son) |
| **Fake / Test double** | Objeto simple que cumple un contrato, para pruebas |
| **Punto de ensamblaje** | `main.py` — el único lugar donde se crean instancias concretas |
| **Inyección por constructor** | La forma recomendada: dependencias como parámetros de `__init__` |

---
