### Contexto y Datos Iniciales

Una empresa minorista en Colombia ha recopilado las ventas trimestrales de sus tiendas en varias ciudades colombianas durante el año 2025. Los datos están en millones de pesos colombianos (COP). La siguiente matriz de Python (**ventas**) presenta las ventas por trimestre de cada ciudad, y la lista **ciudades** actúa como referencia para el nombre de cada ciudad (cada fila de la matriz corresponde a la ciudad en la misma posición en la lista). Cada columna de la matriz corresponde a un trimestre del año (T1, T2, T3, T4 respectivamente).

```python
# Matriz de ventas 2025 (en millones de COP) por ciudad [T1, T2, T3, T4] 
ventas = [ 
    [200, 180, 210, 190],  # Bogotá 
    [150, 130, 160, 160],  # Medellín 
    [130, 120, 110, 115],  # Cali 
    [90,  100,  95, 120],  # Barranquilla 
    [80,   85,  70,  75]   # Cartagena 
] 
 
# Lista de nombres de ciudades correspondientes a cada fila de la matriz 
ciudades = ["Bogotá", "Medellín", "Cali", "Barranquilla", "Cartagena"]
```

### Preguntas

1. Implemente una función que obtenga el **total anual de cada ciudad** y el **total acumulado** de todas las ventas.

2. Implemente una función que determine, para una **ciudad indicada**, el **menor valor trimestral** y el **trimestre** en el que ocurre.

3. Implemente una función que calcule el **promedio del primer semestre (T1–T2)** y el **promedio del segundo semestre (T3–T4)**, e **indique** en cuál semestre el promedio fue mayor o si **son iguales**.

4. Implemente una función que identifique **el nombre de la ciudad** con el **mayor total anual** y el **monto correspondiente**.
