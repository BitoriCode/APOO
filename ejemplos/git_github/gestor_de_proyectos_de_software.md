# Ejercicio — Gestor de Proyectos de Software

---

## Enunciado

Un equipo de desarrollo quiere llevar el registro de sus proyectos de software. El líder técnico te dice:

> *"Tenemos varios proyectos activos. Cada proyecto tiene un nombre, un lenguaje de programación principal y una lista de colaboradores que trabajan en él. Los colaboradores tienen un nombre de usuario y un correo. Lo que necesitamos es poder agregar colaboradores a un proyecto, listar quiénes participan en él, y saber si una persona ya hace parte del proyecto o no, buscando por su nombre de usuario. También queremos tener una lista general de todos los proyectos registrados y poder buscar un proyecto por su nombre."*

Implementa en Python una solución orientada a objetos para este problema. Usa **type hints** y **dataclasses** donde consideres apropiado.

---

## Casos de prueba

El siguiente código debe ejecutarse **sin errores** y producir las salidas indicadas:

```python
# Colaboradores
ana   = Colaborador(username="ana_dev", email="ana@mail.com")
luis  = Colaborador(username="luis99",  email="luis@mail.com")
sofia = Colaborador(username="sofiaml", email="sofia@mail.com")

# Proyectos
p1 = Proyecto(nombre="InventarioApp", lenguaje="Python")
p1.agregar_colaborador(ana)
p1.agregar_colaborador(luis)
p1.agregar_colaborador(ana)   # aviso: ya existe

p2 = Proyecto(nombre="WebStore", lenguaje="JavaScript")
p2.agregar_colaborador(sofia)

# __str__
print(p1)  # Proyecto: InventarioApp [Python] — 2 colaborador(es)
print(p2)  # Proyecto: WebStore [JavaScript] — 1 colaborador(es)

# tiene_colaborador
print(p1.tiene_colaborador("ana_dev"))  # True
print(p1.tiene_colaborador("sofiaml"))  # False

# Gestor
gestor = GestorProyectos()
gestor.registrar_proyecto(p1)
gestor.registrar_proyecto(p2)
gestor.registrar_proyecto(p1)  # aviso: ya existe

encontrado = gestor.buscar_proyecto("WebStore")
print(encontrado)  # Proyecto: WebStore [JavaScript] — 1 colaborador(es)

no_existe = gestor.buscar_proyecto("OtroProyecto")
print(no_existe)   # None

print(len(gestor.listar_proyectos()))  # 2
```
