# Ejercicio 25 — Plataforma de Eventos Universitarios

> **Tipo:** Ejercicio independiente de repaso integral.
> **Objetivo:** Analizar un problema, identificar entidades y relaciones, y construir una solución usando `@dataclass`, clases normales, `dict`, `list` y list comprehension.

---

## Descripción del problema

> *La Universidad quiere una plataforma sencilla para gestionar sus eventos culturales y deportivos. Los eventos tienen un nombre, un tipo (puede ser: concierto, deportivo o académico), una fecha, un lugar, un cupo máximo de asistentes y un precio de boleta (que puede ser cero si es gratuito). Los estudiantes se registran en la plataforma con su código, nombre y programa académico.*
>
> *Un estudiante puede inscribirse a un evento siempre que haya cupo disponible; si el evento está lleno, el sistema debe informarlo. Tampoco se permite que un mismo estudiante se inscriba dos veces al mismo evento. La plataforma debe poder listar los eventos disponibles por tipo, saber quiénes están inscritos en un evento específico, saber a qué eventos se inscribió un estudiante y calcular el total de ingresos acumulados por todas las inscripciones.*

---

## Análisis previo

Antes de escribir código, responde estas preguntas:

1. ¿Qué **entidades** identifica el texto? Enumera los sustantivos del dominio.
2. ¿Qué **información** describe a cada entidad? (atributos)
3. ¿Qué **comportamientos o consultas** se mencionan, y a qué entidad le corresponde cada uno?
4. ¿Cómo se relacionan un estudiante y un evento? ¿Es una relación 1:N o N:M? ¿Cómo la modelas?
5. ¿Qué estructura de datos usas para guardar estudiantes — `dict` o `list`? ¿Y para eventos? Justifica.
6. ¿Dónde ubicas la validación de cupo disponible y la de inscripción duplicada?
7. ¿Qué clases tienen sentido como `@dataclass`? ¿Cuál no, y por qué?

---

## Tu tarea

### Parte 1 — Diagrama de clases

Propone el diagrama de clases con todas las entidades, sus atributos, métodos y las relaciones entre ellas. Incluye multiplicidades.

### Parte 2 — Implementación

Implementa en Python las clases que diseñaste. Usa type hints y valida las reglas de negocio con `ValueError`.

**Restricciones:**
- Sin herencia.
- Atributos públicos excepto contadores internos (prefijo `_`).
- Usa `@dataclass` con `__post_init__` donde corresponda.
- Las consultas que filtran listas deben usar **list comprehension**.

---

## Casos de prueba

```python
# --- Datos de prueba ---
e1 = Estudiante(codigo="001", nombre="Ana Torres", programa="Ingeniería de Sistemas")
e2 = Estudiante(codigo="002", nombre="Carlos Rúa", programa="Medicina")
e3 = Estudiante(codigo="003", nombre="Pedro Salas", programa="Derecho")
e4 = Estudiante(codigo="004", nombre="Laura Díaz", programa="Arquitectura")

ev1 = Evento(id=1, nombre="Rock Fest UdeM", tipo="concierto",
             fecha="2026-10-15", lugar="Auditorio", capacidad=3, precio_boleta=15000.0)
ev2 = Evento(id=2, nombre="Torneo de Fútbol", tipo="deportivo",
             fecha="2026-10-20", lugar="Cancha Central", capacidad=50, precio_boleta=0.0)
ev3 = Evento(id=3, nombre="Congreso de IA", tipo="academico",
             fecha="2026-11-05", lugar="Sala A", capacidad=30, precio_boleta=25000.0)

plataforma = Plataforma()
for e in [e1, e2, e3, e4]:
    plataforma.registrar_estudiante(e)
for ev in [ev1, ev2, ev3]:
    plataforma.agregar_evento(ev)

# --- Inscripciones ---
i1 = plataforma.inscribir("001", 1)   # Ana → Rock Fest
i2 = plataforma.inscribir("002", 1)   # Carlos → Rock Fest
i3 = plataforma.inscribir("001", 3)   # Ana → Congreso

print(i1.id)                            # 1
print(plataforma.cupos_disponibles(1))  # 1  (capacidad 3 − 2 inscritos)

# --- Consultas ---
conciertos = plataforma.eventos_por_tipo("concierto")
print(len(conciertos))                  # 1

inscritos_rock = plataforma.inscritos_en_evento(1)
print(len(inscritos_rock))             # 2
print(inscritos_rock[0].nombre)       # Ana Torres

eventos_ana = plataforma.eventos_de_estudiante("001")
print(len(eventos_ana))                # 2

print(plataforma.ingresos_totales())   # 55000.0

# --- Errores esperados ---
try:
    plataforma.inscribir("001", 1)     # inscripción duplicada
except ValueError as e:
    print("Error esperado:", e)

plataforma.inscribir("003", 1)         # Pedro toma el último cupo
print(plataforma.cupos_disponibles(1)) # 0

try:
    plataforma.inscribir("004", 1)     # sin cupo
except ValueError as e:
    print("Error esperado:", e)

try:
    Evento(id=99, nombre="Test", tipo="INVALIDO",
           fecha="2026-12-01", lugar="Sala", capacidad=10, precio_boleta=0.0)
except ValueError as e:
    print("Error esperado:", e)
```
