
## CASO DE ESTUDIO 1: PLATAFORMA DE EVENTOS (ENUNCIADO)

Se quiere construir una aplicación llamada **Plataforma de Eventos** que permita administrar la organización de eventos y la inscripción de participantes a dichos eventos. La plataforma estará destinada a gestionar conferencias, conciertos o talleres, donde existen **organizadores** que registran eventos y **usuarios** que se inscriben como participantes.

La plataforma debe funcionar en línea y permitir gestionar **múltiples eventos**. Cada evento debe registrarse con información como **fecha y hora**, **lugar**, y un **cupo máximo** de asistentes. Los usuarios podrán inscribirse a eventos **mientras haya cupo disponible**. La plataforma debe permitir **cancelar inscripciones** y también **cancelar eventos** si es necesario. Cuando un evento sea cancelado, debe quedar registro de esa cancelación y la plataforma debe permitir identificar que dicho evento ya no está activo para nuevas inscripciones.

La aplicación también debe permitir realizar consultas como:

* ver el **listado de participantes inscritos** en un evento,
* buscar eventos por ciertos criterios (por ejemplo: por nombre/título, por fecha o por lugar).

### Datos mínimos a manejar

**De cada evento se conoce:**

* ID o código del evento (único en la plataforma).
* Nombre o título del evento.
* Fecha y hora (inicio).
* Lugar (ubicación).
* Cupo máximo.
* Estado del evento (por ejemplo: activo o cancelado).

**De cada participante se conoce:**

* Identificación única (por ejemplo: correo o un código asignado).
* Nombre.
* Datos de contacto (correo y/o teléfono).

**De cada inscripción se conoce:**

* Evento asociado.
* Participante asociado.
* Fecha de inscripción.
* Estado de la inscripción (por ejemplo: activa o cancelada).

### Restricciones y consideraciones

* No debe haber dos eventos con el mismo ID.
* No debe haber dos participantes con la misma identificación.
* No se debe permitir inscribir participantes en eventos cancelados.
* No se deben permitir inscripciones si el evento ya alcanzó su cupo máximo.
* Una inscripción cancelada no debe eliminarse: debe conservarse el registro con estado “cancelada”.

---

## CASO DE ESTUDIO 2: ALMACÉN DE PRODUCTOS (LOGÍSTICA) (ENUNCIADO)

Se quiere construir una aplicación llamada **Almacén de Productos** que permita administrar el inventario de productos en un almacén logístico. El sistema manejará un catálogo de productos disponibles, registrará las operaciones de **entrada** y **salida** de mercancías y permitirá consultas sobre el estado del inventario. El objetivo es tener control sobre cuántas unidades de cada producto hay en stock y llevar un **historial de movimientos de inventario**.

El sistema debe permitir:

* agregar productos nuevos al catálogo,
* eliminar productos que ya no se manejan (según reglas),
* registrar entradas de stock (cuando llegan unidades),
* registrar salidas de stock (cuando se despachan unidades),
* consultar la cantidad disponible (stock) y revisar movimientos registrados.

### Datos mínimos a manejar

**De cada producto se conoce:**

* Código único del producto (por ejemplo, SKU). No pueden existir dos productos con el mismo código.
* Nombre o descripción.
* Cantidad en stock (unidades disponibles).
* Precio de costo (valor unitario de adquisición).
* Precio de venta (valor unitario estimado de salida/venta).
* Estado del producto (por ejemplo: activo/inactivo) si decides manejarlo.

**De cada movimiento de inventario se conoce:**

* Producto asociado.
* Tipo de movimiento: entrada o salida.
* Fecha del movimiento.
* Cantidad de unidades involucradas.
* Observación o motivo (opcional pero recomendado).

### Restricciones y consideraciones

* El stock **no puede ser negativo**: no se puede registrar una salida mayor al stock disponible.
* El stock de un producto **solo** debe cambiar cuando se registren entradas o salidas (no se modifica “a mano”).
* No debe haber dos productos con el mismo código.
* Si un producto se elimina del catálogo, define una regla clara (por ejemplo: solo permitir eliminar si stock = 0, o marcar como inactivo).
