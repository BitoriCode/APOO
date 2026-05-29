# Ejercicio: Cuenta de Ahorros

---

## Descripción del problema

Un banco pequeño quiere informatizar la gestión de sus cuentas de ahorros. Un analista entrevistó al gerente y tomó estas notas:

> *"Cada cuenta tiene un titular, un saldo y un número de cuenta que el propio sistema debe generar de forma automática y única. Los clientes pueden consignar o retirar dinero, pero no podemos permitir montos negativos ni retiros que dejen el saldo en rojo. El banco también necesita saber, en cualquier momento, el total de dinero depositado entre todas las cuentas. Y por supuesto, debe poderse consultar una cuenta específica dado su número."*

---

## Análisis del problema

Antes de escribir cualquier línea de código, responde estas preguntas:

1. ¿Qué **entidades** puedes identificar en la descripción?
2. ¿Qué **información** debe guardar cada entidad? ¿Qué datos deberían ser privados y por qué?
3. ¿Qué **acciones o consultas** se mencionan? ¿A qué entidad le corresponde cada una?
4. ¿Qué **reglas de negocio** deben validarse antes de realizar una operación?
5. ¿Cómo se **relacionan** las entidades? ¿Quién conoce a quién?

---

## Tu tarea

### Parte 1 — Diagrama de clases

Propone el diagrama de clases  que represente las entidades con sus atributos, métodos y la relación entre ellas.


---

### Parte 2 — Implementación

Implementa en Python las clases que identificaste en tu análisis. Usa type hints y aplica encapsulamiento donde corresponda.


---

## Casos de prueba

El siguiente código debe ejecutarse **sin errores** y producir las salidas indicadas en los comentarios:

```python
# --- Banco y apertura de cuentas ---
banco = Banco(nombre="Banco del Norte")

c1 = banco.abrir_cuenta(titular="Ana Gómez", deposito_inicial=500_000.0)
c2 = banco.abrir_cuenta(titular="Luis Pérez", deposito_inicial=1_200_000.0)

print(c1.obtener_titular())   # Ana Gómez
print(c1.obtener_saldo())     # 500000.0
print(c1.obtener_numero())    # CTA-001  (o el formato que elijas, debe ser único)

# --- Operaciones sobre cuenta ---
c1.depositar(200_000.0)
print(c1.obtener_saldo())     # 700000.0

c1.retirar(100_000.0)
print(c1.obtener_saldo())     # 600000.0

print(c1.resumen()) # Ejemplo: "Cuenta CTA-001 | Titular: Ana Gómez | Saldo: $600000.00"

# --- Búsqueda ---
encontrada = banco.buscar_cuenta(c2.obtener_numero())
print(encontrada.obtener_titular())      # Luis Pérez

no_existe = banco.buscar_cuenta("CTA-999")
print(no_existe)               # None

# --- Total depositado ---
print(banco.total_depositado()) # 1800000.0  (600000 + 1200000)

# --- Validaciones (deben lanzar ValueError) ---
try:
    c2.depositar(0)
except ValueError as e:
    print("Error esperado:", e)

try:
    c2.retirar(5_000_000.0)    # saldo insuficiente
except ValueError as e:
    print("Error esperado:", e)

# --- Los atributos privados no deben modificarse directamente ---
# c1._saldo = 999_999  <-- esto NO debe usarse; usa depositar() y retirar()
```

---

> **Nota:** Propone la solución completa desde cero. El número de cuenta puede generarse con un contador de clase, un UUID corto u otro mecanismo, siempre que sea único dentro del banco.
