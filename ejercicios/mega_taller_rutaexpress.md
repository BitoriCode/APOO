# Mega taller integrador — RutaExpress

> **Modalidad:** individual, con construcción guiada en clase.  
> **Dos clases de 110 minutos:** el mismo proyecto continúa de una sesión a la otra.  
> **Meta:** pasar del análisis del mundo del problema a una aplicación orientada a objetos, modular y ejecutable.

## 1. Cómo trabajaremos

No recibirás toda la solución para copiarla. En cada etapa encontrarás tres indicaciones:

- **Hace el docente:** analiza o implementa un ejemplo frente al grupo.
- **Implementas tú:** completas una variante o un caso de uso usando lo aprendido.
- **Punto de control:** ejecutas un escenario y explicas por qué tu solución respeta las reglas.

Ya estudiaste los temas; este taller busca **integrarlos y justificar las decisiones**, no repetir sus definiciones. No necesitas terminar tareas nuevas entre las dos clases. Conserva el proyecto de la primera sesión para continuar en la segunda.

**Temas incluidos:** conceptos POO; clases, atributos y métodos; cuatro pilares; listas, tuplas y diccionarios; comprehensions; dataclasses; modularización y abstracción; herencia y polimorfismo; ABC y Protocol; inyección de dependencias y DIP; módulos y paquetes; manejo de excepciones.

**Fuera del taller:** pruebas unitarias, TDD, archivos y serialización, Git/GitHub, menú interactivo, interfaz gráfica, bases de datos y servicios externos.

---

## 2. El mundo del problema

RutaExpress es una pequeña central de mensajería que recibe paquetes para entregas locales y nacionales. Actualmente su operador anota las solicitudes en una libreta: escribe quién envía, el destino, el peso y los cambios de estado. Cuando un remitente consulta, debe buscar entre muchas páginas para saber si el paquete sigue registrado, salió a reparto o ya fue entregado.

La libreta también provoca errores de cobro. Los envíos locales y nacionales comparten información, pero sus tarifas son distintas. Algunas veces el operador cobra un kilogramo exacto como si fueran dos; otras veces olvida cobrar la fracción adicional. RutaExpress necesita que el sistema calcule el valor siempre con la misma regla.

Cada solicitud corresponde a **un paquete** y tiene un código único. Del remitente se conoce su identificación, nombre y un contacto. Un mismo remitente puede tener varios envíos. El destino se representa como un texto que incluye el nombre del destinatario y la dirección; no se necesita administrar un catálogo independiente de destinatarios.

Cuando se recibe una solicitud, el envío queda **registrado** y comienza su historial. Luego, el operador puede despacharlo y, posteriormente, confirmar su entrega. Si el remitente cambia de decisión antes del despacho, el operador puede cancelarlo. No se eliminan solicitudes: una entrega o una cancelación debe poder consultarse después.

El sistema debe rechazar las operaciones que contradigan esas reglas. Por ejemplo, no puede entregar un paquete que todavía no ha sido despachado ni cancelar uno que ya está en ruta. El intento rechazado no debe dejar un cambio parcial en el historial.

Además, RutaExpress quiere informar al contacto del remitente cada registro o cambio de estado exitoso. Durante el taller no enviaremos mensajes reales: primero los mostraremos en consola y después podremos acumularlos en memoria. Ese cambio no debería obligar a reescribir los casos de uso.

Finalmente, el operador necesita consultar un envío por código, listar los de un estado y ver cuántos hay en cada estado. Toda la información vive en memoria: al cerrar el programa se pierde. La conservación del historial significa **durante esa ejecución**, no almacenamiento en un archivo.

### Alcance funcional

El actor que opera la aplicación es el **operador de RutaExpress**. El remitente y el destinatario son interesados del negocio; en este alcance no acceden directamente al programa.

| ID | Necesidad del operador |
|---|---|
| R1 | Registrar un envío nuevo y conocer su tarifa |
| R2 | Consultar un envío por código, incluyendo estado e historial |
| R3 | Despachar un envío registrado |
| R4 | Confirmar la entrega de un envío que está en ruta |
| R5 | Cancelar un envío que aún no ha sido despachado |
| R6 | Listar envíos por estado |
| R7 | Consultar cantidades por estado, incluso si alguna es cero |
| R8 | Informar al remitente cada registro o transición exitosa |

No se administran pagos, distancias, mapas, impuestos, usuarios con contraseña, devoluciones ni solicitudes con varios paquetes.

## 3. Reglas que no se pueden negociar

### Información válida

1. Identificación, nombre, contacto, código y destino deben ser textos no vacíos después de aplicar `.strip()`.
2. Se conservan mayúsculas y minúsculas: `E001` y `e001` son códigos distintos. Los espacios exteriores se eliminan.
3. El peso es un **entero positivo en gramos**. Se rechazan `0`, negativos, decimales, cadenas y booleanos. Aunque Python trate `bool` como una subclase de `int`, `True` no representa un peso válido.
4. No se permiten códigos duplicados. Un intento duplicado no reemplaza el envío anterior.
5. El cliente se valida al construirlo; editar sus datos después no es un caso de uso del taller.

### Tarifas

Todo el dinero se expresa en **pesos enteros**, sin redondeos de punto flotante.

```text
bloques = (peso_gramos + 999) // 1000
tarifa_local = 5000 + 1000 * bloques
tarifa_nacional = 10000 + 2000 * bloques
```

Cada bloque de 1 000 gramos iniciado se cobra completo.

| Peso | Bloques | Local | Nacional |
|---|---:|---:|---:|
| 1 g | 1 | 6 000 | 12 000 |
| 1 000 g | 1 | 6 000 | 12 000 |
| 1 001 g | 2 | 7 000 | 14 000 |

### Estados y seguimiento

| Operación | Estado necesario | Estado resultante |
|---|---|---|
| Registrar solicitud nueva | Objeto nuevo en `registrado` | `registrado` |
| Despachar | `registrado` | `en_ruta` |
| Entregar | `en_ruta` | `entregado` |
| Cancelar | `registrado` | `cancelado` |

- No hay otras transiciones. Repetir una operación también es un error.
- La construcción del envío crea su primera entrada de seguimiento: `registrado` y su fecha.
- Registrar el objeto en el sistema **no agrega una segunda entrada inicial**.
- Cada transición válida agrega exactamente una entrada con estado y fecha.
- `entregado` y `cancelado` son estados terminales.
- Un envío de estado terminal sigue disponible para consulta.
- Un error de negocio no modifica estado, historial ni notificaciones.
- Consultar y generar reportes no modifica los envíos ni envía notificaciones.
- No se expone la lista interna del historial para que otro módulo la cambie.
- El listado vacío es válido; un filtro con un estado desconocido es un error, no un listado vacío.

### Notificaciones

Se genera un mensaje por cada registro exitoso y uno por cada transición exitosa. El contacto utilizado es el del remitente. Los mensajes de este taller no dependen de Internet ni simulan fallos de red.

---

## 4. Clase 1 — Primero comprender, después programar

### Distribución de los 110 minutos

| Minutos | Actividad |
|---|---|
| 0–10 | Presentación del caso y límites |
| 10–30 | Análisis compartido e individual |
| 30–45 | Contratos, paquete, errores y datos |
| 45–65 | Demostración del modelo y envío local |
| 65–90 | Implementación individual |
| 90–100 | Contraste con escenarios |
| 100–110 | Corrección focalizada y cierre |

### Etapa A. Analizar sin empezar por los nombres de las clases

**Hace el docente**

Modela R1 con actor, entradas, precondiciones, resultado y errores. Identifica una responsabilidad del negocio y explica por qué no todo sustantivo del relato debe convertirse en una clase.

**Implementas tú**

Antes de consultar los contratos de la siguiente etapa, entrega un análisis breve:

1. Identifica actor, interesados y objetivo del sistema.
2. Propón un glosario con al menos cinco conceptos del problema.
3. Completa R3, R4 y R5 usando esta estructura:

   | ID | Actor | Entradas | Precondiciones | Resultado | Posible rechazo |
   |---|---|---|---|---|---|
   | … | … | … | … | … | … |

4. Propón entidades y explica cuáles datos y comportamientos corresponden a cada una.
5. Construye una tabla de responsabilidades: **entidad / qué conoce / qué hace / con quién colabora**.
6. Dibuja relaciones y multiplicidades. Justifica un caso de **“es un”** y uno de **“tiene un”**.
7. Representa las transiciones de estado y recorre una entrega exitosa y una cancelación rechazada.
8. Responde: ¿quién debe impedir una entrega anticipada?, ¿por qué no basta con validarla en el código de consola?

**Punto de control A**

Explica la diferencia entre el modelo del negocio y decisiones técnicas como un repositorio o un notificador. Conserva tu propuesta original y anota los ajustes después de la discusión. No se exige acertar todos los nombres antes del contraste.

### Etapa B. Acordar contratos después del análisis

> Continúa aquí cuando el docente cierre la discusión anterior. Estos contratos permiten integrar el trabajo; no sustituyen tu análisis.

Usaremos Python 3.10 o posterior y únicamente biblioteca estándar.

```text
proyecto_rutaexpress\
    rutaexpress\
        __init__.py
        __main__.py
        excepciones.py
        modelos.py
        envios.py
        repositorios.py
        notificaciones.py
        servicio.py
```

En la primera clase solo necesitas `__init__.py`, `__main__.py`, `excepciones.py`, `modelos.py` y `envios.py`. Los demás módulos se agregan en la segunda. Ejecuta desde `proyecto_rutaexpress`:

```console
python -m rutaexpress
```

**Hace el docente**

- Crea el paquete y una jerarquía con `RutaExpressError`, `DatoInvalido`, `CodigoDuplicado`, `EnvioNoEncontrado` y `TransicionInvalida`.
- Implementa el helper `validar_texto(valor: str, campo: str) -> str`: valida y retorna el texto sin espacios exteriores.
- Construye `Cliente` como dataclass con `identificacion`, `nombre` y `contacto`; valida en `__post_init__`.
- Explica el contrato de un registro histórico sin implementar todavía el reto.

**Implementas tú — reto 1: el dato histórico**

Construye `RegistroSeguimiento`:

| Campo | Tipo | Condición |
|---|---|---|
| `estado` | `str` | Se recibe desde las operaciones controladas del envío |
| `fecha` | `datetime` | Se obtiene al construir cada registro |

Usa `@dataclass(frozen=True)` y `field(default_factory=datetime.now)`. Explica por qué `default=datetime.now()` no sería equivalente. No necesitas permitir crear registros sueltos desde la consola ni implementar un catálogo de estados adicional en esta dataclass.

### Etapa C. Un modelo con reglas, no una bolsa de atributos

**Hace el docente**

Construye `Envio(ABC)` con:

- Constructor `(codigo: str, remitente: Cliente, destino: str, peso_gramos: int)`.
- Constante de clase `ESTADOS` como tupla de los cuatro estados.
- Atributos por instancia y propiedades de solo lectura `codigo`, `remitente`, `destino`, `peso_gramos`, `estado` e `historial`.
- `historial -> tuple[RegistroSeguimiento, ...]`, sin exponer la lista interna.
- `despachar()`, `entregar()` y `cancelar()`, con las transiciones acordadas.
- `calcular_tarifa() -> int` como método abstracto.

Luego implementa `EnvioLocal`, incluyendo `super().__init__(...)`, y muestra cómo consultar la tarifa sin conocer detalles internos.

**Implementas tú — reto 2: la variante nacional**

1. Construye `EnvioNacional` usando el mismo contrato.
2. Reutiliza el comportamiento común: no copies las operaciones de estado.
3. Sobrescribe `calcular_tarifa()` con la tarifa nacional.
4. Crea una lista con un envío local y uno nacional y recórrela llamando el mismo método.
5. No selecciones la fórmula desde la consola con `if tipo == ...` ni con `isinstance`.

**Punto de control B — cierre de la primera clase**

Tu ejecución debe demostrar:

- Las seis tarifas de la tabla de pesos.
- Un envío local que pasa por `registrado → en_ruta → entregado`, con tres entradas históricas.
- Un envío nacional cancelado desde `registrado`, con dos entradas.
- Una entrega anticipada rechazada con `TransicionInvalida`, sin modificar el historial.
- Dos objetos con historiales independientes.

Responde: ¿qué es una instancia?, ¿qué atributo es de clase?, ¿dónde aparecen los cuatro pilares?, ¿por qué una tupla por sí sola no vuelve inmutables sus objetos internos?

**Conserva este punto de control.** El docente tendrá una versión de recuperación para continuar la segunda clase si tu integración quedó incompleta.

---

## 5. Clase 2 — Integrar sin amarrar las piezas

### Distribución de los 110 minutos

| Minutos | Actividad |
|---|---|
| 0–10 | Recuperar el modelo funcional |
| 10–30 | Repositorio y contrato nominal |
| 30–50 | Protocolo, servicio y demostración de DI/DIP |
| 50–75 | Casos de uso y segundo notificador |
| 75–90 | Consultas y reportes |
| 90–100 | Historia completa y rechazos |
| 100–110 | Corrección focalizada y defensa individual |

### Etapa D. Consultar por contrato

**Hace el docente**

Define `RepositorioEnvios(ABC)` y construye `RepositorioEnviosMemoria`:

| Método | Contrato |
|---|---|
| `agregar(envio: Envio) -> None` | Rechaza duplicados sin reemplazar el anterior |
| `obtener(codigo: str) -> Envio` | Retorna el envío o lanza `EnvioNoEncontrado` |
| `listar() -> list[Envio]` | Retorna una lista nueva, no el contenedor interno |

El almacenamiento es `dict[str, Envio]`. Obtener un código normaliza espacios exteriores. Una lista nueva protege el contenedor, pero sus elementos siguen siendo los mismos objetos de dominio: sus operaciones públicas controlan las transiciones.

**Implementas tú**

Explica por qué un diccionario resulta apropiado y qué protege una copia de la lista. Recorre un listado y muestra código, estado y tarifa sin acceder al diccionario interno.

### Etapa E. Cambiar una dependencia sin cambiar el caso de uso

**Hace el docente**

1. Define `Notificador(Protocol)` con `enviar(contacto: str, mensaje: str) -> None`.
2. Implementa `NotificadorConsola` sin heredar explícitamente del protocolo.
3. Inicia `ServicioEnvios(repo: RepositorioEnvios, notificador: Notificador)`.
4. Implementa `registrar`, `consultar` y `despachar`, incluyendo el mensaje posterior al éxito.
5. Crea las concreciones **fuera** del servicio, en el punto de entrada, y las inyecta.

Contrato acordado del servicio:

```python
def registrar(self, envio: Envio) -> None: ...
def consultar(self, codigo: str) -> Envio: ...
def despachar(self, codigo: str) -> None: ...
def entregar(self, codigo: str) -> None: ...
def cancelar(self, codigo: str) -> None: ...
def filtrar_por_estado(self, estado: str) -> list[Envio]: ...
def resumen_por_estado(self) -> dict[str, int]: ...
```

`registrar` acepta únicamente objetos nuevos en `registrado`. La validación no crea otra entrada histórica.

**Implementas tú — reto 3: completar la operación**

- Implementa `entregar` y `cancelar` en el servicio.
- Delega las reglas de estado al objeto de dominio.
- Envía el mensaje solo después de una operación exitosa.
- No copies las reglas en el servicio ni captures errores para convertirlos en éxitos.

**Implementas tú — reto 4: otro notificador**

Construye `NotificadorMemoria` sin heredar de `Notificador`:

- Guarda internamente una lista por instancia de pares `(contacto, mensaje)`.
- Expone una propiedad `mensajes -> tuple[tuple[str, str], ...]`.
- No imprime ni envía mensajes reales.

Cambia la dependencia desde el punto de entrada. No modifiques `ServicioEnvios`. No se trata de crear pruebas unitarias, sino de inspeccionar otra implementación del mismo contrato.

### Etapa F. Consultas que no alteran el negocio

**Implementas tú — reto 5: reportes**

1. `filtrar_por_estado` debe validar el estado y usar una **list comprehension**.
2. `resumen_por_estado` debe usar una **dict comprehension** y contener siempre los cuatro estados.
3. Recorre `resumen.items()` desempaquetando `(estado, cantidad)`.
4. Demuestra que consultar y reportar no cambia el seguimiento ni agrega mensajes.

**Hace el docente**

Muestra una captura específica en el punto de entrada:

- `try`: intento de operación.
- `except TransicionInvalida`: informa el rechazo real.
- `else`: confirma éxito solo si no ocurrió una excepción.
- `finally`: anuncia el cierre del intento, no una limpieza de archivos inexistentes.

**Implementas tú**

Completa los escenarios de duplicado, código inexistente y transición prohibida. No uses `except: pass` ni una captura general de `Exception` que esconda defectos del programa.

---

## 6. Historia de aceptación final

Usa un remitente de demostración: identificación `C001`, nombre `Ana`, contacto `ana@ejemplo.test`. Los contactos son ficticios; no se envía nada por Internet.

| Código | Tipo | Destino de demostración | Peso |
|---|---|---|---:|
| E001 | Local | Luis - Sede centro | 1 000 |
| E002 | Nacional | Sara - Sede norte | 1 001 |
| E003 | Local | Eva - Sede sur | 1 |

Con el notificador en memoria:

1. Registra los tres envíos.
2. Despacha y entrega `E001`.
3. Cancela `E002`.
4. Despacha `E003`.
5. Consulta el resumen y los mensajes acumulados.

Resultado obligatorio:

```text
E001 | entregado | 6000 | 3
E002 | cancelado | 14000 | 2
E003 | en_ruta | 6000 | 2
{'registrado': 0, 'en_ruta': 1, 'entregado': 1, 'cancelado': 1}
Mensajes: 7
```

Las cuatro columnas representan código, estado, tarifa y cantidad de entradas del historial. Las fechas reales no se comparan como texto fijo.

Después intenta cancelar `E003`. Debe informar `TransicionInvalida` y conservar estado `en_ruta`, dos entradas y siete mensajes. Buscar `NO-EXISTE` debe lanzar `EnvioNoEncontrado`; agregar otro `E001` debe lanzar `CodigoDuplicado` sin reemplazar el original.

### Lista de verificación manual

| Comprobación | Resultado esperado |
|---|---|
| Peso `0`, `-1`, `1.5`, `"1000"` o `True` | `DatoInvalido` |
| Texto vacío o solo espacios en un dato obligatorio | `DatoInvalido` |
| Entregar antes de despachar | `TransicionInvalida`; no cambia nada |
| Cancelar después de despachar | `TransicionInvalida`; no cambia nada |
| Repetir despacho, entrega o cancelación | `TransicionInvalida`; no cambia nada |
| Operar sobre un estado terminal | `TransicionInvalida`; no cambia nada |
| Filtrar `registrado` cuando no hay coincidencias | Lista vacía |
| Filtrar `perdido` | `DatoInvalido` |
| Modificar la lista recibida de `listar()` | No elimina elementos del repositorio |
| Obtener `historial` | Tupla de registros congelados |
| Cambiar notificador en el punto de entrada | No cambia el servicio ni el dominio |

## 7. Qué debes entregar y explicar

- Análisis inicial y ajustes posteriores: requerimientos, responsabilidades, relaciones y estados.
- Paquete organizado, ejecutable con el comando indicado.
- Retos 1–5 completos, sin `TODO` en las funcionalidades obligatorias.
- Evidencia de ejecución del cierre de cada clase y de la historia final.
- Explicación breve de:
  - una decisión de encapsulamiento;
  - una relación de herencia frente a una asociación;
  - la diferencia entre contrato nominal y estructural;
  - la diferencia entre inyectar una dependencia y depender de una abstracción;
  - el uso concreto de lista, tupla y diccionario;
  - por qué el error se informa sin modificar parcialmente el negocio.

Se revisará **comprensión del problema, cumplimiento de reglas, diseño POO, modularidad, integración y justificación individual**. No basta con obtener la salida correcta mediante datos escritos directamente en los reportes.
