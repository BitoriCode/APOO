## Ejercicio 1: Inventario de productos (diccionario básico)

1. En el módulo `inventario.py`, cree una clase `Inventario` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `existencias` (diccionario) donde la **clave** es `str` (producto) y el **valor** es `int` (unidades).

2. En el **constructor**, asigne `existencias = {}`.

3. Defina el método de instancia `agregar_producto(nombre: str, cantidad: int) -> None`.

   * Si `nombre` **no existe** en `existencias`, créelo con `cantidad`.
   * Si **existe**, sume `cantidad`.

4. Defina `quitar_producto(nombre: str, cantidad: int) -> bool`.

   * Si hay suficientes unidades, descuente y retorne `True`; si no, retorne `False`.

5. Defina `stock_de(nombre: str) -> int` (retorna la cantidad en stock o `0` si no existe).

6. Defina `total_items() -> int` (sume todas las cantidades recorriendo el diccionario).

---

## Ejercicio 2: Notas por estudiante (diccionario con listas)

1. En el módulo `notas.py`, cree una clase `RegistroNotas` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `por_estudiante` (diccionario) donde la **clave** es `str` (nombre) y el **valor** es `list[float]` (notas).

2. En el **constructor**, asigne `por_estudiante = {}`.

3. Defina `agregar_estudiante(nombre: str) -> None` (cree la entrada con lista vacía solo si no existe).

4. Defina `agregar_nota(nombre: str, nota: float) -> None`.

   * Si el estudiante no existe, créelo primero con una lista vacía.
   * Luego agregue la nota al final.

5. Defina `promedio_de(nombre: str) -> float`.

   * Si no existe o no tiene notas, retorne `0.0`; si tiene, sume y divida por la cantidad usando un **bucle**.

6. Defina `mejor_estudiante() -> str | None`.

   * Recorra el diccionario, calcule promedios y retorne el nombre con mayor promedio (o `None` si no hay datos).

---

## Ejercicio 3: Diccionario de traducciones (clave→valor)

1. En el módulo `traducciones.py`, cree una clase `DiccionarioTraduccion` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `pares` (diccionario) donde la **clave** es `str` (palabra origen) y el **valor** es `str` (traducción).

2. En el **constructor**, asigne `pares = {}`.

3. Defina `agregar_palabra(origen: str, destino: str) -> None`.

   * Si `origen` no existe, créelo; si existe, **actualícelo** con `destino`.

4. Defina `traducir_palabra(origen: str) -> str | None` (retorne la traducción o `None` si no existe).

5. Defina `traducir_frase(palabras: list[str]) -> list[str]`.

   * Recorra la lista, y por cada palabra agregue su traducción si existe; si no, agregue la palabra original.

---

## Ejercicio 4: Biblioteca y préstamos (diccionario con listas)

1. En el módulo `biblioteca.py`, cree una clase `Biblioteca` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `prestamos` (diccionario) donde la **clave** es `str` (usuario) y el **valor** es `list[str]` (títulos prestados).

2. En el **constructor**, asigne `prestamos = {}`.

3. Defina `registrar_usuario(usuario: str) -> None` (cree la lista vacía si no existe).

4. Defina `prestar(usuario: str, titulo: str) -> None`.

   * Cree al usuario si falta.
   * Agregue `titulo` **solo si no está ya** (evite duplicados recorriendo la lista).

5. Defina `devolver(usuario: str, titulo: str) -> bool`.

   * Si el título está en su lista, elimínelo y retorne `True`; si no, `False`.

6. Defina `prestados_de(usuario: str) -> list[str]`.

   * Si no existe, retorne `[]`; si existe, retorne una **copia** (construida con un bucle).

---

## Ejercicio 5: Carrito de compras (diccionario producto→cantidad)

1. En el módulo `carrito.py`, cree una clase `Carrito` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `cantidades` (diccionario) donde la **clave** es `str` (producto) y el **valor** es `int` (cantidad).

2. En el **constructor**, asigne `cantidades = {}`.

3. Defina `agregar(producto: str, cantidad: int) -> None`.

   * Si el producto no existe, créelo con esa cantidad; si existe, **sume**.

4. Defina `quitar(producto: str, cantidad: int) -> bool`.

   * Si existe y alcanza, **reste**; si queda en `0`, elimine la clave; retorne `True`.
   * Si no alcanza o no existe, retorne `False`.

5. Defina `cantidad_total_items() -> int` (sume todas las cantidades).

6. Defina `productos() -> list[str]` (retorne los nombres de productos actuales recorriendo las **claves**).

---

## Ejercicio 6: Contador de palabras (diccionario palabra→frecuencia)

1. En el módulo `texto.py`, cree una clase `ContadorPalabras` con los siguientes **atributos de la clase** (se inicializan en el **constructor**):
   `frecuencias` (diccionario) donde la **clave** es `str` (palabra en minúsculas) y el **valor** es `int` (ocurrencias).

2. En el **constructor**, asigne `frecuencias = {}`.

3. Defina `agregar_linea(linea: str) -> None`.

   * Use `split()` para obtener palabras, conviértalas a **minúsculas** y cuéntelas en `frecuencias`.

4. Defina `veces_de(palabra: str) -> int` (retorne su frecuencia o `0` si no existe).

5. Defina `palabra_mas_frecuente() -> str | None`.

   * Si el diccionario está vacío, `None`; si no, recorra y retorne la **clave** con mayor **valor**.

---
