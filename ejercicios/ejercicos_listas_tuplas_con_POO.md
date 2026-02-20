
## Ejercicio 1: Playlist de canciones (lista de tuplas)

Haz el codigo con los siguientez pasos.

1. En el módulo `playlist.py`, cree una clase `Playlist` que tenga los siguientes atributos:
   `nombre` (str), `pistas` (lista).
   El atributo `pistas` será una **lista de tuplas** con el formato `(titulo, duracion_seg)`.

2. En el método constructor, reciba el parámetro `nombre` para inicializar el atributo correspondiente.
   El atributo `pistas` inicialícelo como una lista vacía.

3. En la clase `Playlist`, defina un método de instancia `agregar_pista` que recibe `titulo` (str) y `duracion_seg` (int).
   Cree una tupla `(titulo, duracion_seg)` y agréguela al final de la lista `pistas`.

4. En la clase `Playlist`, defina un método de instancia `cantidad_pistas` que **retorna** un entero con la cantidad de pistas (use `len`).

5. En la clase `Playlist`, defina un método de instancia `duracion_total` que **retorna** un entero con la suma de la duración de todas las pistas. Recorra `pistas` y acumule los segundos.

6. En la clase `Playlist`, defina un método de instancia `pista_mas_larga` que **retorna** la tupla `(titulo, duracion_seg)` con mayor duración.
   Si no hay pistas, retorne `None`.

7. En la clase `Playlist`, defina el método especial `__str__` que retorna un `str` con el formato: `"nombre - N pistas"` donde `N` es la cantidad de elementos en `pistas`.

---


## Ejercicio 2: Serie numérica (lista)

Haz el codigo con los siguientez pasos.

1. En el módulo `series.py`, cree una clase `SerieNumerica` con los atributos:
   `nombre` (str), `valores` (lista).
   El atributo `valores` será una **lista** de números.

2. En el método constructor, reciba `nombre` (str) e inicialice `valores` como lista vacía.

3. En la clase `SerieNumerica`, defina un método de instancia `agregar_valor` que recibe `v` (float o int) y lo agrega al final de `valores`.

4. En la clase `SerieNumerica`, defina un método de instancia `cantidad` que **retorna** un entero con la cantidad de elementos en `valores`.

5. En la clase `SerieNumerica`, defina un método de instancia `minimo` que **retorna** el menor valor de la lista o `None` si la lista está vacía.
   Recorra `valores` con un bucle y compare para encontrar el mínimo.

6. En la clase `SerieNumerica`, defina un método de instancia `maximo` que **retorna** el mayor valor de la lista o `None` si la lista está vacía.

7. En la clase `SerieNumerica`, defina un método de instancia `promedio` que **retorna** un `float` con el promedio de los valores.
   Si la lista está vacía, retorne `0.0`.

---

## Ejercicio 3: Cola de tareas (lista de tuplas)

Haz el codigo con los siguientez pasos.

1. En el módulo `tareas.py`, cree una clase `ColaTareas` con los atributos:
   `nombre` (str), `items` (lista).
   El atributo `items` será una **lista de tuplas** con el formato `(titulo, minutos_estimados)`.

2. En el método constructor, reciba `nombre` (str) e inicialice `items` como lista vacía.

3. En la clase `ColaTareas`, defina un método de instancia `agregar` que recibe `titulo` (str) y `minutos` (int), y agrega la tupla al final de `items`.

4. En la clase `ColaTareas`, defina un método de instancia `siguiente` que **retorna** la primera tupla `(titulo, minutos)` y la **elimina** de la cola.
   Si `items` está vacía, retorne `None`.

5. En la clase `ColaTareas`, defina un método de instancia `tiempo_total` que **retorna** un entero con la suma de `minutos` de todas las tareas (recorra `items` y acumule).

6. En la clase `ColaTareas`, defina el método especial `__str__` que retorna `"nombre - N tareas"`.

---

## Ejercicio 4: Álbum de fotos (lista de tuplas)

Haz el codigo con los siguientez pasos.

1. En el módulo `fotos.py`, cree una clase `AlbumFotos` con los atributos:
   `titulo` (str), `fotos` (lista).
   El atributo `fotos` será una **lista de tuplas** `(ancho_px, alto_px)`.

2. En el método constructor reciba `titulo` (str) e inicialice `fotos` como lista vacía.

3. En la clase `AlbumFotos`, defina un método de instancia `agregar_foto` que recibe `ancho_px` (int) y `alto_px` (int) y agrega la tupla al final de `fotos`.

4. En la clase `AlbumFotos`, defina un método de instancia `cantidad` que **retorna** un entero con la cantidad de fotos.

5. En la clase `AlbumFotos`, defina un método de instancia `conteo_orientacion` que **retorna** una tupla `(apaisadas, verticales, cuadradas)` contando:

   * apaisada si `ancho_px > alto_px`
   * vertical si `alto_px > ancho_px`
   * cuadrada si `ancho_px == alto_px`
     Recorra `fotos` y acumule estos tres contadores.

6. En la clase `AlbumFotos`, defina un método de instancia `foto_mayor_area` que **retorna** la tupla `(ancho_px, alto_px)` con área máxima (`ancho*alto`).
   Si no hay fotos, retorne `None`.

---

## Ejercicio 5: Bitácora de viaje (lista de tuplas)

Haz el codigo con los siguientez pasos.

1. En el módulo `viajes.py`, cree una clase `BitacoraViaje` con los atributos:
   `nombre` (str), `paradas` (lista).
   El atributo `paradas` será una **lista de tuplas** `(ciudad, dias_estadia)`.

2. En el método constructor, reciba `nombre` (str) e inicialice `paradas` como lista vacía.

3. En la clase `BitacoraViaje`, defina un método de instancia `agregar_parada` que recibe `ciudad` (str) y `dias` (int) y agrega la tupla al final de `paradas`.

4. En la clase `BitacoraViaje`, defina un método de instancia `total_dias` que **retorna** un entero con la suma de `dias` de todas las paradas.

5. En la clase `BitacoraViaje`, defina un método de instancia `primera_parada` que **retorna** la primera tupla `(ciudad, dias)` de la lista o `None` si la lista está vacía.

6. En la clase `BitacoraViaje`, defina un método de instancia `ultima_parada` que **retorna** la última tupla `(ciudad, dias)` de la lista o `None` si la lista está vacía.

7. En la clase `BitacoraViaje`, defina el método especial `__str__` que retorna un `str` con el formato:
   `"nombre - N paradas, T días"` donde `N` es la cantidad de paradas y `T` es el total de días.

---
