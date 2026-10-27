# Archivos, serialización y persistencia en POO

## 1. ¿Por qué necesitamos archivos en POO?

En programación orientada a objetos, las clases representan entidades del mundo real o del problema que estamos resolviendo. Pero cuando el programa termina, normalmente se pierden los datos que estaban en memoria.

Por ejemplo:

- una lista de clientes,
- un inventario de productos,
- una agenda de contactos,
- una biblioteca digital,
- una colección de tareas,
- un registro de ventas,
- un historial de usuarios.

Si queremos que esos datos sigan existiendo entre ejecuciones, necesitamos guardarlos en archivos. A eso se le llama persistencia.

La idea es simple:

- la clase modela el objeto,
- el archivo guarda el estado del objeto,
- la aplicación puede volver a crear ese objeto desde el archivo al iniciar.

---

## 2. Lectura y escritura de archivos en Python

Python permite trabajar con archivos usando la función `open()`. La sintaxis básica es:

```python
archivo = open("ruta/archivo.txt", "modo")
```

### Modos principales

- `"r"` → lectura. El archivo debe existir.
- `"w"` → escritura. Crea el archivo o lo sobrescribe.
- `"a"` → append. Agrega contenido al final.
- `"x"` → creación exclusiva. Falla si el archivo ya existe.
- `"b"` → modo binario.
- `"t"` → modo texto (valor por defecto).

Ejemplos:

```python
# Lectura
archivo = open("datos.txt", "r", encoding="utf-8")
contenido = archivo.read()
archivo.close()

# Escritura
archivo = open("datos.txt", "w", encoding="utf-8")
archivo.write("Hola, mundo\n")
archivo.close()
```

### Uso recomendado: with open

Para evitar olvidarse de cerrar archivos, se usa `with open(...):`.

```python
with open("datos.txt", "r", encoding="utf-8") as archivo:
    contenido = archivo.read()

print(contenido)
```

Esto garantiza que el archivo se cierre automáticamente al salir del bloque.

---

## 3. Métodos básicos para leer archivos

### `read()`

Lee todo el contenido del archivo.

```python
with open("datos.txt", "r", encoding="utf-8") as archivo:
    texto = archivo.read()
    print(texto)
```

### `readline()`

Lee una sola línea.

```python
with open("datos.txt", "r", encoding="utf-8") as archivo:
    linea = archivo.readline()
    print(linea)
```

### `readlines()`

Lee todas las líneas y las devuelve en una lista.

```python
with open("datos.txt", "r", encoding="utf-8") as archivo:
    lineas = archivo.readlines()
    print(lineas)
```

### `write()`

Escribe texto en un archivo.

```python
with open("saludo.txt", "w", encoding="utf-8") as archivo:
    archivo.write("Hola estudiantes\n")
    archivo.write("Bienvenidos a POO\n")
```

### `writelines()`

Escribe una lista de líneas.

```python
lineas = ["Línea 1\n", "Línea 2\n", "Línea 3\n"]

with open("archivo.txt", "w", encoding="utf-8") as archivo:
    archivo.writelines(lineas)
```

### `seek()`

Mueve el cursor de lectura/escritura dentro del archivo.

```python
with open("datos.txt", "r", encoding="utf-8") as archivo:
    print(archivo.read(10))
    archivo.seek(0)
    print(archivo.read())
```

---

## 4. Relación entre archivos y clases

Supongamos que tenemos una clase `Estudiante`:

```python
class Estudiante:
    def __init__(self, nombre: str, codigo: str, promedio: float):
        self.nombre = nombre
        self.codigo = codigo
        self.promedio = promedio
```

Si queremos guardar la información del estudiante en un archivo, podemos hacer esto:

```python
with open("estudiantes.txt", "w", encoding="utf-8") as archivo:
    archivo.write(f"{estudiante.nombre},{estudiante.codigo},{estudiante.promedio}\n")
```

Luego, cuando el programa vuelve a abrirse, podemos leer ese archivo y crear objetos nuevamente.

Esto es la base de la persistencia en aplicaciones orientadas a objetos.

---

## 5. Guardar información de objetos en archivos

Hay dos grandes formas de almacenar objetos:

1. Como texto estructurado (`JSON`, `CSV`, `TXT`)
2. Como objetos Python completos (`pickle`)

### 5.1 Guardar como texto Simple

Cuando guardamos datos simples, convertimos los atributos del objeto en una cadena o en una estructura de diccionario.

```python
class Producto:
    def __init__(self, nombre: str, precio: float, stock: int):
        self.nombre = nombre
        self.precio = precio
        self.stock = stock

    def __str__(self) -> str:
        return f"{self.nombre} - ${self.precio} - Stock: {self.stock}"
```

```python
p = Producto("Laptop", 1200.00, 10)

with open("producto.txt", "w", encoding="utf-8") as archivo:
    archivo.write(f"{p.nombre}|{p.precio}|{p.stock}\n")
```

Luego se puede leer y reconstruir el objeto.

---

## 6. Serialización con JSON

JSON significa JavaScript Object Notation. Es un formato muy usado para intercambiar datos, y Python lo soporta nativamente con el módulo `json`.

### ¿Por qué se usa JSON?

- es legible por humanos,
- es común en APIs,
- es fácil de guardar en archivos,
- funciona bien con diccionarios, listas, cadenas, números y booleanos.

### Convertir objeto a diccionario

La forma más útil en POO es transformar el objeto a un diccionario con sus atributos.

```python
from dataclasses import dataclass, asdict
import json

@dataclass
class Persona:
    nombre: str
    edad: int
    ciudad: str

p = Persona("Ana", 21, "Bogotá")

d = asdict(p)
print(d)
```

Salida:

```python
{'nombre': 'Ana', 'edad': 21, 'ciudad': 'Bogotá'}
```

### Guardar un diccionario como JSON

```python
with open("persona.json", "w", encoding="utf-8") as archivo:
    json.dump(d, archivo, ensure_ascii=False, indent=2)
```

### Leer JSON desde un archivo

```python
with open("persona.json", "r", encoding="utf-8") as archivo:
    datos = json.load(archivo)

print(datos)
print(type(datos))
```

### Reconstruir un objeto desde JSON

```python
class Persona:
    def __init__(self, nombre: str, edad: int, ciudad: str):
        self.nombre = nombre
        self.edad = edad
        self.ciudad = ciudad

    def __str__(self) -> str:
        return f"{self.nombre} tiene {self.edad} años y vive en {self.ciudad}."

with open("persona.json", "r", encoding="utf-8") as archivo:
    datos = json.load(archivo)

p = Persona(datos["nombre"], datos["edad"], datos["ciudad"])
print(p)
```

### Ejemplo con una clase `CuentaBancaria`

```python
class CuentaBancaria:
    def __init__(self, titular: str, saldo: float):
        self.titular = titular
        self.saldo = saldo

    def depositar(self, monto: float) -> None:
        if monto <= 0:
            raise ValueError("El monto debe ser positivo.")
        self.saldo += monto

    def mostrar(self) -> str:
        return f"Titular: {self.titular} | Saldo: ${self.saldo}"

cuenta = CuentaBancaria("Lucía", 5000)

payload = {
    "titular": cuenta.titular,
    "saldo": cuenta.saldo,
}

with open("cuenta.json", "w", encoding="utf-8") as archivo:
    json.dump(payload, archivo, ensure_ascii=False, indent=2)
```

### Ventajas de JSON

- muy legible,
- compatible con muchos lenguajes,
- perfecto para guardar estado de objetos simples,
- útil en APIs, sistemas web y configuraciones.

### Limitaciones de JSON

- no puede serializar directamente funciones,
- no puede guardar tipos complejos como objetos personalizados a menos que los conviertas a diccionario,
- requiere un paso de conversión antes de guardar y al recuperar.

---

## 7. Serialización con pickle

El módulo `pickle` permite guardar objetos Python completos, sin necesidad de convertirlos a diccionario manualmente.

```python
import pickle

class Alumno:
    def __init__(self, nombre: str, nota: float):
        self.nombre = nombre
        self.nota = nota

    def __str__(self) -> str:
        return f"{self.nombre} - nota: {self.nota}"

alumno = Alumno("Mateo", 4.8)

with open("alumno.pkl", "wb") as archivo:
    pickle.dump(alumno, archivo)
```

### Leer el objeto guardado

```python
with open("alumno.pkl", "rb") as archivo:
    alumno_recuperado = pickle.load(archivo)

print(alumno_recuperado)
```

### Ventajas de pickle

- guarda objetos Python directamente,
- conserva la estructura interna del objeto,
- útil para prototipos o aplicaciones de escritorio simples.

### Limitaciones de pickle

- solo funciona con Python,
- no es un formato estándar,
- no es recomendable para datos que podrían ser manipulados por usuarios externos,
- puede ser inseguro si se carga contenido que no es de confianza.

### Regla práctica

- usa `JSON` cuando guardas datos de negocio o intercambio de información,
- usa `pickle` solo cuando quieres guardar objetos Python en un entorno controlado y seguro.

---

## 8. Aplicación en programación orientada a objetos

### Patrón típico: clase + archivo

Un patrón muy común en programación orientada a objetos es:

```python
class GestorProductos:
    def __init__(self, archivo: str):
        self.archivo = archivo
        self.productos = []

    def cargar(self):
        try:
            with open(self.archivo, "r", encoding="utf-8") as f:
                lineas = f.readlines()
            for linea in lineas:
                nombre, precio, stock = linea.strip().split("|")
                self.productos.append({
                    "nombre": nombre,
                    "precio": float(precio),
                    "stock": int(stock)
                })
        except FileNotFoundError:
            self.productos = []

    def guardar(self):
        with open(self.archivo, "w", encoding="utf-8") as f:
            for producto in self.productos:
                f.write(f"{producto['nombre']}|{producto['precio']}|{producto['stock']}\n")

    def agregar(self, nombre: str, precio: float, stock: int):
        self.productos.append({
            "nombre": nombre,
            "precio": precio,
            "stock": stock,
        })
        self.guardar()
```

Esto encapsula la lógica de persistencia dentro de una clase. La clase es la responsable de guardar y recuperar la información.

---

## 9. Clase con persistencia usando JSON

```python
import json

class Inventario:
    def __init__(self, nombre_archivo: str):
        self.nombre_archivo = nombre_archivo
        self.productos = []

    def cargar(self):
        try:
            with open(self.nombre_archivo, "r", encoding="utf-8") as archivo:
                self.productos = json.load(archivo)
        except FileNotFoundError:
            self.productos = []

    def guardar(self):
        with open(self.nombre_archivo, "w", encoding="utf-8") as archivo:
            json.dump(self.productos, archivo, ensure_ascii=False, indent=2)

    def agregar_producto(self, nombre: str, precio: float, stock: int):
        producto = {
            "nombre": nombre,
            "precio": precio,
            "stock": stock,
        }
        self.productos.append(producto)
        self.guardar()

inventario = Inventario("inventario.json")
inventario.cargar()
inventario.agregar_producto("Mouse", 25.5, 8)
print(inventario.productos)
```

Este enfoque es muy usado en aplicaciones pequeñas y medianas.

---

## 10. Clase con persistencia usando pickle

```python
import pickle

class Agenda:
    def __init__(self, nombre_archivo: str):
        self.nombre_archivo = nombre_archivo
        self.contactos = {}

    def cargar(self):
        try:
            with open(self.nombre_archivo, "rb") as archivo:
                self.contactos = pickle.load(archivo)
        except FileNotFoundError:
            self.contactos = {}

    def guardar(self):
        with open(self.nombre_archivo, "wb") as archivo:
            pickle.dump(self.contactos, archivo)

    def agregar_contacto(self, nombre: str, telefono: str):
        self.contactos[nombre] = telefono
        self.guardar()

agenda = Agenda("agenda.pkl")
agenda.cargar()
agenda.agregar_contacto("Luis", "3001234567")
print(agenda.contactos)
```

Aquí la información se guarda como un diccionario en un archivo binario.

---

## 11. Buenas prácticas para persistencia en POO

1. Separar la lógica de negocio de la lógica de almacenamiento.
2. Usar `with open(...)` para manejar archivos correctamente.
3. Validar datos antes de guardarlos.
4. Capturar `FileNotFoundError` cuando el archivo no exista.
5. Usar JSON cuando el contenido debe ser legible y portable.
6. Usar pickle solo cuando se trabaja estrictamente con Python.
7. No guardar directamente objetos complejos sin convertirlos antes.
8. Mantener una única clase responsable de guardado/carga si el proyecto es pequeño.
9. Cuando la aplicación crece, conviene crear clases como `Repositorio`, `DAO` o `GestorArchivo`.

---

## 12. Errores comunes

### Error 1: olvidar cerrar el archivo

```python
archivo = open("archivo.txt", "w")
archivo.write("texto")
# archivo.close() no se ejecuta
```

Esto puede provocar pérdida de información o corrupción del archivo.

### Error 2: leer un archivo inexistente

```python
with open("no_existe.txt", "r", encoding="utf-8") as archivo:
    contenido = archivo.read()
```

Esto lanza `FileNotFoundError`.

### Error 3: guardar datos no serializables en JSON

```python
import json

objeto = object()
json.dumps(objeto)
```

Esto lanzará un `TypeError` porque `object()` no es un tipo JSON serializable.

### Error 4: guardar objetos con pickle sin considerar seguridad

```python
pickle.load(archivo_desconocido)
```

Esto puede ser peligroso si el archivo proviene de una fuente no confiable.

---

## 13. Resumen conceptual

En POO, una clase representa una entidad, pero los programas necesitan persistencia. Por eso, los objetos se guardan en archivos.

Los conceptos clave son:

- `open()` para abrir el archivo,
- `with open()` para manejar recursos de forma segura,
- `read()`/`write()` para leer y escribir texto,
- `json.dump()`/`json.load()` para guardar datos estructurados,
- `pickle.dump()`/`pickle.load()` para guardar objetos Python completos,
- y una clase de gestión de archivos para encapsular la lógica de lectura y escritura.

La idea central es que el estado del objeto puede ser almacenado y luego recuperado.

---

## 14. Ejemplo integrador en POO

```python
import json

class Producto:
    def __init__(self, nombre: str, precio: float, stock: int):
        self.nombre = nombre
        self.precio = precio
        self.stock = stock

    def vender(self, cantidad: int):
        if cantidad <= 0:
            raise ValueError("La cantidad debe ser positiva.")
        if cantidad > self.stock:
            raise ValueError("No hay suficiente stock.")
        self.stock -= cantidad

    def to_dict(self):
        return {
            "nombre": self.nombre,
            "precio": self.precio,
            "stock": self.stock,
        }


class CatalogoProductos:
    def __init__(self, archivo: str):
        self.archivo = archivo
        self.productos = []

    def cargar(self):
        try:
            with open(self.archivo, "r", encoding="utf-8") as f:
                datos = json.load(f)
            self.productos = [
                Producto(p["nombre"], p["precio"], p["stock"]) for p in datos
            ]
        except FileNotFoundError:
            self.productos = []

    def guardar(self):
        datos = [p.to_dict() for p in self.productos]
        with open(self.archivo, "w", encoding="utf-8") as f:
            json.dump(datos, f, ensure_ascii=False, indent=2)

    def agregar(self, producto: Producto):
        self.productos.append(producto)
        self.guardar()

catalogo = CatalogoProductos("catalogo.json")
catalogo.cargar()

producto1 = Producto("Teclado", 120000, 5)
catalogo.agregar(producto1)

print(catalogo.productos[0].nombre)
```

Este ejemplo refleja muy bien lo que se hace en la POO aplicada a persistencia: la clase `Producto` representa el objeto y la clase `CatalogoProductos` se encarga de guardarlo y recuperarlo del archivo.

---

## 15. Conclusión

Los archivos permiten que los datos de una aplicación sobrevivan a la ejecución del programa. En POO, esto es fundamental porque los objetos tienen estado y ese estado muchas veces debe guardarse.

Los temas de esta unidad son:

- lectura de archivos,
- escritura de archivos,
- manipulación de texto,
- JSON como formato de serialización,
- pickle como serialización de objetos Python,
- y la relación directa entre archivos y clases.

Cuando un objeto necesita “recordarse” o “guardarse”, normalmente se recurre a serialización y persistencia.

---

## 16. Preguntas guía para estudiar

1. ¿Qué diferencia hay entre abrir un archivo en modo lectura y en modo escritura?
2. ¿Por qué es recomendable usar `with open()`?
3. ¿Qué es la serialización?
4. ¿Qué diferencia hay entre `json` y `pickle`?
5. ¿Cómo convertir un objeto a diccionario para guardarlo en JSON?
6. ¿Cómo reconstruir un objeto a partir de un JSON?
7. ¿Cómo puede una clase encapsular la persistencia de sus datos?
8. ¿Cuál es la ventaja de guardar objetos con `JSON` en aplicaciones web o pequeñas APIs?
9. ¿En qué casos conviene usar `pickle`?
10. ¿Qué problemas puede generar no validar los datos antes de guardarlos?

---
