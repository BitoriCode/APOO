# Diccionarios en Python

Los **diccionarios** son estructuras de datos que almacenan pares *clave–valor*. Se parecen a listas, pero con índices más generales: “en una lista, los índices deben ser enteros; en un diccionario, los índices pueden ser (casi) cualquier tipo”. Cada elemento del diccionario se llama **par clave-valor**, donde la clave (key) apunta a un valor (value) asociado. Las claves deben ser únicas (no pueden repetirse en el mismo diccionario) y **deben ser objetos *hashable*** (inmutables como cadenas, números o tuplas); de lo contrario Python lanzará un `TypeError`. Los valores, en cambio, pueden ser de cualquier tipo, incluyendo objetos mutables o incluso otros diccionarios. Además, los diccionarios son **mutables**: es decir, puedes crear uno y luego modificarlo añadiendo, cambiando o eliminando elementos. El uso de corchetes `{}` indica un diccionario, por ejemplo `d = {'a': 1, 'b': 2}`.

Internamente, los diccionarios se implementan como **tablas hash**, lo que permite un acceso muy rápido a los elementos mediante su clave. De hecho, el operador `in` en un diccionario utiliza una tabla hash que mantiene una complejidad de tiempo *O(1)* en promedio; es decir, verificar la existencia de una clave lleva el mismo tiempo independientemente del tamaño del diccionario. Gracias a esto, operaciones como obtener, insertar o eliminar un valor por clave suelen ser muy eficientes (constantes en promedio). Hay que notar que en las versiones modernas de Python (≥3.7) los diccionarios mantienen el **orden de inserción** de las claves (anteriormente se decía que el orden era impredecible, pero hoy en CPython está garantizado aunque conceptualmente no se debe confiar en un orden numérico).

En resumen, un diccionario de Python es una **colección no ordenada de pares clave-valor con claves únicas**, implementada con tabla hash para acceso rápido. Por ejemplo:

```python
# Creación de un diccionario
persona = {"nombre": "Ana", "edad": 25}
print(persona["nombre"])    # Accede por clave: 'Ana'
```

# Analogías del mundo real

* **Diccionario de idioma**: igual que un diccionario de palabras donde cada palabra (clave) tiene una definición (valor). Python toma prestado este nombre: “el nombre viene de la analogía con un diccionario del mundo real, en donde la información se encuentra en formato palabra(llave)\:definición(valor)”. Por ejemplo, clave `"gato"` → valor `"animal doméstico felino"`.
* **Agenda telefónica o lista de contactos**: cada entrada tiene un nombre o identificador único (clave) y el número de teléfono o datos asociados (valor). Así podemos buscar rápidamente el teléfono si conocemos el nombre (clave).
* **Inventario de cocina**: como en el ejemplo de cocina, donde cada ingrediente es una clave y su descripción o cantidad es el valor. Por ejemplo, `"harina": "1 kg"`.
* **Índice de libro o tabla de referencias**: se almacena el término clave (tópico, palabra) junto al número de página o sección (valor), permitiendo encontrar la información rápidamente por la clave.

Estas analogías resaltan que en un diccionario importan las relaciones *clave→valor*, no posiciones numéricas. La clave actúa como etiqueta para hallar el valor asociado, similar a buscar la definición de una palabra en un diccionario real.

# Ejemplos prácticos en Python

Los diccionarios se crean con llaves `{}` o con la función `dict()`. Por ejemplo:

```python
# Crear diccionarios
d1 = {"nombre": "Juan", "edad": 30}
d2 = dict(ciudad="Lima", pais="Perú")
```

Acceder o modificar elementos se hace por clave:

```python
print(d1["nombre"])     # 'Juan'
d1["edad"] = 31         # Actualiza el valor asociado a 'edad'
d1["email"] = "j@mail.com"  # Añade un nuevo par
```

Al acceder a una clave inexistente con corchetes, Python lanza un `KeyError`. Para evitarlo, se puede usar `get()`, que devuelve un valor por defecto si no existe la clave:

```python
print(d1.get("edad", 0))     # 31
print(d1.get("telefono", "N/A"))  # 'N/A' (clave no existe)
```

Otra forma de recorrer un diccionario es iterar sobre sus claves o elementos. Por defecto, `for clave in dict` itera sobre las claves:

```python
for clave in d1:
    print(clave, d1[clave])
# Output:
# nombre Juan
# edad 31
# email j@mail.com
```

También pueden usarse métodos como `keys()`, `values()` e `items()`:

```python
print(list(d1.keys()))   # lista de claves, p.ej. ['nombre', 'edad', 'email']
print(list(d1.values())) # lista de valores, p.ej. ['Juan', 31, 'j@mail.com']
print(list(d1.items()))  # lista de tuplas (clave, valor)
```

Los diccionarios son muy útiles en casos comunes como **contar ocurrencias**. Por ejemplo, contar letras o palabras en un texto:

```python
texto = "hola hola mundo"
contadores = {}
for palabra in texto.split():
    contadores[palabra] = contadores.get(palabra, 0) + 1

print(contadores)  # {'hola': 2, 'mundo': 1}
```

Otro caso es **fusionar información**: combinar dos diccionarios mediante `update()`.

```python
dA = {"x": 1, "y": 2}
dB = {"y": 3, "z": 4}
dA.update(dB)
print(dA)  # {'x': 1, 'y': 3, 'z': 4}
```

Este ejemplo sobrescribe el valor de `"y"` por el proveniente de `dB`. En resumen, los diccionarios facilitan representar estructuras de datos asociativas, llevar conteos, agrupar datos etiquetados, caches, configuraciones, etc.

# Métodos y funciones de diccionario

Python ofrece varios métodos integrados para trabajar con diccionarios. A continuación se repasan los más importantes:

* **`clear()`**: Elimina todos los elementos del diccionario.

  * *Sintaxis:* `diccionario.clear()`
  * *Ejemplo:*

    ```python
    d = {"a":1, "b":2}
    d.clear()
    print(d)  # {}
    ```
  * *Errores comunes:* No genera error; simplemente deja el diccionario vacío (len=0).

* **`copy()`**: Devuelve una copia (shallow) del diccionario original.

  * *Sintaxis:* `copia = diccionario.copy()`
  * *Ejemplo:*

    ```python
    origen = {"x": 10}
    copia = origen.copy()
    copia["x"] = 20
    print(origen)  # {'x': 10}, copia es independiente de origen
    ```
  * *Errores comunes:* Es copia superficial; si el valor es un objeto mutable, ambos diccionarios comparten el mismo objeto.

* **`fromkeys(iterable, valor=None)`**: Crea un nuevo diccionario cuyos keys son los elementos del iterable dado, asignando a cada uno el *valor* especificado.

  * *Sintaxis:* `nuevo = dict.fromkeys(["a", "b"], 0)`
  * *Ejemplo:*

    ```python
    d = dict.fromkeys(["a", "b", "c"], 0)
    print(d)  # {'a': 0, 'b': 0, 'c': 0}
    ```
  * *Errores comunes:* Si no se proporciona `valor`, por defecto es `None`. No altera un diccionario existente, siempre retorna uno nuevo.

* **`get(key, default=None)`**: Devuelve el valor asociado a *key*; si la clave no existe, devuelve *default* (None si no se da otro).

  * *Sintaxis:* `valor = diccionario.get(key[, default])`
  * *Ejemplo:*

    ```python
    d = {"a": 1}
    print(d.get("a", 0))  # 1
    print(d.get("z", 0))  # 0 (clave ausente)
    ```
  * *Errores comunes:* No genera error por clave ausente (devuelve default). Si se omite *default* y la clave no existe, devuelve `None`.

* **`items()`**: Devuelve una vista (iterable) de los pares `(clave, valor)` del diccionario.

  * *Sintaxis:* `pares = diccionario.items()`
  * *Ejemplo:*

    ```python
    d = {"x": 10, "y": 20}
    for k, v in d.items():
        print(k, "→", v)
    # Imprime: 
    # x → 10
    # y → 20
    ```
  * *Errores comunes:* Devuelve un *view* dinámico, no lista; si se modifica el diccionario después, la vista refleja los cambios. Para una lista fija, usar `list(d.items())`.

* **`keys()`**: Devuelve una vista de las **claves** del diccionario.

  * *Sintaxis:* `claves = diccionario.keys()`
  * *Ejemplo:*

    ```python
    d = {"nombre": "Ana", "edad": 25}
    print("nombre" in d.keys())  # True
    # También puede usarse directamente 'nombre' in d
    ```
  * *Errores comunes:* Al igual que `items()`, retorna una vista. Las operaciones de pertenencia con `in` son equivalentes a usar `diccionario`, pues busca entre las claves.

* **`pop(key[, default])`**: Elimina el elemento con la clave dada y devuelve su valor. Si la clave no existe y se proporcionó *default*, devuelve *default*; de lo contrario lanza `KeyError`.

  * *Sintaxis:* `valor = diccionario.pop(key[, default])`
  * *Ejemplo:*

    ```python
    d = {"a":1, "b":2}
    x = d.pop("b")
    print(x, d)  # 2, {'a':1}
    y = d.pop("z", 0)
    print(y, d)  # 0, {'a':1}
    ```
  * *Errores comunes:* Si la clave no existe y no se da `default`, se lanza `KeyError`.

* **`popitem()`**: Elimina y devuelve *un* par (clave, valor) del diccionario. Desde Python 3.7 lo hace con el *último* par insertado. Si el diccionario está vacío lanza `KeyError`.

  * *Sintaxis:* `clave, valor = diccionario.popitem()`
  * *Ejemplo:*

    ```python
    d = {"x": 1, "y": 2}
    par = d.popitem()
    print(par, d)  # ('y', 2), {'x':1}
    ```
  * *Errores comunes:* No llamar a `popitem()` si el diccionario está vacío, pues causará `KeyError`. Tampoco confiar en que elimine un elemento “aleatorio”: en las versiones actuales elimina el último insertado.

* **`setdefault(key, default=None)`**: Si la clave existe, retorna su valor. Si no existe, inserta la clave con el valor *default* especificado y lo retorna.

  * *Sintaxis:* `valor = diccionario.setdefault(key[, default])`
  * *Ejemplo:*

    ```python
    d = {"a": 1}
    v = d.setdefault("b", 0)
    print(v, d)  # 0, {'a':1, 'b':0}
    v2 = d.setdefault("a", 99)
    print(v2, d) # 1, {'a':1, 'b':0} (no cambia 'a')
    ```
  * *Errores comunes:* Si *default* es un objeto mutable (lista, dict, etc.), recuerda que todos los inserts usarán la misma referencia. Además, no provoca error por clave ausente.

* **`update([otro])`**: Actualiza el diccionario con pares de otro diccionario u objeto iterable de pares `(clave, valor)`. Si hay claves repetidas, sus valores serán sobrescritos.

  * *Sintaxis:* `diccionario.update(otro_diccionario)`
  * *Ejemplo:*

    ```python
    d = {"a":1}
    d.update({"b":2, "a":3})
    print(d)  # {'a':3, 'b':2}
    ```
  * *Errores comunes:* Debe pasarse un iterable de pares o un dict. Llamar con tipos incompatibles (por ejemplo, un entero) causará un `TypeError`.

* **`values()`**: Devuelve una vista de los **valores** del diccionario.

  * *Sintaxis:* `valores = diccionario.values()`
  * *Ejemplo:*

    ```python
    d = {"a":1, "b":2}
    print(list(d.values()))  # [1, 2]
    ```
  * *Errores comunes:* Igual que `items()` y `keys()`, `values()` retorna una vista dinámica. Si necesitas una lista estática de valores, convierte con `list()`.

Además de estos métodos, cabe recordar algunas funciones internas útiles: `len(diccionario)` devuelve el número de pares clave-valor; `in` verifica existencia de clave; la sentencia `del diccionario[key]` elimina un par dado (lanza `KeyError` si no existe). Por ejemplo, `del d["a"]` remueve la clave `"a"`.

# Buenas prácticas y advertencias

* **Evitar errores por claves inexistentes:** Antes de acceder con `d[key]`, comprueba que la clave exista (`if key in d`) o usa `get()`/`pop(key, default)` para manejar ausencias y evitar `KeyError`. Por ejemplo, con `d.get(key, valor_por_defecto)` se evita excepción y se obtiene un valor predefinido.
* **Claves inmutables:** No uses listas u otros objetos mutables como clave (producto de colisión o TypeError). Las claves deben ser *hashable* (cadenas, números, tuplas, etc.).
* **Orden e inserción:** Aunque Python 3.7+ mantiene orden de inserción, no debe asumirse orden numérico. Si necesitas claves ordenadas, ordénalas explícitamente con `sorted(d.keys())`.
* **Modificar durante iteración:** Ten cuidado al cambiar un diccionario mientras lo iteras (añadir/eliminar claves) porque puede llevar a comportamientos inesperados o errores de runtime. Usar copias (`d.copy()`) si vas a modificar mientras iteras.
* **Uso de `setdefault` y valores mutables:** Al usar `setdefault(key, lista_vacía)`, recuerda que todas las claves nuevas compartirán esa misma lista si la claves no existían. Mejor usar `defaultdict` de `collections` para cuentas o agrupaciones acumulativas.
* **`popitem()` con lógica dependiente:** No abuses de `popitem()` en código crítico; su utilidad principal es extraer “el último elemento”. No asumas más allá de eso. Siempre revisa que el diccionario no esté vacío (KeyError).
* **Elegir estructura adecuada:** No uses diccionario cuando un simple número índice o secuencia ordenada sea suficiente. Por ejemplo, si solo necesitas colección de elementos sin clave, lista o tupla suele ser más simple y eficiente en memoria. Sin embargo, si buscas acceso rápido por un identificador custom (string, ID, etc.), usa diccionario.

# Comparación con listas y tuplas

A diferencia de las **listas** o **tuplas**, los diccionarios no están basados en posición numérica sino en asociación clave→valor. Una lista mantiene un orden secuencial y sus elementos se acceden por índice entero, ideal cuando importa la posición y se requiere iterar en orden. En cambio, un diccionario es útil cuando cada elemento tiene una **etiqueta o clave única** para acceso rápido (por ejemplo, un registro de configuraciones, datos de usuario indexados por nombre de campo, etc.). Las tuplas son como listas inmutables (útiles para registros fijos), y pueden usarse como claves (siempre que sus contenidos sean inmutables). En resumen:

* Use **lista/tupla** si necesita una secuencia ordenada de elementos o solo índices numéricos.
* Use **diccionario** si necesita mapear claves arbitrarias a valores y acceder eficientemente por clave. (Por ejemplo, en lugar de una lista donde el significado de cada posición está “documentado”, en un diccionario cada clave es auto-explicativa).



**Fuentes:** Todo lo anterior está basado en la documentación y tutoriales de Python, adaptado con ejemplos claros para fines docentes.
