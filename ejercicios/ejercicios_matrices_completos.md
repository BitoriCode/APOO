# Repaso para Examen Final: Matrices en Python

## Instrucciones generales

- Los ejercicios deben resolverse aplicando **programación modular**. Usted decide cómo organizar el código en módulos o bloques lógicos.
- **Restricción:** No usar `sum()`, `min()`, `max()`, `sort()`, `sorted()` ni librerías externas. La única función especial permitida es `len()`.
- Los ciclos `for` deben escribirse usando `range`.
- El programa debe mostrar todos los resultados solicitados con un mensaje claro.

---

## Ejercicio 1: Temperaturas Mensuales en Ciudades Colombianas

El IDEAM registró la temperatura promedio mensual (en °C) de cinco ciudades colombianas durante el primer semestre del año. Cada fila de la matriz representa una ciudad y cada columna corresponde a un mes. A continuación se presentan los datos junto con las listas auxiliares `ciudades` y `meses`:

```python
ciudades = ["Bogotá", "Medellín", "Cali", "Bucaramanga", "Cartagena"]
meses    = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio"]

temperaturas = [
    [14, 15, 16, 15, 13, 10],  # Bogotá
    [22, 23, 24, 22, 21, 20],  # Medellín
    [24, 25, 26, 25, 24, 23],  # Cali
    [27, 28, 29, 30, 28, 26],  # Bucaramanga
    [29, 29, 30, 30, 29, 28],  # Cartagena
]
```

* Calcule la temperatura **promedio** de una ciudad a lo largo de los seis meses.
* Determine cuál es la ciudad con la **mayor temperatura** en un mes dado.
* Identifique las ciudades cuya temperatura **nunca varió más de 1 °C** entre dos meses consecutivos.
* Encuentre el mes en el que se presentó la **mayor diferencia** entre la temperatura más alta y la temperatura más baja entre todas las ciudades.
* Calcule la temperatura **promedio entre todas las ciudades** para un mes dado.

---

## Ejercicio 2: Ventas Semanales en una Cadena de Droguerías

Una cadena de droguerías en Colombia registró las unidades vendidas de cinco medicamentos de venta libre durante cuatro semanas. Cada fila representa un medicamento y cada columna una semana. Los datos se presentan a continuación:

```python
medicamentos = ["Acetaminofén", "Ibuprofeno", "Loratadina", "Omeprazol", "Amoxicilina"]
semanas      = ["Semana 1", "Semana 2", "Semana 3", "Semana 4"]

ventas = [
    [350, 380, 410, 430],  # Acetaminofén
    [200, 185, 220, 210],  # Ibuprofeno
    [150, 165, 158, 172],  # Loratadina
    [180, 195, 188, 205],  # Omeprazol
    [120, 108, 135, 125],  # Amoxicilina
]
```

* Determine en qué **semana** un medicamento dado registró sus **mayores ventas**.
* Identifique qué medicamentos tuvieron ventas que **crecieron cada semana sin excepción** (nunca disminuyeron de una semana a la siguiente).
* Calcule el **promedio de unidades vendidas** entre todos los medicamentos en una semana dada.
* Determine cuál fue la semana con ventas **más equilibradas**, es decir, aquella con la **menor diferencia** entre el medicamento más vendido y el menos vendido.
* Identifique qué medicamento tuvo el **mayor incremento** de ventas entre la primera y la última semana.

---
