# Manejo de Excepciones en Python

## ¿Qué es una excepción?

Cuando un programa se ejecuta, las cosas pueden salir mal. Intentan dividir entre cero, buscan un archivo que no existe, piden un producto que no está en el inventario, el usuario escribe letras donde esperaban un número. Todas esas son situaciones **excepcionales**: no son el flujo normal del programa, pero **pueden ocurrir** y hay que estar preparados para manejarlas.

Una **excepción** es la forma que tiene Python de decir: *"algo salió mal y no puedo seguir ejecutando esta parte del código"*. Cuando ocurre una situación anómala, Python **lanza** (o *levanta*) una excepción. Si nadie la **captura**, el programa se detiene y muestra un error con un traceback.

Hasta ahora en el curso hemos trabajado con excepciones desde un solo lado: las hemos **lanzado** (`raise`) para señalar errores de validación en nuestros modelos y servicios. Si revisan los ejemplos (la máquina expendedora, la boletería de cine), verán que definimos jerarquías de excepciones personalizadas y las lanzamos cuando algo no cumple las reglas del dominio. Pero nunca las hemos **capturado**. Es hora de cerrar ese ciclo.

---

## Analogías del mundo real

### El extintor y el plan de emergencia

En un edificio tienen un plan de emergencia: si hay un incendio, suenan las alarmas, la gente evacúa por las rutas señalizadas, los bomberos actúan con los extintores. El plan de emergencia no **evita** el incendio, pero **define qué hacer cuando ocurre** para minimizar el daño.

Las excepciones funcionan igual. El código "normal" es el edificio funcionando bien. Cuando algo sale mal (el "incendio"), se lanza una excepción (suena la "alarma"). El bloque `try/except` es el "plan de emergencia": define qué hacer cuando la excepción ocurre.

### La caja de fusibles

En la instalación eléctrica de una casa, los fusibles protegen el circuito. Si hay una sobrecarga, el fusible **se activa y corta la corriente** en esa sección de la casa, evitando que se dañe toda la instalación. El resto de la casa sigue funcionando.

Las excepciones hacen lo mismo con el código: cuando hay un error en una parte, la excepción "corta" la ejecución de esa sección, pero si la capturan correctamente, el resto del programa puede seguir funcionando.

### El GPS y las rutas alternativas

Cuando van en el carro con GPS y hay un accidente en la vía, el GPS no se apaga y dice "no puedo continuar". Calcula una **ruta alternativa** y los lleva al destino por otro camino.

Eso es exactamente lo que hace un buen manejo de excepciones: cuando el camino normal falla, proporciona una ruta alternativa para que el programa pueda continuar o al menos terminar de forma elegante.

---

## La jerarquía de excepciones de Python

Todas las excepciones en Python son clases que heredan de `BaseException`. La estructura se ve así (simplificada):

```
BaseException
├── SystemExit
├── KeyboardInterrupt
├── GeneratorExit
└── Exception                          ← De aquí heredan TODAS las excepciones "normales"
    ├── ValueError
    ├── TypeError
    ├── KeyError
    ├── IndexError
    ├── AttributeError
    ├── FileNotFoundError
    ├── ZeroDivisionError
    ├── ImportError
    │   └── ModuleNotFoundError
    ├── OSError
    │   └── FileNotFoundError
    ├── RuntimeError
    │   └── RecursionError
    ├── StopIteration
    └── ... (muchas más)
```

Hay dos cosas fundamentales que deben saber de esta jerarquía:

1. **Siempre hereden de `Exception`, nunca de `BaseException`.** Las excepciones que hereden directamente de `BaseException` (como `SystemExit` y `KeyboardInterrupt`) son para el sistema, no para el código de usuario. Si sus excepciones personalizadas heredan de `BaseException`, podrían interferir con el cierre del programa.

2. **`except Exception` captura todo lo "normal".** Si usan `except Exception`, capturan cualquier excepción que herede de `Exception`. Eso incluye `ValueError`, `TypeError`, `KeyError` y todas las demás excepciones estándar. **No** incluye `SystemExit` ni `KeyboardInterrupt`, que es exactamente lo que quieren: que el usuario siempre pueda cerrar el programa con Ctrl+C.

---

## Sintaxis: `try`, `except`, `else`, `finally`

Este es el bloque completo de manejo de excepciones en Python. Tiene cuatro partes, de las cuales solo `try` y al menos un `except` son obligatorios:

```python
try:
    # Código que podría lanzar una excepción
    resultado = operacion_riesgosa()
except TipoDeError as e:
    # Qué hacer si ocurre ese tipo de error
    print(f"Ocurrió un error: {e}")
else:
    # Se ejecuta SOLO si NO hubo excepción en el try
    print(f"Todo salió bien: {resultado}")
finally:
    # Se ejecuta SIEMPRE, haya o no haya excepción
    print("Limpieza final")
```

Vamos una por una.

### `try`: "intenta hacer esto"

El bloque `try` envuelve el código que **podría fallar**. Python ejecuta las instrucciones dentro del `try` de forma normal. Si todo va bien, salta al `else` (si existe) y luego al `finally`. Si algo lanza una excepción, Python deja de ejecutar el `try` inmediatamente y busca un `except` que coincida.

```python
try:
    numero = int(input("Ingrese un número: "))
    resultado = 100 / numero
```

Si el usuario escribe "abc", `int()` lanza `ValueError`. Si escribe "0", la división lanza `ZeroDivisionError`. En cualquiera de los dos casos, Python **salta** al `except` correspondiente.

### `except`: "si falla, haz esto"

El bloque `except` captura una excepción específica (o varias). Pueden tener múltiples bloques `except` para manejar diferentes tipos de error de forma distinta:

```python
try:
    numero = int(input("Ingrese un número: "))
    resultado = 100 / numero
except ValueError:
    print("Eso no es un número válido.")
except ZeroDivisionError:
    print("No se puede dividir entre cero.")
```

Python busca el `except` **de arriba a abajo** y ejecuta el **primero que coincida**. Por eso, si tienen excepciones con herencia, pongan las más específicas primero:

```python
# CORRECTO: específico primero, general después
try:
    operacion()
except ProductoNoEncontrado:
    print("Producto no existe.")
except TiendaError:
    print("Error general de la tienda.")
except Exception:
    print("Error inesperado.")
```

```python
# INCORRECTO: Exception primero captura TODO, las demás nunca se ejecutan
try:
    operacion()
except Exception:           # ← Esto captura todo, incluyendo ProductoNoEncontrado
    print("Error.")
except ProductoNoEncontrado:  # ← NUNCA se alcanza
    print("Producto no existe.")
```

### Capturar la excepción con `as`

Para acceder al **mensaje** o los **datos** de la excepción, usen `as`:

```python
try:
    producto = tienda.buscar("XYZ")
except ProductoNoEncontrado as e:
    print(f"Error: {e}")  # → Error: No se encontró producto con código 'XYZ'.
```

La variable `e` contiene la instancia de la excepción. Pueden acceder a su mensaje con `str(e)` o a sus atributos si definieron atributos personalizados.

### Capturar múltiples excepciones en un solo `except`

Si quieren manejar varios tipos de excepción de la misma forma, pueden agruparlos en una tupla:

```python
try:
    resultado = operacion()
except (ValueError, TypeError, KeyError) as e:
    print(f"Error de datos: {e}")
```

### `else`: "si no hubo error, haz esto"

El bloque `else` se ejecuta **solo si el `try` terminó sin lanzar ninguna excepción**. Es el lugar ideal para el código que depende del resultado del `try` pero que no debería estar protegido por el `except`:

```python
try:
    numero = int(input("Ingrese un número: "))
except ValueError:
    print("Entrada inválida.")
else:
    # Solo se ejecuta si int() no lanzó ValueError
    print(f"El doble de {numero} es {numero * 2}")
```

**¿Por qué no poner ese código dentro del `try`?** Porque si lo ponen dentro del `try`, cualquier excepción que ocurra en esa línea también sería capturada por el `except`, incluso si no tiene nada que ver con la conversión del input. El `else` delimita: "esto solo se ejecuta si la parte riesgosa salió bien".

### `finally`: "pase lo que pase, haz esto"

El bloque `finally` se ejecuta **siempre**, sin importar si hubo excepción o no, si se capturó o no. Es para código de **limpieza** que debe ejecutarse sin falta:

```python
archivo = None
try:
    archivo = open("datos.txt", "r")
    contenido = archivo.read()
except FileNotFoundError:
    print("El archivo no existe.")
finally:
    if archivo is not None:
        archivo.close()  # Siempre cerrar el archivo, haya o no error
        print("Archivo cerrado.")
```

El `finally` es especialmente útil para liberar recursos: cerrar archivos, cerrar conexiones de red, liberar memoria. Incluso si el `except` lanza otra excepción o si hay un `return` dentro del `try`, el `finally` **siempre se ejecuta**.

---

## Lanzar excepciones: `raise`

Ya conocen `raise` de los ejemplos del curso. Es la forma de **señalar** que algo salió mal:

```python
def retirar(self, monto: int) -> None:
    if monto <= 0:
        raise ValueError("El monto debe ser positivo.")
    if monto > self._saldo:
        raise SaldoInsuficiente(
            f"Saldo insuficiente: disponible={self._saldo}, solicitado={monto}."
        )
    self._saldo -= monto
```

### Re-lanzar una excepción

A veces capturan una excepción para hacer algo (por ejemplo, registrar el error) y luego quieren que siga propagándose. Para eso usan `raise` sin argumentos:

```python
try:
    resultado = operacion_critica()
except OperacionError as e:
    print(f"[LOG] Error en operación: {e}")
    raise  # Re-lanza la misma excepción, con todo su traceback original
```

Esto es útil porque no pierden información: el traceback sigue apuntando al lugar donde se originó el error, no al lugar donde lo re-lanzaron.

### Encadenamiento de excepciones: `raise ... from ...`

A veces capturan una excepción de bajo nivel (por ejemplo, un error al leer un archivo) y quieren lanzar una excepción de dominio más significativa, pero **preservando la causa original**:

```python
try:
    with open("configuracion.json") as f:
        datos = json.load(f)
except FileNotFoundError as e:
    raise ConfiguracionError("No se encontró el archivo de configuración.") from e
except json.JSONDecodeError as e:
    raise ConfiguracionError("El archivo de configuración tiene formato inválido.") from e
```

El `from e` conecta las dos excepciones. Si esta excepción se propaga hasta el usuario, el traceback mostrará **ambas**: la excepción original y la nueva, conectadas con un mensaje *"The above exception was the direct cause of the following exception"*.

Esto es mucho mejor que simplemente lanzar la nueva excepción y perder la información del error original. Con `from e`, quien depure el error sabe exactamente qué pasó por debajo.

---

## Excepciones personalizadas

En los ejercicios del curso ya hemos estado creando excepciones personalizadas. Repasemos el patrón y ampliemos lo que podemos hacer con ellas.

### El patrón básico: jerarquía de dominio

```python
# excepciones.py

class ErrorDeDominio(Exception):
    """Excepción base para todos los errores del dominio."""
    pass

class ProductoNoEncontrado(ErrorDeDominio):
    """Se lanza cuando se busca un producto que no existe."""
    pass

class StockInsuficiente(ErrorDeDominio):
    """Se lanza cuando no hay suficiente inventario."""
    pass

class MontoInvalido(ErrorDeDominio):
    """Se lanza cuando un monto no cumple las validaciones."""
    pass
```

**¿Por qué usar una excepción base de dominio?** Porque permite capturar **todas** las excepciones de su dominio con un solo `except`:

```python
try:
    resultado = tienda.vender("A01", 5)
except ErrorDeDominio as e:
    # Captura ProductoNoEncontrado, StockInsuficiente, MontoInvalido, etc.
    print(f"Error de negocio: {e}")
```

O ser más específico cuando lo necesiten:

```python
try:
    resultado = tienda.vender("A01", 5)
except ProductoNoEncontrado:
    print("Producto no encontrado. Verifique el código.")
except StockInsuficiente:
    print("No hay suficiente inventario.")
```

### Excepciones con atributos personalizados

Las excepciones son clases normales. Pueden agregarles atributos para transportar información adicional sobre el error:

```python
class StockInsuficiente(ErrorDeDominio):
    def __init__(self, producto: str, disponible: int, solicitado: int) -> None:
        self.producto = producto
        self.disponible = disponible
        self.solicitado = solicitado
        super().__init__(
            f"Stock insuficiente para '{producto}': "
            f"disponible={disponible}, solicitado={solicitado}."
        )
```

Ahora quien capture esta excepción puede acceder a los datos:

```python
try:
    tienda.vender("A01", 50)
except StockInsuficiente as e:
    print(f"Producto: {e.producto}")
    print(f"Disponible: {e.disponible}")
    print(f"Solicitado: {e.solicitado}")
    print(f"Faltan: {e.solicitado - e.disponible} unidades")
```

Esto es mucho más poderoso que tener solo un mensaje de texto. El código que captura la excepción puede tomar **decisiones** basadas en los datos del error, no solo mostrar el mensaje.

---

## Context managers: el patrón `with`

Antes de seguir, hay un patrón muy relacionado con el manejo de excepciones que deben conocer: el **context manager** con la sentencia `with`.

### El problema que resuelve

Cuando trabajan con recursos (archivos, conexiones, etc.), necesitan asegurarse de **cerrarlos siempre**, incluso si hay un error:

```python
# Sin context manager — propenso a errores
archivo = open("datos.txt", "r")
try:
    contenido = archivo.read()
    # ... procesar contenido ...
finally:
    archivo.close()  # Hay que recordar siempre cerrar
```

### La solución: `with`

```python
# Con context manager — limpio y seguro
with open("datos.txt", "r") as archivo:
    contenido = archivo.read()
    # ... procesar contenido ...
# El archivo se cierra automáticamente al salir del bloque with,
# sin importar si hubo excepción o no
```

El `with` garantiza que el recurso se **libera siempre**, pase lo que pase. Es como un `try/finally` automático pero más limpio y menos propenso a errores.

### ¿Cuándo usar `with`?

Siempre que trabajen con algo que necesita abrirse y cerrarse:

```python
# Archivos
with open("reporte.txt", "w") as f:
    f.write("Contenido del reporte")

# Múltiples archivos a la vez (Python 3.10+)
with (
    open("entrada.txt", "r") as entrada,
    open("salida.txt", "w") as salida,
):
    salida.write(entrada.read().upper())
```

Para los proyectos del curso esto es menos frecuente (trabajamos con datos en memoria), pero es un patrón fundamental que van a usar constantemente en cualquier aplicación real que interactúe con archivos, bases de datos o APIs externas.

---

## Dos filosofías: LBYL vs. EAFP

Python tiene dos formas de abordar las situaciones donde algo podría fallar:

### LBYL: *Look Before You Leap* ("Mirar antes de saltar")

Verificar **antes** si la operación es posible:

```python
# LBYL: Verificar primero, actuar después
def obtener_producto(catalogo: dict[str, Producto], codigo: str) -> Producto | None:
    if codigo in catalogo:        # ← Verifico primero
        return catalogo[codigo]   # ← Luego actúo
    return None
```

### EAFP: *Easier to Ask Forgiveness than Permission* ("Es más fácil pedir perdón que permiso")

Intentar la operación y manejar el error si falla:

```python
# EAFP: Actuar y manejar el error si ocurre
def obtener_producto(catalogo: dict[str, Producto], codigo: str) -> Producto:
    try:
        return catalogo[codigo]          # ← Intento directamente
    except KeyError:
        raise ProductoNoEncontrado(f"No existe producto '{codigo}'.")
```

### ¿Cuál es mejor?

En Python, **EAFP es el estilo preferido** por la comunidad. La razón es doble:

1. **Evita condiciones de carrera:** En LBYL, entre la verificación y la acción algo podría cambiar (otro hilo podría modificar el estado). Con EAFP, la operación es atómica.
2. **Es más Pythónico:** Python está optimizado para que las excepciones sean baratas. No es como en Java donde lanzar una excepción tiene un costo significativo.

Dicho esto, no es una regla absoluta. Hay casos donde LBYL es más claro, especialmente cuando la verificación es simple y no hay riesgo de condiciones de carrera. Usen el sentido común.

En los ejemplos del curso, usamos una mezcla de ambos enfoques. En las validaciones de `__post_init__` hacemos LBYL (verificamos y lanzamos). En los servicios, a veces hacemos EAFP (intentamos y capturamos).

---

## El ciclo completo: lanzar Y capturar

Hasta este punto del curso, hemos visto cómo lanzar excepciones en modelos y servicios, pero el código que **usa** esos servicios nunca las capturaba. Vamos a cerrar el ciclo con un ejemplo integrador.

Usaremos la tienda del ejemplo de modularización:

```python
# excepciones.py
class TiendaError(Exception):
    pass

class ProductoNoEncontrado(TiendaError):
    pass

class StockInsuficiente(TiendaError):
    pass
```

```python
# servicio.py (versión simplificada)
from __future__ import annotations
from .excepciones import ProductoNoEncontrado, StockInsuficiente

class Tienda:
    def __init__(self) -> None:
        self._catalogo: dict[str, dict] = {}

    def agregar(self, codigo: str, nombre: str, precio: int, stock: int) -> None:
        self._catalogo[codigo] = {
            "nombre": nombre, "precio": precio, "stock": stock
        }

    def vender(self, codigo: str, cantidad: int) -> int:
        if codigo not in self._catalogo:
            raise ProductoNoEncontrado(f"Producto '{codigo}' no existe.")
        producto = self._catalogo[codigo]
        if cantidad > producto["stock"]:
            raise StockInsuficiente(
                f"Stock insuficiente: disponible={producto['stock']}, "
                f"solicitado={cantidad}."
            )
        producto["stock"] -= cantidad
        return producto["precio"] * cantidad
```

Ahora, el **`main.py`** que captura las excepciones:

```python
# main.py
from .excepciones import ProductoNoEncontrado, StockInsuficiente, TiendaError
from .servicio import Tienda


def main() -> None:
    tienda = Tienda()
    tienda.agregar("A01", "Café", 3500, 5)
    tienda.agregar("A02", "Galletas", 2000, 10)

    # Caso 1: Venta exitosa
    try:
        total = tienda.vender("A01", 2)
    except TiendaError as e:
        print(f"Error: {e}")
    else:
        print(f"Venta exitosa. Total: ${total:,}")

    # Caso 2: Producto que no existe
    try:
        total = tienda.vender("Z99", 1)
    except ProductoNoEncontrado as e:
        print(f"Producto no encontrado: {e}")
    except StockInsuficiente as e:
        print(f"Stock insuficiente: {e}")

    # Caso 3: Stock insuficiente
    try:
        total = tienda.vender("A01", 100)
    except ProductoNoEncontrado as e:
        print(f"Producto no encontrado: {e}")
    except StockInsuficiente as e:
        print(f"Stock insuficiente: {e}")


if __name__ == "__main__":
    main()
```

Salida:

```
Venta exitosa. Total: $7,000
Producto no encontrado: Producto 'Z99' no existe.
Stock insuficiente: Stock insuficiente: disponible=3, solicitado=100.
```

**Observen el patrón:**

1. Los **modelos y servicios lanzan** excepciones específicas cuando algo no cumple las reglas.
2. El **punto de entrada (main) captura** esas excepciones y decide qué hacer: mostrar un mensaje, ofrecer una alternativa, registrar el error, etc.
3. Cada capa hace su trabajo: el servicio **señala** los problemas, el main **reacciona** a ellos.

---

## ¿Cuánto código meter en el `try`?

Una pregunta práctica muy importante: **¿qué tan grande debe ser el bloque `try`?**

### Demasiado amplio (malo)

```python
try:
    producto = buscar_producto(codigo)
    validar_stock(producto, cantidad)
    resultado = calcular_total(producto, cantidad)
    actualizar_inventario(producto, cantidad)
    generar_factura(resultado)
    enviar_notificacion(cliente)
except Exception as e:
    print(f"Algo salió mal: {e}")
```

Si hay un error, no saben en cuál de las 6 operaciones falló. Y están capturando `Exception`, que es demasiado genérico.

### Demasiado granular (excesivo)

```python
try:
    producto = buscar_producto(codigo)
except ProductoNoEncontrado:
    print("Producto no encontrado")
    return

try:
    validar_stock(producto, cantidad)
except StockInsuficiente:
    print("Stock insuficiente")
    return

try:
    resultado = calcular_total(producto, cantidad)
except ValueError:
    print("Error en cálculo")
    return

# ... y así para cada línea
```

Cada operación en su propio `try/except` es innecesariamente verboso. El código se vuelve difícil de leer.

### El balance correcto

Agrupen las operaciones que forman una **unidad lógica** y captur excepciones que tengan sentido como grupo:

```python
try:
    producto = buscar_producto(codigo)
    total = calcular_y_ejecutar_venta(producto, cantidad)
except ProductoNoEncontrado:
    print("Producto no encontrado. Verifique el código.")
except StockInsuficiente:
    print("No hay suficiente inventario.")
else:
    generar_factura(total)
    enviar_notificacion(cliente)
```

El `try` contiene la operación de venta (que puede fallar de formas predecibles). El `else` contiene las acciones post-venta (que asumimos que no fallan de las mismas formas). Cada `except` maneja un error específico.

---

## Buenas prácticas

* **Capturar excepciones específicas, no genéricas.** Eviten `except Exception` o peor aún, `except:` (sin tipo). Cada `except` debe nombrar el tipo exacto de error que esperan manejar:

  ```python
  # Bien
  except ProductoNoEncontrado as e:
      ...

  # Mal
  except Exception:
      ...

  # Peor
  except:
      ...
  ```

* **No silenciar excepciones.** El peor error posible es capturar una excepción y no hacer nada:

  ```python
  # NUNCA hagan esto
  try:
      operacion()
  except Exception:
      pass  # ← El error desaparece silenciosamente. Bugs invisibles garantizados.
  ```

  Si capturan una excepción, **hagan algo**: mostrar un mensaje, registrar el error, lanzar otra excepción, tomar una acción correctiva.

* **Lanzar excepciones específicas de dominio.** Sus servicios no deberían lanzar `ValueError` o `KeyError` directamente. Creen excepciones de dominio que comuniquen el **significado** del error:

  ```python
  # En vez de esto:
  raise ValueError("No hay suficiente stock")

  # Hagan esto:
  raise StockInsuficiente(f"Disponible: {stock}, solicitado: {cantidad}")
  ```

* **Usar `from` para encadenar excepciones.** Cuando convierten una excepción de bajo nivel en una de dominio, preserven la causa:

  ```python
  except KeyError as e:
      raise ProductoNoEncontrado("...") from e
  ```

* **Validar en los bordes del sistema.** Las validaciones (y los `raise` correspondientes) van en los puntos de entrada de datos: `__post_init__` de las dataclasses, los métodos públicos de los servicios, las funciones que reciben input del usuario. El interior del sistema debería poder asumir que los datos son válidos.

* **Documentar qué excepciones lanza cada método.** En las especificaciones de los ejercicios del curso siempre listamos qué excepciones lanza cada método y bajo qué condiciones. Hagan lo mismo en sus proyectos:

  ```python
  def vender(self, codigo: str, cantidad: int) -> int:
      """Realiza una venta.

      Raises:
          ProductoNoEncontrado: si el código no existe en el catálogo.
          StockInsuficiente: si no hay suficiente inventario.
          ValueError: si la cantidad es <= 0.
      """
  ```

---

## Errores comunes

### Capturar `Exception` indiscriminadamente

```python
try:
    resultado = calcular(datos)
except Exception:
    print("Error")
```

¿Qué tipo de error fue? ¿Datos inválidos? ¿Bug en el cálculo? ¿Error de tipeo en el código? No lo saben, y eso hace que los bugs sean muy difíciles de diagnosticar.

### Silenciar errores con `pass`

```python
try:
    dato = int(valor)
except ValueError:
    pass  # ← ¿Y ahora qué valor tiene 'dato'? → NameError más adelante
```

El error se "fue", pero el programa sigue ejecutándose en un estado inconsistente. Eso genera errores secundarios que no tienen nada que ver con el error original, haciendo el debug una pesadilla.

### Capturar y lanzar inmediatamente sin agregar valor

```python
try:
    resultado = operacion()
except OperacionError as e:
    raise OperacionError(str(e))  # ← Esto no agrega nada, y pierde el traceback original
```

Si no van a hacer nada con la excepción, no la capturen. Déjenla propagarse naturalmente. Si quieren re-lanzarla, usen `raise` sin argumentos para preservar el traceback.

### Poner `return` en el `finally`

```python
def obtener_valor() -> int:
    try:
        return 42
    finally:
        return -1  # ← Esto SIEMPRE gana. La función siempre retorna -1.
```

El `finally` siempre se ejecuta, y si tiene un `return`, ese valor sobreescribe cualquier otro valor de retorno. Es un comportamiento confuso. **Nunca pongan `return` en un bloque `finally`.**

### Excepciones demasiado genéricas en el dominio

```python
class AppError(Exception):
    pass

# En TODAS partes del código:
raise AppError("Algo salió mal")
raise AppError("Otro problema")
raise AppError("Otra cosa")
```

Si todas las excepciones son del mismo tipo, no pueden manejarlas de forma diferenciada. Creen excepciones específicas para cada situación: `ProductoNoEncontrado`, `StockInsuficiente`, `MontoInvalido`, etc.

---

## Resumen: el flujo de excepciones en una aplicación modular

Poniéndolo todo junto, así se ve el flujo de excepciones en los proyectos del curso:

```
┌─────────────────────────────────────────────────────────────┐
│  main.py (punto de entrada)                                 │
│                                                             │
│  try:                                                       │
│      resultado = servicio.operacion(datos)                  │
│  except ExcepcionEspecifica as e:    ← CAPTURA y REACCIONA  │
│      manejar_error(e)                                       │
│  else:                                                      │
│      mostrar_resultado(resultado)                           │
└─────────────┬───────────────────────────────────────────────┘
              │ llama
              ▼
┌─────────────────────────────────────────────────────────────┐
│  servicio.py (lógica de negocio)                            │
│                                                             │
│  def operacion(datos):                                      │
│      if no_cumple_regla:                                    │
│          raise ExcepcionDominio("...")   ← LANZA            │
│      # ... lógica normal ...                                │
│      return resultado                                       │
└─────────────┬───────────────────────────────────────────────┘
              │ usa
              ▼
┌─────────────────────────────────────────────────────────────┐
│  modelos.py (clases de datos)                               │
│                                                             │
│  @dataclass                                                 │
│  class Modelo:                                              │
│      campo: str                                             │
│      def __post_init__(self):                               │
│          if not self.campo:                                  │
│              raise DatoInvalido("...")   ← LANZA            │
└─────────────────────────────────────────────────────────────┘
              │ hereda de
              ▼
┌─────────────────────────────────────────────────────────────┐
│  excepciones.py (jerarquía de excepciones)                  │
│                                                             │
│  ErrorDeDominio (base)                                      │
│  ├── ProductoNoEncontrado                                   │
│  ├── StockInsuficiente                                      │
│  ├── MontoInvalido                                          │
│  └── DatoInvalido                                           │
└─────────────────────────────────────────────────────────────┘
```

Los **modelos** y **servicios** lanzan excepciones. El **main** las captura y decide qué hacer. Las **excepciones** son el vocabulario compartido que conecta estas capas.

---

## Preguntas para pensar

1. ¿Por qué en los ejemplos del curso los servicios (`VendingMachine`, `TicketOffice`) lanzan excepciones pero nunca las capturan? ¿Quién debería capturarlas?
2. Si tienen un `except Exception` que captura un `KeyboardInterrupt`, ¿eso es un problema? ¿Por qué sí o por qué no?
3. ¿Cuándo usarían `else` en un `try/except`? Den un ejemplo concreto.
4. ¿Qué pasaría si la clase `StockInsuficiente` NO heredara de `ErrorDeDominio`? ¿Cómo afectaría al código que captura excepciones?
5. En el patrón EAFP, ¿hay algún caso donde sea mejor verificar primero (LBYL)? ¿Cuál?
6. Si un método puede lanzar 5 excepciones diferentes, ¿deberían capturarlas todas individualmente o usar la excepción base? ¿Depende del contexto?
