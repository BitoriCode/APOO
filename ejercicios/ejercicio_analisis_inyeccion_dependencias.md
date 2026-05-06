# Ejercicio de análisis — **Sistema de Notificaciones para EduTrack**

> **Duración estimada:** 60 minutos  
> **Temas que practica:** Análisis del problema · `Protocol` · Inyección de dependencias por constructor · Punto de ensamblaje  
> **Prerrequisito:** Nota `inyeccion_de_dependencias_y_DIP.md`

---

## Descripción del problema

*EduTrack* es una plataforma de aprendizaje en línea. Cuando un estudiante **entrega una tarea**, el sistema debe **notificarle** que la entrega fue recibida correctamente.

El equipo de desarrollo tiene el siguiente código funcionando:

```python
class GestorEntregas:
    def registrar_entrega(self, estudiante: str, tarea: str) -> None:
        # ... lógica de almacenamiento ...
        notificador = NotificadorEmail()
        notificador.enviar(
            destinatario=estudiante,
            mensaje=f"Tu entrega de '{tarea}' fue recibida."
        )
```

El sistema lleva tres meses en producción cuando llegan dos solicitudes al mismo tiempo:

1. El área de sistemas pide que **en entornos de prueba** no se envíen correos reales, sino que los mensajes se impriman en consola.
2. El área de producto pide agregar soporte para **notificaciones push** (además del correo) sin modificar la lógica de entregas.

El equipo intenta atender ambas solicitudes y se da cuenta de que, con el diseño actual, **cualquier cambio obliga a tocar `GestorEntregas`**.

---

## Tu tarea

Rediseña el sistema usando **inyección de dependencias** y un **`Protocol`** como abstracción, de modo que `GestorEntregas` nunca necesite ser modificado para cambiar o agregar canales de notificación.

---

## Parte 1 — Análisis del problema (sin código) 

Responde las siguientes preguntas **antes de escribir cualquier línea de código**. Puedes escribir tus respuestas directamente aquí o en papel.

**1.1 — Identifica la dependencia**

> ¿Qué objeto crea `GestorEntregas` internamente?
> ¿Por qué eso es un problema de diseño?



**1.2 — Identifica la variabilidad**

> ¿Qué parte del sistema cambia o podría cambiar en el futuro?
> ¿Qué parte debería permanecer estable?



**1.3 — Define el contrato**

> ¿Qué operación(es) comparten todos los canales de notificación
> (email, consola, push)?
> Escribe en lenguaje natural (no en código) qué debe poder hacer
> cualquier notificador.


---

## Parte 2 — Diseño (diagrama de clases) 

Con base en tu análisis, dibuja (en papel o escribe en Mermaid) el diagrama de clases de tu solución. Debe incluir:

- El `Protocol` que representa el contrato del notificador.
- Al menos **tres** implementaciones concretas del notificador.
- La clase `GestorEntregas` y cómo se relaciona con el `Protocol`.
- Las flechas correctas: implementación (`<|..`) y dependencia inyectada (`o-->`).

> **Criterio de aceptación del diagrama:** ¿Puedes agregar un cuarto notificador
> sin dibujar ninguna flecha nueva desde `GestorEntregas`?

---

## Parte 3 — Implementación

Crea un único archivo `apellido_nombre_edutrack.py` con todo el código.

### 3.1 — Define el `Protocol`

- Nombre sugerido: `Notificador`
- Método requerido: recibe `destinatario: str` y `mensaje: str`, no retorna nada.

### 3.2 — Implementa los tres notificadores

| Clase | Comportamiento |
|---|---|
| `NotificadorEmail` | Imprime: `[Email → <destinatario>] <mensaje>` |
| `NotificadorConsola` | Imprime: `[Consola] <destinatario>: <mensaje>` |
| `NotificadorPush` | Imprime: `[Push → <destinatario>] 🔔 <mensaje>` |

> Ninguno de los tres debe heredar de ninguna clase.
> Solo deben tener el método que exige el `Protocol`.

### 3.3 — Rediseña `GestorEntregas`

- Constructor: recibe un `Notificador` y lo guarda en un atributo privado.
- Método `registrar_entrega(self, estudiante: str, tarea: str) -> None`:
  usa el notificador inyectado para enviar el mensaje de confirmación.
- **No** debe crear ningún notificador internamente.
- **No** debe contener condicionales del tipo `if tipo == "email"`.

### 3.4 — Escribe el punto de ensamblaje

Al final del archivo, en el bloque `if __name__ == "__main__":`, crea al menos
**tres** instancias de `GestorEntregas`, cada una con un notificador distinto,
y registra una entrega con cada una.

**Salida esperada** (puede variar según tus mensajes exactos):

```
[Email → ana@edu.co] Tu entrega de 'Taller 3' fue recibida.
[Consola] ana@edu.co: Tu entrega de 'Taller 3' fue recibida.
[Push → ana@edu.co] 🔔 Tu entrega de 'Taller 3' fue recibida.
```

---

## Parte 4 — Reflexión escrita

Responde las siguientes preguntas después de terminar la implementación.

**4.1**
> El equipo ahora recibe una nueva solicitud: integrar notificaciones por **WhatsApp**.
> ¿Cuántas clases existentes tienes que modificar para implementarlo?
> ¿Qué clase(s) debes crear?



**4.2**
> ¿Dónde en tu código se decide **qué notificador concreto** se usa?
> ¿Por qué ese lugar es el correcto?



**4.3**
> Compara tu solución con el código original de `GestorEntregas`.
> ¿Qué principio de diseño se aplica aquí? Explícalo con tus palabras.



---

## Criterios de evaluación

| Criterio | Puntaje |
|---|---|
| Análisis escrito (Parte 1): identifica correctamente la dependencia y la variabilidad | 20 % |
| Diagrama de clases correcto con flechas apropiadas | 20 % |
| `Protocol` bien definido con la firma correcta | 15 % |
| Tres implementaciones concretas que cumplen el contrato | 20 % |
| `GestorEntregas` rediseñado sin crear dependencias internamente | 15 % |
| Reflexión escrita (Parte 4) con argumentos claros | 10 % |

---

## Reto adicional

Si terminas antes de tiempo, implementa un **cuarto notificador** llamado
`NotificadorSilencioso` que no imprime nada (útil para pruebas automatizadas).

Úsalo en el `main` junto a los demás y verifica que `GestorEntregas` no
necesitó ningún cambio para aceptarlo.
