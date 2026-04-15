# Protocolos e Interfaces en Python

## ¿Qué es una interfaz?

Antes de hablar de Python, hablemos del concepto general.

Una **interfaz** es un **contrato** que dice *qué* se puede hacer, sin decir *cómo* se hace. Define las operaciones que un objeto debe ofrecer para que otros puedan usarlo, sin importar los detalles internos de su implementación.

La idea es tan vieja como la ingeniería misma: cuando ustedes enchufan un cargador en un tomacorriente, no necesitan saber cómo está cableada la casa. Solo necesitan que el enchufe cumpla con la **interfaz** del tomacorriente — la forma física, el voltaje, la frecuencia. Si el enchufe "tiene los pines correctos", funciona.

En programación orientada a objetos, una interfaz dice:

> "Cualquier objeto que quiera participar en esta parte del sistema debe tener **estos métodos** con **estas firmas**."

Nada más. No dice cómo implementarlos. No dice qué hacer adentro. Solo dice **qué debe existir**.

---

## Analogías del mundo real

### El enchufe eléctrico

En Colombia usamos enchufes tipo A y B. Un electrodoméstico no sabe si la energía viene de una hidroeléctrica, un panel solar o una planta diésel. Solo necesita que el tomacorriente **cumpla con la interfaz**: dos pines planos, 120V, 60Hz. Eso es un contrato. El electrodoméstico depende de la **interfaz** del tomacorriente, no de la fuente de energía.

### El puerto USB

Un puerto USB es una interfaz. Pueden conectar un mouse, una memoria, un teclado, un disco duro. Todos son dispositivos completamente diferentes, pero todos cumplen con la **misma interfaz**: el estándar USB. Su computador no necesita saber qué hay al otro lado del cable. Solo necesita que el dispositivo "hable USB".

### El contrato de un servicio

Cuando contratan un servicio de mensajería, el contrato dice: "usted me da un paquete, una dirección y un plazo, y yo se lo entrego". No dice si lo llevan en moto, en avión o a pie. El contrato define el **qué**, no el **cómo**. Si la empresa cumple el contrato, el servicio funciona.

---

## ¿Por qué necesitamos interfaces?

Sin interfaces, las partes de un sistema se conocen entre sí con demasiada intimidad. El código que envía notificaciones sabe que usa un servidor SMTP. El código que guarda datos sabe que usa PostgreSQL. El código que cobra sabe que usa Stripe.

Eso crea **acoplamiento fuerte**: si cambian el servidor de correo, deben modificar el código que envía notificaciones **y** todo el código que depende de él. ¿Recuerdan los conceptos de cohesión y acoplamiento de las notas de modularización? Este es exactamente ese problema.

Con interfaces, las partes del sistema se comunican a través de **contratos**. El código que envía notificaciones solo sabe que tiene un objeto con un método `enviar(destinatario, mensaje)`. No sabe (ni le importa) si es por correo, por SMS o por paloma mensajera.

**Beneficios concretos:**

| Beneficio | Sin interfaz | Con interfaz |
|-----------|-------------|--------------|
| **Cambiar implementación** | Hay que modificar múltiples archivos | Se crea una nueva clase que cumpla el contrato |
| **Probar el código** | Necesita la base de datos real, el servidor real | Se usa un objeto falso que cumple el contrato |
| **Entender el sistema** | Hay que leer toda la implementación | Se lee el contrato y se entiende qué se espera |
| **Trabajar en equipo** | "Esperá a que yo termine mi parte" | "Cumplí este contrato y trabajamos en paralelo" |

---

## Dos formas de definir contratos en Python

Python ofrece dos mecanismos para definir interfaces: las **Clases Base Abstractas** (ABC) y los **Protocolos** (Protocol). Ambos definen contratos, pero de formas fundamentalmente diferentes.

La diferencia clave está en el tipo de **tipado** que usan:

- **ABC** → **Tipado nominal**: la clase **debe declarar explícitamente** que hereda de la ABC. Es como un carné de membresía: si no lo tienes, no entras aunque sepas hacer todo.
- **Protocol** → **Tipado estructural**: la clase solo necesita **tener los métodos correctos**. Es como una audición: si sabes cantar, entras. No importa de qué escuela vienes.

### Primera vista rápida

```python
from abc import ABC, abstractmethod
from typing import Protocol


# ── ABC: contrato NOMINAL ──────────────────────────
class Exportador(ABC):
    @abstractmethod
    def exportar(self, datos: list[dict]) -> str:
        ...


class ExportadorCSV(Exportador):  # ← Declara herencia explícita
    def exportar(self, datos: list[dict]) -> str:
        encabezados = ",".join(datos[0].keys())
        filas = [",".join(str(v) for v in fila.values()) for fila in datos]
        return encabezados + "\n" + "\n".join(filas)


# ── Protocol: contrato ESTRUCTURAL ─────────────────
class Almacenamiento(Protocol):
    def guardar(self, clave: str, contenido: str) -> None: ...
    def cargar(self, clave: str) -> str: ...


class AlmacenamientoLocal:  # ← NO hereda de nada
    def guardar(self, clave: str, contenido: str) -> None:
        with open(clave, "w") as f:
            f.write(contenido)

    def cargar(self, clave: str) -> str:
        with open(clave) as f:
            return f.read()
```

`ExportadorCSV` **debe** heredar de `Exportador` (ABC) — si no lo hace, no es un `Exportador`.

`AlmacenamientoLocal` **no** hereda de `Almacenamiento` (Protocol) — y aun así lo es, porque tiene los métodos `guardar` y `cargar` con las firmas correctas.

---

## Protocol en profundidad

### ¿Qué es el tipado estructural?

El tipado estructural dice: **"si tiene la forma correcta, es del tipo correcto"**. No importa de dónde viene, no importa si declaró algo. Lo que importa es que **tiene los métodos que se necesitan**.

Python siempre ha sido así en tiempo de ejecución — eso es el **duck typing** que vimos en las notas de herencia y polimorfismo. Si un objeto tiene un método `__iter__`, puedes iterar sobre él. Si tiene `__len__`, puedes llamar `len()`. Python no pregunta "¿eres iterable?". Pregunta "¿tienes `__iter__`?".

`Protocol` lleva esa misma filosofía al **sistema de tipos** — permite que un type checker (como mypy o Pyright) verifique que un objeto cumple con un contrato **sin necesidad de herencia**.

### Sintaxis de Protocol

```python
from typing import Protocol


class Renderizable(Protocol):
    def renderizar(self) -> str:
        """Devuelve el contenido como texto formateado."""
        ...
```

Eso es todo. `Renderizable` es un contrato que dice: "cualquier objeto que tenga un método `renderizar()` que devuelva `str` es `Renderizable`".

Ahora cualquier clase que tenga ese método **ya cumple** el contrato:

```python
class Tabla:
    def __init__(self, filas: list[list[str]]) -> None:
        self.filas = filas

    def renderizar(self) -> str:
        return "\n".join(" | ".join(fila) for fila in self.filas)


class Parrafo:
    def __init__(self, texto: str) -> None:
        self.texto = texto

    def renderizar(self) -> str:
        return f"<p>{self.texto}</p>"
```

Ni `Tabla` ni `Parrafo` heredan de `Renderizable`. Ni siquiera importan `Renderizable`. Pero **ambas son `Renderizable`** porque tienen el método con la firma correcta.

```python
def generar_documento(elementos: list[Renderizable]) -> str:
    return "\n---\n".join(e.renderizar() for e in elementos)


doc = generar_documento([
    Tabla([["Nombre", "Edad"], ["Ana", "22"]]),
    Parrafo("Este es un párrafo de ejemplo."),
])
print(doc)
# → Nombre | Edad
# → Ana | 22
# → ---
# → <p>Este es un párrafo de ejemplo.</p>
```

El type checker verifica que cada elemento de la lista tiene `renderizar() -> str`. Si pasan un objeto que no lo tiene, marca error **antes de ejecutar**.

### Protocols con atributos

Un Protocol puede exigir no solo métodos, sino también **atributos**:

```python
class ConNombre(Protocol):
    nombre: str


class ConIdentificacion(Protocol):
    nombre: str
    identificacion: str


class Empleado:
    def __init__(self, nombre: str, identificacion: str, cargo: str) -> None:
        self.nombre = nombre
        self.identificacion = identificacion
        self.cargo = cargo


class Vehiculo:
    def __init__(self, nombre: str, placa: str) -> None:
        self.nombre = nombre
        self.placa = placa
```

`Empleado` cumple tanto `ConNombre` como `ConIdentificacion`. `Vehiculo` cumple `ConNombre` pero no `ConIdentificacion` (no tiene `identificacion`).

```python
def saludar(entidad: ConNombre) -> str:
    return f"Hola, {entidad.nombre}!"


print(saludar(Empleado("Carlos", "123", "Dev")))  # → Hola, Carlos!
print(saludar(Vehiculo("Mazda 3", "ABC-123")))     # → Hola, Mazda 3!
```

Ambos funcionan porque ambos tienen `nombre: str`.

### Protocols con métodos que tienen implementación por defecto

A diferencia de las ABCs, los Protocols **no deben** tener implementación en sus métodos (porque el propósito es definir la forma, no la lógica). Sin embargo, Python permite definir métodos con cuerpo en un Protocol. Estos métodos **no se heredan** (porque las clases no heredan del Protocol), son solo para documentación.

> 💡 **Regla práctica:** En un Protocol, dejen los métodos con `...` o `pass`. Si necesitan lógica compartida, usen una ABC.

### `@runtime_checkable` — verificación en tiempo de ejecución

Por defecto, los Protocols solo funcionan con el **type checker** (análisis estático). No se pueden usar con `isinstance()` en tiempo de ejecución:

```python
from typing import Protocol


class Serializable(Protocol):
    def a_json(self) -> str: ...


class Producto:
    def __init__(self, nombre: str, precio: int) -> None:
        self.nombre = nombre
        self.precio = precio

    def a_json(self) -> str:
        import json
        return json.dumps({"nombre": self.nombre, "precio": self.precio})


p = Producto("Café", 5000)

# ❌ Esto FALLA:
isinstance(p, Serializable)
# TypeError: Protocols with non-method members can't be used with isinstance()
```

Para habilitar la verificación en tiempo de ejecución, se usa `@runtime_checkable`:

```python
from typing import Protocol, runtime_checkable


@runtime_checkable
class Serializable(Protocol):
    def a_json(self) -> str: ...


class Producto:
    def __init__(self, nombre: str, precio: int) -> None:
        self.nombre = nombre
        self.precio = precio

    def a_json(self) -> str:
        import json
        return json.dumps({"nombre": self.nombre, "precio": self.precio})


class Categoria:
    def __init__(self, nombre: str) -> None:
        self.nombre = nombre


p = Producto("Café", 5000)
c = Categoria("Bebidas")

print(isinstance(p, Serializable))  # → True  (tiene a_json)
print(isinstance(c, Serializable))  # → False (no tiene a_json)
```

**¿Cuándo usar `@runtime_checkable`?**

- Cuando necesitan filtrar objetos dinámicamente: "de esta lista, ¿cuáles son serializables?".
- Cuando reciben datos de fuentes externas y necesitan validar que cumplen el contrato.
- En validaciones defensivas donde el type checker no puede ayudar.

**Limitaciones:**

- `@runtime_checkable` + `isinstance()` solo verifica que el método **existe**, no que tenga la **firma correcta**. Si un objeto tiene `a_json()` pero retorna `int` en vez de `str`, `isinstance` dirá `True`.
- Para verificación completa de firmas, se necesita el type checker estático.

---

## ABC como interfaz — repaso enfocado

Ya conocen las ABCs de las notas de herencia y polimorfismo. Aquí las vemos **específicamente como mecanismo para definir interfaces**, no como herramienta de herencia.

### ABC pura (solo métodos abstractos) = Interfaz

Cuando una ABC tiene **solo métodos abstractos** y **ninguna implementación**, funciona exactamente como una interfaz:

```python
from abc import ABC, abstractmethod


class Buscador(ABC):
    @abstractmethod
    def buscar(self, consulta: str) -> list[str]:
        ...

    @abstractmethod
    def cantidad_resultados(self, consulta: str) -> int:
        ...
```

Cualquier clase que herede de `Buscador` **debe** implementar ambos métodos. Si no lo hace, Python lanza `TypeError` al intentar instanciarla.

```python
class BuscadorWeb(Buscador):
    def buscar(self, consulta: str) -> list[str]:
        # Simulación
        return [f"Resultado web para '{consulta}'"]

    def cantidad_resultados(self, consulta: str) -> int:
        return 1


class BuscadorLocal(Buscador):
    def __init__(self, directorio: str) -> None:
        self.directorio = directorio

    def buscar(self, consulta: str) -> list[str]:
        return [f"Archivo en {self.directorio} que contiene '{consulta}'"]

    def cantidad_resultados(self, consulta: str) -> int:
        return 1
```

### ABC con métodos concretos = Interfaz + lógica reutilizable

Cuando una ABC tiene **métodos abstractos y métodos concretos**, deja de ser una interfaz pura. Se convierte en un **Template Method**: la ABC define el esqueleto de un algoritmo y deja que las subclases llenen los huecos.

```python
from abc import ABC, abstractmethod
from datetime import datetime


class GeneradorReporte(ABC):
    """ABC con Template Method: define el flujo, las subclases llenan los datos."""

    def generar(self) -> str:
        # Este método concreto define el FLUJO (no se sobrescribe)
        encabezado = self._crear_encabezado()
        cuerpo = self._crear_cuerpo()
        pie = f"\n— Generado el {datetime.now():%Y-%m-%d %H:%M}"
        return f"{encabezado}\n{cuerpo}{pie}"

    @abstractmethod
    def _crear_encabezado(self) -> str:
        """Las subclases definen cómo crear el encabezado."""
        ...

    @abstractmethod
    def _crear_cuerpo(self) -> str:
        """Las subclases definen cómo crear el cuerpo."""
        ...


class ReporteVentas(GeneradorReporte):
    def __init__(self, ventas: list[int]) -> None:
        self.ventas = ventas

    def _crear_encabezado(self) -> str:
        return "=== REPORTE DE VENTAS ==="

    def _crear_cuerpo(self) -> str:
        lineas = [f"  Venta: ${v:,}" for v in self.ventas]
        lineas.append(f"  TOTAL: ${sum(self.ventas):,}")
        return "\n".join(lineas)


class ReporteAsistencia(GeneradorReporte):
    def __init__(self, asistentes: list[str]) -> None:
        self.asistentes = asistentes

    def _crear_encabezado(self) -> str:
        return "=== REPORTE DE ASISTENCIA ==="

    def _crear_cuerpo(self) -> str:
        lineas = [f"  ✓ {a}" for a in self.asistentes]
        lineas.append(f"  Total: {len(self.asistentes)} asistentes")
        return "\n".join(lineas)
```

```python
r = ReporteVentas([150_000, 230_000, 85_000])
print(r.generar())
# → === REPORTE DE VENTAS ===
# →   Venta: $150,000
# →   Venta: $230,000
# →   Venta: $85,000
# →   TOTAL: $465,000
# → — Generado el 2026-04-14 10:30
```

El método `generar()` es **concreto** y define el flujo. Los métodos `_crear_encabezado()` y `_crear_cuerpo()` son **abstractos** y cada subclase los llena. Eso es Template Method.

> 💡 **¿Cuándo usar Template Method?** Cuando varias clases comparten el **mismo flujo** pero difieren en los **pasos específicos**. Si no hay flujo compartido, una ABC pura (solo abstractos) es suficiente.

---

## Tabla de decisión: ABC vs Protocol

Esta tabla amplía la que vimos brevemente en las notas de herencia y polimorfismo:

| Criterio | ABC | Protocol |
|----------|-----|----------|
| **Tipo de tipado** | Nominal (debe heredar) | Estructural (solo necesita los métodos) |
| **Herencia requerida** | Sí, explícita | No |
| **Verificación instantánea** | Sí (`TypeError` al instanciar si falta un método) | Solo con type checker (o `@runtime_checkable`) |
| **Puede tener lógica compartida** | Sí (métodos concretos, Template Method) | No (los métodos con cuerpo no se heredan) |
| **Funciona con clases de terceros** | No (la clase debe heredar de su ABC) | Sí (si tiene los métodos, cumple) |
| **Detecta errores de firma** | En ejecución (`TypeError`) | Con type checker (antes de ejecutar) |
| **Cuándo usar** | Familia controlada con lógica compartida | Contratos ligeros, clases de terceros, desacoplamiento máximo |

### La regla de decisión

```
¿Necesita lógica compartida entre las implementaciones?
  └─ SÍ → ABC (Template Method)
  └─ NO → ¿Necesita funcionar con clases que NO controla (de terceros)?
              └─ SÍ → Protocol
              └─ NO → ¿Es un contrato ligero (1-3 métodos)?
                          └─ SÍ → Protocol
                          └─ NO → Cualquiera funciona, elija según preferencia
```

### Escenarios del curso

| Escenario | Mejor opción | Por qué |
|-----------|-------------|---------|
| Repositorio de datos (`guardar`, `obtener`, `eliminar`) | **ABC** o **Protocol** | Sin lógica compartida → ambos funcionan. Si quiere proteger con `TypeError`, ABC. Si quiere flexibilidad, Protocol. |
| Servicio de notificaciones (`enviar(destinatario, mensaje)`) | **Protocol** | Contrato ligero, 1 método. Podría haber implementaciones de terceros (Twilio, SendGrid). |
| Generador de reportes con flujo fijo (encabezado → cuerpo → pie) | **ABC** | Lógica compartida en el flujo. Template Method. |
| Regla de precios (`calcular(precio) -> precio`) | **Protocol** | Contrato de 1 método. Máxima flexibilidad para combinar reglas. |
| Validador de formularios con pasos comunes | **ABC** | Los validadores comparten el flujo: sanitizar → validar campos → resultado. |

---

## "Programar contra la interfaz, no contra la implementación"

Este es uno de los principios más importantes del diseño orientado a objetos. Viene del libro *Design Patterns* (Gamma et al., 1994) y dice:

> Las partes de un sistema no deben depender de clases concretas, sino de abstracciones (interfaces o contratos).

### ¿Qué significa en la práctica?

Significa que cuando una función o una clase necesita usar un servicio, **no debe pedir la clase concreta** sino el **contrato** (ABC o Protocol):

```python
from typing import Protocol


class Traductor(Protocol):
    def traducir(self, texto: str, idioma_destino: str) -> str: ...


# ── MAL: depende de la implementación concreta ─────
class ServicioContenido:
    def __init__(self) -> None:
        self.traductor = TraductorGoogle()  # ← Concreta, acoplada

    def publicar(self, texto: str, idioma: str) -> str:
        return self.traductor.traducir(texto, idioma)


# ── BIEN: depende de la interfaz ───────────────────
class ServicioContenido:
    def __init__(self, traductor: Traductor) -> None:  # ← Interfaz
        self.traductor = traductor

    def publicar(self, texto: str, idioma: str) -> str:
        return self.traductor.traducir(texto, idioma)
```

En la versión "BIEN":
- `ServicioContenido` no sabe qué traductor usa.
- Puede recibir `TraductorGoogle`, `TraductorDeepL`, `TraductorLocal`, o un **fake para testing**.
- Si mañana cambian de traductor, `ServicioContenido` **no se modifica**.

### El beneficio más inmediato: testing

Si `ServicioContenido` depende de `TraductorGoogle`, para probarlo necesitan conexión a internet, una API key, y que Google esté disponible. Eso hace las pruebas lentas, frágiles y costosas.

Si depende de `Traductor` (interfaz), pueden crear un traductor falso para las pruebas:

```python
class TraductorFalso:
    """Traductor para pruebas: devuelve el texto sin cambios."""
    def traducir(self, texto: str, idioma_destino: str) -> str:
        return f"[{idioma_destino}] {texto}"


# En las pruebas:
servicio = ServicioContenido(TraductorFalso())
resultado = servicio.publicar("Hola mundo", "en")
assert resultado == "[en] Hola mundo"  # ✓ Rápido, sin internet, determinista
```

> 💡 Este patrón de pasar las dependencias desde afuera en vez de crearlas adentro se llama **Inyección de Dependencias (DI)**, y lo veremos en profundidad en las notas de DI y DIP.

---

## Conexión con duck typing y polimorfismo

En las notas de herencia y polimorfismo vimos:

- **Duck typing**: "si camina como pato y hace cuac como pato, es un pato".
- **Polimorfismo**: un mismo mensaje (método) tiene comportamiento diferente según el objeto que lo recibe.

**Protocol formaliza el duck typing.** Antes de Protocol, el duck typing en Python era completamente implícito: funcionaba en ejecución, pero el type checker no podía verificarlo. Protocol le da **nombre** y **verificabilidad** al duck typing.

| Concepto | Sin Protocol | Con Protocol |
|----------|-------------|-------------|
| **Duck typing** | Funciona en ejecución, sin verificación estática | Funciona en ejecución **y** el type checker lo valida |
| **Documentación** | "Este parámetro necesita un objeto con método X" (en comentario) | `def f(x: MiProtocol)` (en el tipo) |
| **Errores** | Se descubren al ejecutar (`AttributeError`) | Se descubren al escribir (el editor marca rojo) |

Y el **polimorfismo** se beneficia directamente: cuando definen un Protocol, están diciendo "cualquier objeto que cumpla esta forma puede participar". Eso es polimorfismo en su expresión más pura — no necesita herencia, no necesita ABC, solo necesita **la forma correcta**.

---

## Ejemplo completo: sistema de validación

Vamos a construir un ejemplo que muestre Protocol en acción con un dominio diferente a los de los ejemplos prácticos del curso.

### El problema

Un formulario de registro necesita validar datos. Diferentes campos tienen diferentes reglas: el email debe tener `@`, la contraseña debe tener mínimo 8 caracteres, la edad debe ser positiva. Queremos que el sistema sea **extensible** — que se puedan agregar nuevas reglas sin modificar el código existente.

### El contrato

```python
from dataclasses import dataclass
from typing import Protocol


@dataclass
class ResultadoValidacion:
    """Resultado de validar un campo."""
    es_valido: bool
    mensaje: str


class ReglaValidacion(Protocol):
    """Contrato: cualquier objeto con este método es una regla de validación."""
    def validar(self, valor: str) -> ResultadoValidacion: ...
```

### Las implementaciones

```python
class NoVacio:
    """Valida que el campo no esté vacío."""
    def __init__(self, nombre_campo: str) -> None:
        self.nombre_campo = nombre_campo

    def validar(self, valor: str) -> ResultadoValidacion:
        if valor.strip():
            return ResultadoValidacion(True, "OK")
        return ResultadoValidacion(False, f"{self.nombre_campo} no puede estar vacío.")


class LongitudMinima:
    """Valida que el campo tenga al menos N caracteres."""
    def __init__(self, nombre_campo: str, minimo: int) -> None:
        self.nombre_campo = nombre_campo
        self.minimo = minimo

    def validar(self, valor: str) -> ResultadoValidacion:
        if len(valor) >= self.minimo:
            return ResultadoValidacion(True, "OK")
        return ResultadoValidacion(
            False,
            f"{self.nombre_campo} debe tener al menos {self.minimo} caracteres."
        )


class ContieneCaracter:
    """Valida que el campo contenga un carácter específico."""
    def __init__(self, nombre_campo: str, caracter: str) -> None:
        self.nombre_campo = nombre_campo
        self.caracter = caracter

    def validar(self, valor: str) -> ResultadoValidacion:
        if self.caracter in valor:
            return ResultadoValidacion(True, "OK")
        return ResultadoValidacion(
            False,
            f"{self.nombre_campo} debe contener '{self.caracter}'."
        )


class RangoNumerico:
    """Valida que el valor numérico esté en un rango."""
    def __init__(self, nombre_campo: str, minimo: int, maximo: int) -> None:
        self.nombre_campo = nombre_campo
        self.minimo = minimo
        self.maximo = maximo

    def validar(self, valor: str) -> ResultadoValidacion:
        try:
            numero = int(valor)
        except ValueError:
            return ResultadoValidacion(False, f"{self.nombre_campo} debe ser un número.")

        if self.minimo <= numero <= self.maximo:
            return ResultadoValidacion(True, "OK")
        return ResultadoValidacion(
            False,
            f"{self.nombre_campo} debe estar entre {self.minimo} y {self.maximo}."
        )
```

Ninguna de estas clases hereda de `ReglaValidacion`. Ninguna la importa. Solo tienen el método `validar(valor: str) -> ResultadoValidacion`, y eso las convierte en `ReglaValidacion`.

### El validador (consume el contrato)

```python
@dataclass
class CampoFormulario:
    """Un campo con sus reglas de validación."""
    nombre: str
    reglas: list[ReglaValidacion]


class ValidadorFormulario:
    """Valida los datos de un formulario contra sus reglas."""

    def __init__(self, campos: list[CampoFormulario]) -> None:
        self.campos = campos

    def validar(self, datos: dict[str, str]) -> dict[str, list[str]]:
        """Retorna un diccionario con los errores por campo. Vacío = todo válido."""
        errores: dict[str, list[str]] = {}

        for campo in self.campos:
            valor = datos.get(campo.nombre, "")
            for regla in campo.reglas:
                resultado = regla.validar(valor)
                if not resultado.es_valido:
                    errores.setdefault(campo.nombre, []).append(resultado.mensaje)

        return errores
```

`ValidadorFormulario` no sabe nada de `NoVacio`, `LongitudMinima`, etc. Solo sabe que cada regla tiene `validar(valor) -> ResultadoValidacion`. Eso es programar contra la interfaz.

### Uso completo

```python
# ── Configuración del formulario ───────────────────
formulario = ValidadorFormulario([
    CampoFormulario("email", [
        NoVacio("Email"),
        ContieneCaracter("Email", "@"),
    ]),
    CampoFormulario("contraseña", [
        NoVacio("Contraseña"),
        LongitudMinima("Contraseña", 8),
    ]),
    CampoFormulario("edad", [
        NoVacio("Edad"),
        RangoNumerico("Edad", 18, 120),
    ]),
])

# ── Datos del usuario ─────────────────────────────
datos_buenos = {"email": "ana@correo.com", "contraseña": "segura123", "edad": "22"}
datos_malos = {"email": "anasinArroba", "contraseña": "123", "edad": "15"}

# ── Validación ────────────────────────────────────
errores = formulario.validar(datos_buenos)
print(errores)
# → {}   (sin errores)

errores = formulario.validar(datos_malos)
print(errores)
# → {
# →     'email': ["Email debe contener '@'."],
# →     'contraseña': ['Contraseña debe tener al menos 8 caracteres.'],
# →     'edad': ['Edad debe estar entre 18 y 120.']
# → }
```

### ¿Quieren agregar una nueva regla?

Creen una clase con el método `validar` y agréguela a la configuración. **No hay que modificar nada existente**:

```python
class SinEspacios:
    def __init__(self, nombre_campo: str) -> None:
        self.nombre_campo = nombre_campo

    def validar(self, valor: str) -> ResultadoValidacion:
        if " " not in valor:
            return ResultadoValidacion(True, "OK")
        return ResultadoValidacion(False, f"{self.nombre_campo} no debe contener espacios.")


# Agregar a un campo existente:
formulario.campos[1].reglas.append(SinEspacios("Contraseña"))
```

Cero modificaciones al `ValidadorFormulario`. Cero modificaciones a las reglas existentes. Eso es el **Principio Abierto/Cerrado**: abierto para extensión, cerrado para modificación.

---

## Errores comunes

### 1. Heredar del Protocol

```python
# ❌ Error conceptual: no se hereda de un Protocol
class MiClase(MiProtocol):
    def metodo(self) -> str:
        return "hola"
```

Técnicamente Python lo permite, pero **pierde todo el sentido**. El punto de Protocol es que no necesita herencia. Si necesitan herencia, usen ABC.

### 2. Confundir Protocol con ABC

```python
# ❌ Esperan que falle al instanciar sin implementar
class MiProtocol(Protocol):
    def metodo(self) -> str: ...

class Incompleta:
    pass

obj = Incompleta()  # ← NO falla (Protocol no fuerza implementación en ejecución)
```

Si necesitan que Python lance error al instanciar una clase incompleta, usen ABC. Protocol solo verifica con el type checker.

### 3. Usar `@runtime_checkable` para todo

```python
# ❌ Innecesario si solo usan type checking
@runtime_checkable
class Repositorio(Protocol):
    def guardar(self, dato: str) -> None: ...
    def obtener(self, id: int) -> str: ...

# Si nunca hacen isinstance(x, Repositorio), @runtime_checkable sobra
```

Solo usen `@runtime_checkable` cuando **necesiten** `isinstance()` en tiempo de ejecución.

### 4. Protocol con lógica compartida

```python
# ❌ Los métodos con cuerpo en Protocol NO se heredan
class Auditable(Protocol):
    def registrar(self, accion: str) -> None:
        print(f"[LOG] {accion}")  # ← Esto NO se comparte con las implementaciones

class Servicio:
    def registrar(self, accion: str) -> None:
        # Tiene que reimplementar todo, el print del Protocol no llega
        print(f"[LOG] {accion}")
```

Si necesitan lógica compartida, usen ABC o un Mixin.

---

## Preguntas para pensar

1. **¿Por qué Python permite heredar de un Protocol si no tiene sentido hacerlo?** ¿Qué implicaciones tiene eso para los que vienen de lenguajes como Java?

2. **Si Protocol es structural typing y Python ya tiene duck typing, ¿para qué sirve Protocol?** ¿Qué agrega que no tuviera Python sin él?

3. **¿Puede una clase cumplir varios Protocols al mismo tiempo?** ¿Cómo se compara eso con implementar varias interfaces en Java o C#?

4. **¿Qué pasa si un Protocol define `guardar(self, dato: str) -> None` y mi clase tiene `guardar(self, dato: int) -> None`?** ¿Cumple o no cumple?

5. **En el ejemplo de validación, ¿qué pasaría si mañana necesitamos reglas que validen combinaciones de campos (ej: "si edad < 18, tutor es obligatorio")?** ¿Cómo habría que cambiar el diseño?

---

## Resumen

| Concepto | Idea central |
|----------|-------------|
| **Interfaz** | Contrato que define *qué* se puede hacer, no *cómo* |
| **Protocol** | Interfaz basada en tipado estructural — si tiene los métodos, cumple |
| **ABC** | Interfaz basada en tipado nominal — debe heredar explícitamente |
| **`@runtime_checkable`** | Permite usar `isinstance()` con Protocols en tiempo de ejecución |
| **Template Method** | ABC con métodos concretos + abstractos para definir flujos reutilizables |
| **Programar contra la interfaz** | Depender de contratos, no de implementaciones concretas |
| **Tipado estructural** | "Si tiene la forma correcta, es del tipo correcto" |

---
