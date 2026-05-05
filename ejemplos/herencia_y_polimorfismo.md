## 1) Herencia simple + sobrescritura básica

```python
from typing import override

# Clase base: modela un vehículo genérico
class Vehiculo:
    marca: str  # pista de tipo para el atributo de instancia

    def __init__(self, marca: str) -> None:
        # Inicializamos el estado común a todos los vehículos
        self.marca = marca

    def arrancar(self) -> str:
        # Método que las subclases pueden REEMPLAZAR (sobrescribir)
        return f"{self.marca}: motor genérico encendido"


# Subclase: especializa Vehiculo como "Auto"
class Auto(Vehiculo):
    modelo: str

    def __init__(self, marca: str, modelo: str) -> None:
        # Llamamos al constructor de la clase base para reutilizar su inicialización
        super().__init__(marca)
        self.modelo = modelo

    @override
    def arrancar(self) -> str:
        # SOBRESCRITURA: cambiamos el comportamiento para el caso "Auto"
        return f"{self.marca} {self.modelo}: motor a gasolina encendido"


# --- Comprobaciones básicas ---
v = Vehiculo("Genérico")
a = Auto("Toyota", "Corolla")

# Llamadas a métodos para ver la diferencia entre base y subclase
print(v.arrancar())  # Esperado: "Genérico: motor genérico encendido"
print(a.arrancar())  # Esperado: "Toyota Corolla: motor a gasolina encendido"
```

---

## 2) Sobrescritura **extendiendo** el comportamiento con `super()`

```python
from typing import override

# Clase base: un usuario genérico del sistema
class Usuario:
    nombre: str

    def __init__(self, nombre: str) -> None:
        self.nombre = nombre

    def saludo(self) -> str:
        # Mensaje genérico; las subclases podrían ampliarlo
        return f"Hola, soy {self.nombre}."


# Subclase: un profesor es un tipo de usuario con área de experticia
class Profesor(Usuario):
    area: str

    def __init__(self, nombre: str, area: str) -> None:
        # Reutilizamos inicialización de Usuario (nombre)
        super().__init__(nombre)
        self.area = area

    @override
    def saludo(self) -> str:
        # Usamos super() para recuperar el saludo base y LO EXTENDEMOS
        base: str = super().saludo()
        return f"{base} Imparto clases en {self.area}."


# --- Comprobaciones básicas ---
u = Usuario("Ana")
p = Profesor("Carlos", "POO")

# Mismo método "saludo", pero en la subclase se añade información extra
print(u.saludo())  # Esperado: "Hola, soy Ana."
print(p.saludo())  # Esperado: "Hola, soy Carlos. Imparto clases en POO."
```

---

## 3) Cadena de herencia (A → B → C) con sobrescrituras encadenadas

```python
from typing import override

# Clase base: representa un archivo con una ruta
class Archivo:
    ruta: str

    def __init__(self, ruta: str) -> None:
        self.ruta = ruta

    def abrir(self) -> str:
        # Acción general que las subclases complementarán
        return f"Abrir {self.ruta}"


# Subclase intermedia: añade verificación de permisos
class ArchivoSeguro(Archivo):
    @override
    def abrir(self) -> str:
        # Recupera el comportamiento base y lo complementa
        base: str = super().abrir()
        return f"{base} | Verificar permisos"


# Subclase más específica: añade auditoría además de permisos
class ArchivoAuditable(ArchivoSeguro):
    @override
    def abrir(self) -> str:
        # Encadena nuevamente: mantiene permisos y agrega auditoría
        base: str = super().abrir()
        return f"{base} | Registrar auditoría"


# --- Comprobaciones básicas ---
a1 = Archivo("data.csv")
a2 = ArchivoSeguro("secreto.txt")
a3 = ArchivoAuditable("transacciones.log")

# Observa cómo se va "acumulando" el comportamiento en la cadena de herencia
print(a1.abrir())  # Esperado: "Abrir data.csv"
print(a2.abrir())  # Esperado: "Abrir secreto.txt | Verificar permisos"
print(a3.abrir())  # Esperado: "Abrir transacciones.log | Verificar permisos | Registrar auditoría"
```

---

## 4) Herencia **múltiple** con mixins y `super()` cooperativo

```python
from typing import override

# Clase base: define una operación genérica
class BaseInicializable:
    def inicializar(self) -> str:
        return "Base"


# Mixin A: extiende la inicialización y encadena con super()
class MixinA(BaseInicializable):
    @override
    def inicializar(self) -> str:
        # Llama a la siguiente clase en el MRO (orden de resolución de métodos)
        base: str = super().inicializar()
        return f"{base} -> A"


# Mixin B: también extiende la inicialización y encadena con super()
class MixinB(BaseInicializable):
    @override
    def inicializar(self) -> str:
        base: str = super().inicializar()
        return f"{base} -> B"


# Clase final con herencia múltiple.
# El ORDEN importa: MRO será Final -> MixinA -> MixinB -> BaseInicializable -> object
class Final(MixinA, MixinB, BaseInicializable):
    @override
    def inicializar(self) -> str:
        # Encadena toda la línea de herencia respetando el MRO
        cadena: str = super().inicializar()
        return f"{cadena} -> Final"


# --- Comprobaciones básicas ---
f = Final()
# Se observa cómo cada mixin añade su parte en el orden del MRO
print(f.inicializar())  # Esperado: "Base -> B -> A -> Final"
```

---

## 5) Sobrescritura de método especial `__str__` (representación textual)

```python
from typing import override

# Clase base: persona con representación de texto estándar
class Persona:
    nombre: str
    edad: int

    def __init__(self, nombre: str, edad: int) -> None:
        self.nombre = nombre
        self.edad = edad

    def __str__(self) -> str:
        # Representación genérica de Persona
        return f"{self.nombre} ({self.edad} años)"


# Subclase: estudiante que mejora la representación textual
class Estudiante(Persona):
    programa: str

    def __init__(self, nombre: str, edad: int, programa: str) -> None:
        # Reutiliza la inicialización de Persona
        super().__init__(nombre, edad)
        self.programa = programa

    @override
    def __str__(self) -> str:
        # Usa la representación base y añade su dato específico
        base: str = super().__str__()
        return f"{base} — Estudiante de {self.programa}"


# --- Comprobación básica ---
e = Estudiante("Laura", 19, "Ingeniería de Sistemas")
# print(e) llama internamente a e.__str__()
print(e)  # Esperado: "Laura (19 años) — Estudiante de Ingeniería de Sistemas"
```
