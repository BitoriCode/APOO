# Herencia y Polimorfismo

## ¿Qué es la herencia?

En la vida real, heredamos cosas: rasgos físicos de nuestros padres, costumbres de nuestra cultura, conocimientos de nuestros maestros. La idea central es que **lo que ya existe se transmite** y quien lo recibe puede usarlo tal cual, adaptarlo o extenderlo.

En programación orientada a objetos, la **herencia** es exactamente eso: un mecanismo que permite crear una clase nueva (la **subclase** o clase hija) a partir de una clase existente (la **superclase** o clase padre). La subclase **recibe** todos los atributos y métodos de la superclase, y puede:

1. **Usarlos tal cual** — sin cambiar nada.
2. **Sobrescribir** algunos — cambiar su comportamiento.
3. **Extender** — agregar nuevos atributos y métodos que la superclase no tenía.

La relación que establece la herencia es de tipo **"es un(a)"**. Si `Perro` hereda de `Animal`, estamos diciendo que un perro **es un** animal. Si `CuentaAhorros` hereda de `CuentaBancaria`, estamos diciendo que una cuenta de ahorros **es una** cuenta bancaria. Si esa frase no tiene sentido semántico ("un motor **es un** auto"... no), entonces la herencia no es la relación correcta.

---

## Analogías del mundo real

### Las recetas de familia

Su abuela tiene una receta de empanadas. Su mamá la heredó y la usa tal cual para la masa, pero le cambió el relleno (sobrescribió una parte). Ustedes heredaron la receta de su mamá y la extendieron agregando una salsa nueva que nadie más hace. La receta original sigue existiendo, cada generación puede usarla, adaptarla o ampliarla.

### Los formularios preimpresos

Piensen en un formulario base de "Solicitud" en una universidad: tiene campos como nombre, fecha, firma. El formulario de "Solicitud de matrícula" **hereda** esos campos y agrega los suyos: programa, semestre, código. El formulario de "Solicitud de grado" también hereda los campos base pero agrega otros: promedio, fecha de sustentación. Todos son solicitudes (relación "es un(a)"), pero cada uno tiene sus particularidades.

### La clasificación biológica

El reino animal es una jerarquía de herencia: `SerVivo → Animal → Mamífero → Felino → Gato`. Cada nivel hereda las características del anterior y agrega las suyas. Un gato tiene todo lo de un felino (garras retráctiles, visión nocturna), que a su vez tiene todo lo de un mamífero (sangre caliente, glándulas mamarias), y así sucesivamente.

---

## Herencia simple en Python

### Sintaxis básica

Para indicar que una clase hereda de otra, se pone la clase padre entre paréntesis:

```python
class Animal:
    def __init__(self, nombre: str, sonido: str) -> None:
        self.nombre = nombre
        self.sonido = sonido

    def presentarse(self) -> str:
        return f"Soy {self.nombre} y hago {self.sonido}."


class Perro(Animal):  # ← Perro hereda de Animal
    pass               # No agrega nada por ahora
```

Con solo eso, `Perro` ya tiene todo lo que tiene `Animal`:

```python
firulais = Perro("Firulais", "guau")
print(firulais.presentarse())  # → Soy Firulais y hago guau.
print(firulais.nombre)         # → Firulais
print(firulais.sonido)         # → guau
```

`Perro` no definió `__init__` ni `presentarse`, pero los tiene porque los **heredó** de `Animal`.

### `super()` — acceder a la clase padre

Cuando la subclase necesita su propio `__init__` (porque tiene atributos adicionales), debe llamar al `__init__` del padre para que se inicialicen los atributos heredados. Para eso se usa `super()`:

```python
class Perro(Animal):
    def __init__(self, nombre: str, sonido: str, raza: str) -> None:
        super().__init__(nombre, sonido)  # ← Inicializa nombre y sonido (del padre)
        self.raza = raza                  # ← Atributo propio de Perro

    def descripcion(self) -> str:
        return f"{self.nombre} es un {self.raza}."
```

```python
rex = Perro("Rex", "guau", "Pastor Alemán")
print(rex.presentarse())   # → Soy Rex y hago guau.        (heredado)
print(rex.descripcion())   # → Rex es un Pastor Alemán.     (propio)
```

`super()` devuelve una referencia a la clase padre. `super().__init__(nombre, sonido)` es como decir: "ejecuta el constructor del padre con estos argumentos". Así no duplicamos la lógica de inicialización.

> 💡 **Regla práctica:** Siempre que una subclase defina su propio `__init__`, debe llamar a `super().__init__(...)` como primera instrucción. Si no lo hacen, los atributos del padre no se inicializan y van a tener errores.

---

## Sobrescritura de métodos (*overriding*)

### ¿Qué es sobrescribir?

Sobrescribir un método significa que la subclase **redefine** un método que ya existía en la superclase. Cuando se llama ese método en una instancia de la subclase, se ejecuta la versión de la subclase, no la del padre.

```python
class CuentaBancaria:
    def __init__(self, titular: str, saldo: int) -> None:
        self.titular = titular
        self.saldo = saldo

    def calcular_interes(self) -> int:
        return 0  # Cuenta base no genera interés

    def __str__(self) -> str:
        return f"Cuenta de {self.titular} — Saldo: ${self.saldo:,}"


class CuentaAhorros(CuentaBancaria):
    def __init__(self, titular: str, saldo: int, tasa: float) -> None:
        super().__init__(titular, saldo)
        self.tasa = tasa

    def calcular_interes(self) -> int:
        # Sobrescribe: ahora SÍ calcula interés
        return int(self.saldo * self.tasa)
```

```python
base = CuentaBancaria("Ana", 1_000_000)
ahorro = CuentaAhorros("Luis", 1_000_000, 0.04)

print(base.calcular_interes())    # → 0          (versión de CuentaBancaria)
print(ahorro.calcular_interes())  # → 40000      (versión de CuentaAhorros)
```

### El decorador `@override` (Python 3.12+)

Desde Python 3.12, existe el decorador `@override` del módulo `typing`. No cambia el comportamiento del código, pero le dice al **type checker** (y a quien lea el código): *"este método intencionalmente sobrescribe uno del padre"*.

```python
from typing import override

class CuentaAhorros(CuentaBancaria):
    def __init__(self, titular: str, saldo: int, tasa: float) -> None:
        super().__init__(titular, saldo)
        self.tasa = tasa

    @override
    def calcular_interes(self) -> int:
        return int(self.saldo * self.tasa)
```

**¿Por qué usarlo?**

* Si escriben `@override` pero el método NO existe en el padre (porque lo escribieron mal o lo renombraron), el type checker les avisa. Sin `@override`, el error pasa silencioso.
* Hace el código más legible: al ver `@override` saben que ese método viene del padre.

En este curso usamos `@override` en los ejemplos prácticos. Aún no es obligatorio, pero es una buena práctica.

### Extender vs. reemplazar

Hay dos formas de sobrescribir:

**Reemplazar completamente** — no llaman a `super()`:

```python
class CuentaAhorros(CuentaBancaria):
    @override
    def calcular_interes(self) -> int:
        # Lógica completamente nueva, ignora la del padre
        return int(self.saldo * self.tasa)
```

**Extender** — llaman a `super()` y agregan algo:

```python
class CuentaEspecial(CuentaBancaria):
    @override
    def __str__(self) -> str:
        base = super().__str__()  # ← Reutiliza la representación del padre
        return f"⭐ {base} (Cuenta Especial)"
```

```python
cuenta = CuentaEspecial("María", 500_000)
print(cuenta)  # → ⭐ Cuenta de María — Saldo: $500,000 (Cuenta Especial)
```

**¿Cuándo usar cada uno?**

* **Reemplazar** cuando el comportamiento del padre no aplica en absoluto para la subclase.
* **Extender** cuando el comportamiento del padre es útil pero necesita información adicional. Es el caso más común.

---

## Herencia múltiple

Python permite que una clase herede de **más de una clase padre**. Esto se llama herencia múltiple:

```python
class ClaseHija(Padre1, Padre2, Padre3):
    pass
```

`ClaseHija` hereda atributos y métodos de `Padre1`, `Padre2` y `Padre3`. Es una herramienta poderosa pero que hay que usar con cuidado, porque introduce complejidades que no existen en herencia simple.

### El problema del diamante

El **problema del diamante** es el escenario clásico que complica la herencia múltiple. Ocurre cuando dos clases heredan de una misma base, y luego una cuarta clase hereda de esas dos:

```
        Base
       /    \
    Padre1  Padre2
       \    /
       Hija
```

La pregunta es: si `Base`, `Padre1` y `Padre2` definen el mismo método, **¿cuál versión ejecuta `Hija`?**

Veámoslo con código:

```python
class Empleado:
    def identificarse(self) -> str:
        return "Soy un empleado de la empresa."

class Gerente(Empleado):
    def identificarse(self) -> str:
        return "Soy un gerente."

class Ingeniero(Empleado):
    def identificarse(self) -> str:
        return "Soy un ingeniero."

class GerenteIngeniero(Gerente, Ingeniero):
    pass  # No define identificarse — ¿cuál hereda?
```

```python
gi = GerenteIngeniero()
print(gi.identificarse())  # → Soy un gerente.
```

¿Por qué "un gerente" y no "un ingeniero"? Por el **MRO**.

### MRO: Method Resolution Order (Orden de Resolución de Métodos)

El MRO es el **algoritmo** que Python usa para decidir en qué orden busca un método cuando hay herencia múltiple. Python usa un algoritmo llamado **linearización C3** que genera una lista ordenada de clases. Cuando llaman a un método, Python busca en esa lista de izquierda a derecha y ejecuta la **primera versión que encuentre**.

Pueden inspeccionar el MRO de cualquier clase:

```python
print(GerenteIngeniero.__mro__)
```

Salida:

```
(<class 'GerenteIngeniero'>, <class 'Gerente'>, <class 'Ingeniero'>,
 <class 'Empleado'>, <class 'object'>)
```

Esto dice: cuando `GerenteIngeniero` necesita un método, Python busca primero en `GerenteIngeniero`, luego en `Gerente`, luego en `Ingeniero`, luego en `Empleado`, y finalmente en `object` (la clase raíz de todo en Python).

Como `Gerente` aparece antes que `Ingeniero` en el MRO, y `Gerente` define `identificarse`, esa es la versión que se ejecuta.

### ¿De qué depende el MRO?

Del **orden en que listan las clases padre**:

```python
class GerenteIngeniero(Gerente, Ingeniero):  # Gerente primero
    pass

class IngenieroGerente(Ingeniero, Gerente):  # Ingeniero primero
    pass

print(GerenteIngeniero().identificarse())  # → Soy un gerente.
print(IngenieroGerente().identificarse())  # → Soy un ingeniero.
```

El orden importa. La primera clase padre en la lista tiene prioridad.

### `super()` cooperativo en herencia múltiple

Cuando hay herencia múltiple, `super()` no significa "la clase padre". Significa **"la siguiente clase en el MRO"**. Esto permite que todas las clases en la jerarquía **cooperen**, ejecutando su parte en cadena:

```python
class Componente:
    def configurar(self) -> list[str]:
        return ["Componente base configurado"]

class ConRed(Componente):
    def configurar(self) -> list[str]:
        pasos = super().configurar()
        pasos.append("Red configurada")
        return pasos

class ConAlmacenamiento(Componente):
    def configurar(self) -> list[str]:
        pasos = super().configurar()
        pasos.append("Almacenamiento configurado")
        return pasos

class Servidor(ConRed, ConAlmacenamiento):
    def configurar(self) -> list[str]:
        pasos = super().configurar()
        pasos.append("Servidor listo")
        return pasos
```

```python
s = Servidor()
for paso in s.configurar():
    print(f"  ✓ {paso}")
```

Salida:

```
  ✓ Componente base configurado
  ✓ Almacenamiento configurado
  ✓ Red configurada
  ✓ Servidor listo
```

El MRO de `Servidor` es: `Servidor → ConRed → ConAlmacenamiento → Componente → object`. Cada `super().configurar()` llama a la **siguiente clase en ese orden**, y así se ejecutan todas en cadena. Esto es lo que se llama `super()` cooperativo.

> 💡 **Importante:** Para que `super()` cooperativo funcione correctamente, **todas las clases en la jerarquía** deben llamar a `super()`. Si una clase rompe la cadena (no llama a `super()`), las clases que vienen después en el MRO no se ejecutan.

---

## Mixins

### ¿Qué es un mixin?

Un **mixin** es una clase diseñada para **agregar funcionalidad específica** a otras clases mediante herencia múltiple, sin pretender ser una clase completa por sí misma. No modela una entidad del dominio; solo aporta un comportamiento reutilizable.

La diferencia con una clase padre normal:

| | Clase padre | Mixin |
|---|---|---|
| **¿Modela una entidad?** | Sí: `Animal`, `CuentaBancaria` | No: solo agrega una capacidad |
| **¿Se instancia sola?** | Generalmente sí | Nunca (no tiene sentido por sí sola) |
| **¿Define `__init__`?** | Sí | Raramente |
| **Relación** | "es un(a)" | "puede hacer" |

### Convención de nombres

Por convención, los mixins llevan el sufijo `Mixin` en su nombre: `LogMixin`, `SerializableMixin`, `ValidableMixin`. Esto comunica al lector que no es una clase independiente.

### Ejemplo práctico

```python
import json
from dataclasses import dataclass, asdict

class SerializableMixin:
    """Agrega la capacidad de convertir cualquier dataclass a JSON."""

    def a_json(self) -> str:
        return json.dumps(asdict(self), ensure_ascii=False, indent=2)  # type: ignore[arg-type]


class AuditableMixin:
    """Agrega la capacidad de registrar operaciones."""

    def registrar(self, operacion: str) -> str:
        nombre_clase = type(self).__name__
        return f"[AUDIT] {nombre_clase}: {operacion}"


@dataclass
class Producto(SerializableMixin, AuditableMixin):
    codigo: str
    nombre: str
    precio: int
    stock: int
```

```python
p = Producto("A01", "Café", 3500, 20)
print(p.a_json())
# {
#   "codigo": "A01",
#   "nombre": "Café",
#   "precio": 3500,
#   "stock": 20
# }

print(p.registrar("creación de producto"))
# → [AUDIT] Producto: creación de producto
```

`Producto` **es un** producto (su propia clase) y **puede** serializarse a JSON (`SerializableMixin`) y registrar auditoría (`AuditableMixin`). Los mixins solo aportan funcionalidad, no modelan nada por sí mismos.

> 💡 **Regla práctica:** Los mixins van **primero** en la lista de herencia (antes de la clase padre principal). Esto les da prioridad en el MRO y evita que sobrescriban métodos importantes de la clase padre:
> ```python
> class MiClase(MixinA, MixinB, ClasePadre):
>     pass
> ```

---

## Clases abstractas (ABC) y métodos abstractos

### Repaso: ¿qué son?

Como vimos en la nota de los pilares de POO, las **clases abstractas** definen un contrato: dicen **qué deben hacer** las subclases, sin definir **cómo**. No se pueden instanciar directamente; solo sirven como base para otras clases.

```python
from abc import ABC, abstractmethod

class FiguraGeometrica(ABC):
    @abstractmethod
    def area(self) -> float:
        """Todas las figuras deben poder calcular su área."""
        pass

    @abstractmethod
    def perimetro(self) -> float:
        """Todas las figuras deben poder calcular su perímetro."""
        pass

    def descripcion(self) -> str:
        """Método concreto — las subclases lo heredan tal cual."""
        return f"{type(self).__name__}: área={self.area():.2f}, perímetro={self.perimetro():.2f}"
```

```python
class Rectangulo(FiguraGeometrica):
    def __init__(self, ancho: float, alto: float) -> None:
        self.ancho = ancho
        self.alto = alto

    def area(self) -> float:
        return self.ancho * self.alto

    def perimetro(self) -> float:
        return 2 * (self.ancho + self.alto)


class Circulo(FiguraGeometrica):
    def __init__(self, radio: float) -> None:
        self.radio = radio

    def area(self) -> float:
        return 3.14159 * self.radio ** 2

    def perimetro(self) -> float:
        return 2 * 3.14159 * self.radio
```

```python
# fig = FiguraGeometrica()  # TypeError: no se puede instanciar
r = Rectangulo(5, 3)
c = Circulo(4)

print(r.descripcion())  # → Rectangulo: área=15.00, perímetro=16.00
print(c.descripcion())  # → Circulo: área=50.27, perímetro=25.13
```

Noten que `descripcion()` es un método **concreto** en la clase abstracta. Las subclases lo heredan sin tener que redefinirlo. Eso es válido y útil: las ABCs pueden mezclar métodos abstractos (que las subclases deben implementar) con métodos concretos (que las subclases heredan).

### Propiedades abstractas

También pueden definir **propiedades** abstractas combinando `@property` con `@abstractmethod`:

```python
from abc import ABC, abstractmethod

class Forma(ABC):
    @property
    @abstractmethod
    def nombre(self) -> str:
        """Cada forma debe proveer su nombre."""
        pass


class Triangulo(Forma):
    @property
    def nombre(self) -> str:
        return "Triángulo"
```

```python
t = Triangulo()
print(t.nombre)  # → Triángulo
```

El orden de los decoradores importa: `@property` va **arriba**, `@abstractmethod` va **abajo** (el más cercano a la función).

### ABC vs. Protocol: ¿cuándo usar cada uno?

Existen dos formas de definir contratos en Python: **ABC** (Abstract Base Classes) y **Protocol** (`typing.Protocol`). Aquí las presentamos brevemente — las veremos con mayor profundidad en las notas de protocolos e interfaces. ¿Cuándo usar cada una?

| Aspecto | ABC | Protocol |
|---------|-----|----------|
| **Tipo de relación** | Nominal: la subclase **debe heredar** explícitamente | Estructural: basta con que **tenga los métodos** |
| **Herencia requerida** | Sí: `class Repo(RepositorioBase)` | No: la clase solo implementa los métodos |
| **Métodos concretos compartidos** | Sí: puede tener métodos con implementación | No: solo define la firma de los métodos |
| **Validación** | En tiempo de ejecución: `TypeError` si no implementan | En tiempo de análisis: el type checker avisa |
| **Uso típico** | Contratos dentro de su propio proyecto | Contratos con código externo o cuando no quieren forzar herencia |

**Regla simple:**
* **ABC** cuando necesitan compartir lógica entre las implementaciones o quieren forzar la herencia.
* **Protocol** cuando solo necesitan definir "qué métodos debe tener" sin imponer una jerarquía.

En los ejercicios del curso (máquina expendedora, boletería, almacén), usamos ABCs para los repositorios porque queremos un contrato formal que fuerce la implementación. Usamos Protocols para cosas como notificadores o reglas de precio, donde solo importa que el objeto "pueda hacer algo".

---

## Polimorfismo

### ¿Qué es el polimorfismo?

La palabra viene del griego: *poli* (muchas) + *morfo* (formas). **Polimorfismo** significa que una misma operación puede comportarse de forma diferente según el objeto que la ejecute.

Piénsenlo así: el mensaje `calcular_costo()` lo pueden recibir una `SuscripcionPremium`, una `SuscripcionEducativa` o una `SuscripcionPrepago`. Cada una responde de manera diferente (aplica descuentos distintos, cobra diferente), pero el código que las usa no necesita saber cuál es cuál. Solo dice: **"calcula tu costo"**, y cada objeto sabe cómo hacerlo.

### Polimorfismo de subtipo (el más común con herencia)

Es el polimorfismo que resulta directamente de la herencia y la sobrescritura. El código trabaja con el tipo padre, y el comportamiento real depende del tipo hijo:

```python
from abc import ABC, abstractmethod

class Notificacion(ABC):
    def __init__(self, destinatario: str, mensaje: str) -> None:
        self.destinatario = destinatario
        self.mensaje = mensaje

    @abstractmethod
    def enviar(self) -> str:
        pass


class NotificacionEmail(Notificacion):
    def enviar(self) -> str:
        return f"[EMAIL] → {self.destinatario}: {self.mensaje}"


class NotificacionSMS(Notificacion):
    def enviar(self) -> str:
        return f"[SMS] → {self.destinatario}: {self.mensaje}"


class NotificacionPush(Notificacion):
    def enviar(self) -> str:
        return f"[PUSH] → {self.destinatario}: {self.mensaje}"
```

El código que *usa* las notificaciones no necesita saber el tipo concreto:

```python
def enviar_todas(notificaciones: list[Notificacion]) -> None:
    for n in notificaciones:
        print(n.enviar())  # ← Mismo método, comportamiento diferente

notificaciones: list[Notificacion] = [
    NotificacionEmail("ana@correo.com", "Bienvenida"),
    NotificacionSMS("310-555-1234", "Código: 4829"),
    NotificacionPush("luis_app", "Tienes un nuevo mensaje"),
]

enviar_todas(notificaciones)
```

Salida:

```
[EMAIL] → ana@correo.com: Bienvenida
[SMS] → 310-555-1234: Código: 4829
[PUSH] → luis_app: Tienes un nuevo mensaje
```

La función `enviar_todas` recibe una lista de `Notificacion` (el tipo padre). No le importa si es email, SMS o push. Solo llama a `enviar()` y cada objeto hace lo suyo. **Eso es polimorfismo.**

### Duck typing: polimorfismo sin herencia

En Python, el polimorfismo no requiere herencia. Gracias al **duck typing** (*"si camina como un pato y hace cuac como un pato, entonces es un pato"*), cualquier objeto que tenga el método correcto funciona, sin importar su tipo:

```python
class Impresora:
    def procesar(self, texto: str) -> str:
        return f"Imprimiendo: {texto}"

class Escaner:
    def procesar(self, texto: str) -> str:
        return f"Escaneando: {texto}"

class Fax:
    def procesar(self, texto: str) -> str:
        return f"Enviando fax: {texto}"


def ejecutar_trabajo(dispositivo: object, texto: str) -> None:
    # No nos importa el tipo. Solo que tenga el método 'procesar'
    print(dispositivo.procesar(texto))  # type: ignore[attr-defined]


ejecutar_trabajo(Impresora(), "Documento.pdf")  # → Imprimiendo: Documento.pdf
ejecutar_trabajo(Escaner(), "Foto.jpg")          # → Escaneando: Foto.jpg
ejecutar_trabajo(Fax(), "Contrato.pdf")          # → Enviando fax: Contrato.pdf
```

`Impresora`, `Escaner` y `Fax` no heredan de nada en común, pero todas tienen `procesar()`. Python no verifica tipos; verifica que el método exista. Esto es duck typing.

> 💡 **¿Y los Protocol?** Los `typing.Protocol` formalizan el duck typing: definen qué métodos debe tener un objeto, y el type checker verifica que los tenga, pero sin requerir herencia. Es duck typing con red de seguridad.

### Sobrecarga de operadores (polimorfismo de operadores)

Python permite que las clases definan cómo se comportan con operadores (`+`, `-`, `*`, `==`, `<`, `len()`, `str()`, etc.) implementando métodos especiales (métodos *dunder*):

```python
class Dinero:
    def __init__(self, cantidad: int, moneda: str = "COP") -> None:
        self.cantidad = cantidad
        self.moneda = moneda

    def __add__(self, otro: "Dinero") -> "Dinero":
        if self.moneda != otro.moneda:
            raise ValueError(f"No se pueden sumar {self.moneda} y {otro.moneda}.")
        return Dinero(self.cantidad + otro.cantidad, self.moneda)

    def __str__(self) -> str:
        return f"${self.cantidad:,} {self.moneda}"

    def __eq__(self, otro: object) -> bool:
        if not isinstance(otro, Dinero):
            return NotImplemented
        return self.cantidad == otro.cantidad and self.moneda == otro.moneda
```

```python
a = Dinero(50_000)
b = Dinero(30_000)
c = a + b              # Usa __add__
print(c)               # → $80,000 COP (usa __str__)
print(a == b)          # → False (usa __eq__)
print(a == Dinero(50_000))  # → True
```

El operador `+` se "comporta diferentemente" dependiendo del tipo: para enteros hace suma aritmética, para strings hace concatenación, para `Dinero` suma cantidades en la misma moneda. Eso es polimorfismo de operadores.

Los métodos dunder más comunes que van a encontrar:

| Operador/Función | Método | Ejemplo |
|-----------------|--------|---------|
| `+` | `__add__` | `a + b` |
| `-` | `__sub__` | `a - b` |
| `==` | `__eq__` | `a == b` |
| `<` | `__lt__` | `a < b` |
| `len()` | `__len__` | `len(a)` |
| `str()` / `print()` | `__str__` | `print(a)` |
| `repr()` | `__repr__` | `repr(a)` |
| `in` | `__contains__` | `x in a` |

No necesitan memorizar todos. Lo importante es entender el concepto: los operadores en Python son polimórficos porque cada clase puede definir su propio comportamiento.

---

## Composición vs. Herencia

Este es uno de los temas más importantes de diseño orientado a objetos. Existe un principio ampliamente aceptado: **"Favorece la composición sobre la herencia"** (*Favor composition over inheritance*). Pero esto no significa que la herencia sea mala. Significa que hay que saber cuándo usar cada una.

### ¿Qué es la composición?

La composición es cuando una clase **contiene** a otra como atributo, en vez de heredar de ella. La relación es **"tiene un(a)"** en vez de **"es un(a)"**.

```python
# HERENCIA: un Estudiante ES UNA Persona
class Persona:
    def __init__(self, nombre: str) -> None:
        self.nombre = nombre

class Estudiante(Persona):
    def __init__(self, nombre: str, programa: str) -> None:
        super().__init__(nombre)
        self.programa = programa


# COMPOSICIÓN: un Curso TIENE UN profesor (no es un profesor)
class Curso:
    def __init__(self, nombre: str, profesor: Persona) -> None:
        self.nombre = nombre
        self.profesor = profesor  # ← Composición: tiene una Persona
```

### ¿Cuándo usar herencia?

Usen herencia cuando:

* La relación **"es un(a)"** tiene sentido: un `Perro` **es un** `Animal`, una `CuentaAhorros` **es una** `CuentaBancaria`.
* Las subclases necesitan **todo** (o casi todo) lo que tiene la superclase.
* Quieren definir un **contrato** que las subclases deben cumplir (clases abstractas).
* Quieren **polimorfismo de subtipo**: tratar diferentes subclases de forma uniforme.

### ¿Cuándo usar composición?

Usen composición cuando:

* La relación **"tiene un(a)"** es más natural: un `Curso` **tiene** un profesor, un `Auto` **tiene** un motor.
* Solo necesitan **parte** de la funcionalidad de la otra clase, no toda.
* Quieren poder **cambiar** el componente en tiempo de ejecución (un auto puede cambiar de motor, un curso puede cambiar de profesor).
* La herencia crearía una jerarquía forzada que no tiene sentido semántico.

### Ejemplo: herencia forzada vs. composición natural

**Herencia forzada (malo):**

```python
# Un Reporte NO ES una ConexionBD. Esto no tiene sentido semántico.
class ConexionBD:
    def ejecutar_consulta(self, sql: str) -> list[dict]:
        # ... lógica de conexión ...
        return []

class Reporte(ConexionBD):  # ← ¿Un reporte es una conexión? No.
    def generar(self) -> str:
        datos = self.ejecutar_consulta("SELECT * FROM ventas")
        return f"Reporte con {len(datos)} registros"
```

El programador usó herencia solo para **reutilizar** `ejecutar_consulta()`, pero un reporte **no es** una conexión a base de datos. Esto acopla el reporte a la conexión, y si mañana quieren usar otra fuente de datos (un archivo, una API), tienen que cambiar la herencia.

**Composición natural (bueno):**

```python
class ConexionBD:
    def ejecutar_consulta(self, sql: str) -> list[dict]:
        return []

class Reporte:
    def __init__(self, conexion: ConexionBD) -> None:
        self._conexion = conexion  # ← Composición: TIENE una conexión

    def generar(self) -> str:
        datos = self._conexion.ejecutar_consulta("SELECT * FROM ventas")
        return f"Reporte con {len(datos)} registros"
```

Ahora el reporte **usa** una conexión, pero no **es** una. Pueden cambiar la conexión por otra sin modificar la clase `Reporte`. Esto es más flexible y semánticamente correcto.

### Flowchart de decisión

```
¿La relación es "es un(a)"?
    │
    ├── SÍ → ¿La subclase necesita TODO lo del padre?
    │         │
    │         ├── SÍ → ¿Va a haber polimorfismo?
    │         │         │
    │         │         ├── SÍ → ✅ HERENCIA
    │         │         └── NO → Considerar composición igualmente
    │         │
    │         └── NO → ✅ COMPOSICIÓN (solo necesita parte)
    │
    └── NO → ✅ COMPOSICIÓN
```

---

## Principio de Sustitución de Liskov (LSP)

### ¿Qué dice?

El **Principio de Sustitución de Liskov** dice: *"Si S es subclase de T, entonces los objetos de tipo T pueden ser reemplazados por objetos de tipo S sin alterar el comportamiento correcto del programa."*

En español sencillo: **si el código funciona con el padre, debe funcionar igual con cualquiera de sus hijos**. Los hijos pueden hacer cosas adicionales, pero **no pueden romper las promesas del padre**.

### Ejemplo de violación

```python
class Ave:
    def volar(self) -> str:
        return "Estoy volando."

class Aguila(Ave):
    def volar(self) -> str:
        return "Vuelo alto sobre las montañas."

class Pinguino(Ave):
    def volar(self) -> str:
        raise RuntimeError("¡Los pingüinos no pueden volar!")  # ← VIOLA LSP
```

Si tienen código que espera un `Ave` y llama a `volar()`, va a funcionar con `Aguila` pero va a explotar con `Pinguino`. El `Pinguino` **rompió la promesa** del `Ave`: se suponía que todas las aves podían volar.

### ¿Cómo corregirlo?

Reorganizando la jerarquía para que refleje mejor la realidad:

```python
from abc import ABC, abstractmethod

class Ave(ABC):
    @abstractmethod
    def moverse(self) -> str:
        pass

class AveVoladora(Ave):
    def moverse(self) -> str:
        return "Vuelo por el cielo."

class AveNoVoladora(Ave):
    def moverse(self) -> str:
        return "Camino sobre el hielo."


class Aguila(AveVoladora):
    pass

class Pinguino(AveNoVoladora):
    pass
```

Ahora el código que trabaja con `Ave` solo espera `moverse()`, y tanto `Aguila` como `Pinguino` cumplen sin sorpresas. El LSP se respeta porque **ninguna subclase rompe las promesas de su padre**.

> 💡 **Señal de violación de LSP:** Si en una subclase necesitan lanzar una excepción en un método que el padre define como operación normal, o si necesitan un `if isinstance(...)` para manejar un caso especial de un hijo, probablemente están violando Liskov. Reorganicen la jerarquía.

---

## Herencia en dataclasses

Las dataclasses soportan herencia y funciona de forma natural. La nota de dataclasses cubre esto en detalle, pero repasemos los puntos clave.

### Ejemplo básico

```python
from dataclasses import dataclass

@dataclass
class Persona:
    nombre: str
    edad: int

@dataclass
class Estudiante(Persona):
    programa: str
    semestre: int
```

```python
e = Estudiante("Laura", 20, "Ingeniería", 4)
print(e)  # → Estudiante(nombre='Laura', edad=20, programa='Ingeniería', semestre=4)
```

El `__init__` generado para `Estudiante` incluye **todos** los campos: primero los del padre (`nombre`, `edad`), luego los propios (`programa`, `semestre`).

### La regla de orden de campos

**Un campo con valor por defecto no puede ir antes de un campo sin valor por defecto.** Esto aplica también en herencia. Si el padre tiene un campo con default y el hijo agrega uno sin default, hay un `TypeError`:

```python
@dataclass
class Base:
    nombre: str
    activo: bool = True  # ← Tiene default

@dataclass
class Hija(Base):
    codigo: str  # ← Sin default → TypeError: va después de 'activo' que tiene default
```

**Solución:** Usar `kw_only` (Python 3.10+) o reorganizar los campos:

```python
from dataclasses import dataclass, field

@dataclass
class Hija(Base):
    codigo: str = field(kw_only=True)  # ← Ahora es keyword-only, se resuelve el conflicto
```

Para más detalles sobre herencia entre dataclasses (incluyendo `slots`, `frozen`, `post_init` con herencia), revisen la nota de dataclasses.

---

## Buenas prácticas y anti-patrones

### Buenas prácticas

* **Mantener jerarquías poco profundas.** Si su cadena de herencia tiene más de 3 niveles (A → B → C → D), probablemente están abusando de la herencia. Las jerarquías profundas son difíciles de entender, debuggear y mantener.

* **Preferir composición cuando haya duda.** Si no están seguros de si usar herencia o composición, vayan con composición. Es más flexible y menos propensa a crear problemas de diseño a largo plazo.

* **Usar ABC para definir contratos.** Si quieren que varias clases implementen el mismo conjunto de métodos, definan una clase abstracta. Eso fuerza a las subclases a cumplir el contrato.

* **Usar `@override` para documentar intención.** Cuando sobrescriben un método, el decorador `@override` comunica la intención y permite que las herramientas detecten errores.

* **No heredar solo por reutilizar código.** Si la única razón para heredar es evitar copiar unas líneas de código, probablemente composición o una función compartida sea mejor opción.

### Anti-patrones

* **La clase Dios (*God Class*):** Una superclase que hace todo y las subclases apenas tocan algo. Señal: la clase padre tiene 20+ métodos y las hijas solo sobrescriben 1 o 2.

* **Herencia por conveniencia:** Heredar de una clase solo porque tiene un método útil, aunque semánticamente la relación "es un(a)" no aplique (como el ejemplo del `Reporte` que heredaba de `ConexionBD`).

* **Jerarquías demasiado profundas:** A → B → C → D → E → F. Entender qué hace `F` requiere leer 6 clases. Refactoricen usando composición o aplanando la jerarquía.

* **La clase base frágil (*Fragile Base Class*):** Cuando un cambio en la superclase rompe a las subclases de formas inesperadas. Esto pasa cuando las subclases dependen de detalles internos del padre (no solo de su interfaz pública). Es otra razón para preferir composición: al depender de una interfaz en vez de heredar implementación, los cambios internos no le afectan.

---

## Errores comunes

### Olvidar `super().__init__()`

```python
class Animal:
    def __init__(self, nombre: str) -> None:
        self.nombre = nombre

class Gato(Animal):
    def __init__(self, nombre: str, color: str) -> None:
        # Olvidaron llamar a super().__init__(nombre)
        self.color = color

g = Gato("Michi", "negro")
print(g.color)   # → negro
print(g.nombre)  # → AttributeError: 'Gato' object has no attribute 'nombre'
```

`nombre` nunca se inicializó porque no llamaron a `super().__init__()`. **Siempre llamen a `super().__init__()`** cuando definan `__init__` en una subclase.

### MRO inesperado por orden de bases

```python
class A:
    def metodo(self) -> str:
        return "A"

class B(A):
    def metodo(self) -> str:
        return "B"

class C(A):
    def metodo(self) -> str:
        return "C"

# El orden de las bases determina quién "gana"
class D(B, C):
    pass

class E(C, B):
    pass

print(D().metodo())  # → "B" — porque B va primero
print(E().metodo())  # → "C" — porque C va primero
```

En herencia múltiple, el **orden en que listan las clases padre importa**. No es aleatorio ni "el que tenga el método más específico". Es puro orden de MRO.

### Confundir "es un" con "tiene un"

```python
# INCORRECTO: ¿Un Motor es un Auto? No.
class Auto:
    pass

class Motor(Auto):  # ← Absurdo semántico
    pass

# CORRECTO: Un Auto TIENE un Motor
class Motor:
    def __init__(self, cilindros: int) -> None:
        self.cilindros = cilindros

class Auto:
    def __init__(self, marca: str, motor: Motor) -> None:
        self.marca = marca
        self.motor = motor  # ← Composición
```

**Antes de escribir `class X(Y)`, pregúntense: "¿X es un Y?"** Si la respuesta no tiene sentido semántico, usen composición.

### Romper el contrato del padre (violar LSP)

```python
class Coleccion:
    def agregar(self, elemento: str) -> None:
        ...

class ColeccionInmutable(Coleccion):
    def agregar(self, elemento: str) -> None:
        raise RuntimeError("No se puede agregar a una colección inmutable.")
```

Si algo espera una `Coleccion` y llama a `agregar()`, va a explotar con `ColeccionInmutable`. Eso viola Liskov. **No creen subclases que rompan las promesas del padre.**

---

## Preguntas para pensar

1. Si `Cuadrado` hereda de `Rectangulo`, ¿tiene sentido? Piensen en qué pasa si `Rectangulo` tiene un método `cambiar_ancho()` que solo cambia el ancho sin cambiar el alto. ¿El cuadrado puede cumplir esa promesa?

2. En el ejemplo de notificaciones (Email, SMS, Push), ¿qué pasaría si en vez de herencia usaran un Protocol con un método `enviar()`? ¿Funcionaría igual? ¿Qué perderían y qué ganarían?

3. ¿Pueden pensar en un caso donde herencia múltiple sea la solución más limpia? ¿Y un caso donde sea una mala idea?

4. Si tienen una clase `Vehiculo` con subclases `Auto`, `Moto` y `Bicicleta`, y necesitan agregar la capacidad de "vehículo eléctrico" a algunos, ¿usarían herencia, mixin o composición? ¿Por qué?

5. ¿Por qué creen que el principio dice "favorece" la composición y no "usa siempre" composición? ¿En qué casos la herencia es claramente mejor?

6. Si una jerarquía tiene 5 niveles de profundidad y necesitan agregar un comportamiento que solo aplica al nivel 3 y al 5 pero no al 4, ¿qué harían?
