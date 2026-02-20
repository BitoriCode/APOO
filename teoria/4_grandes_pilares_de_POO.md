
# Los cuatro pilares de la Programación Orientada a Objetos (POO)

La **Programación Orientada a Objetos (POO)** es un paradigma de desarrollo de software que se basa en la idea de organizar el código en torno a **objetos** y sus interacciones. Entre los conceptos fundamentales de la POO se encuentran cuatro pilares principales: **encapsulamiento**, **abstracción**, **herencia** y **polimorfismo**. Estos pilares trabajan en conjunto para producir código más modular, reutilizable y fácil de mantener. A continuación se explora cada pilar en detalle, proporcionando (1) una definición conceptual general, (2) ejemplos o analogías de la vida real, (3) su propósito y beneficios dentro del paradigma, y (4) un ejemplo de implementación en Python para ilustrar cómo este lenguaje los maneja.

## Encapsulamiento

### Definición conceptual

El **encapsulamiento** (o encapsulación) consiste en *ocultar los detalles internos de un objeto y exponer solo una interfaz limitada para su uso*. En términos prácticos, implica **agrupar los datos y los métodos** que operan sobre esos datos dentro de una clase u objeto, **restringiendo el acceso directo** a los datos desde el exterior. Dicho de otra manera, encapsular significa que los atributos o métodos internos de una clase **no pueden ser accedidos ni modificados desde fuera**; solo el propio objeto (o clase) puede acceder y manipular su estado interno. Esto logra una especie de “caja negra” en la que el objeto controla cómo se usan sus datos.

*Ilustración del concepto de encapsulamiento:* el objeto (círculo celeste) expone solo ciertos métodos públicos (cuadro verde) a otras partes del programa, mientras mantiene sus datos privados ocultos (cuadro anaranjado). La interfaz de interacción (flecha gris) representa cómo otros objetos pueden usar los métodos públicos sin acceder directamente a los detalles internos. Este mecanismo protege el estado interno del objeto y define límites claros entre su **interfaz pública** y su **implementación privada**.

### Analogía en la vida real

Para entender el encapsulamiento, imaginemos un **taller de carpintería**. El taller (pensado como una clase u objeto) tiene información interna, por ejemplo una lista de precios de materiales y procesos. Esa tarifa interna la utilizan los empleados del taller para elaborar presupuestos, pero **está oculta para los clientes externos** – ninguno de los clientes puede ver ni modificar esos precios internos. En su lugar, el taller ofrece un servicio público: los clientes *solicitan un presupuesto* y el taller, detrás de escena, usa sus datos internos (precios, costos, etc.) para calcularlo y entregarlo. Asimismo, un cliente no entra al taller a manipular las máquinas directamente; **solo puede interactuar con el taller mediante las operaciones previstas (pedir un presupuesto, consultar catálogos, etc.)**, mientras que las herramientas y procesos internos permanecen encapsulados y solo accesibles por el propio taller. Esta analogía refleja cómo en POO una clase protege sus datos y operaciones internas, exponiendo solo aquello necesario para el resto del programa.

### Propósito y beneficios

El encapsulamiento cumple varios propósitos importantes en la POO, aportando numerosos beneficios:

* **Protección e integridad de datos:** Al ocultar los atributos (declarándolos privados, por ejemplo), se evita el acceso no autorizado o incorrecto desde fuera de la clase. Esto previene que el estado interno del objeto se corrompa o se modifique de forma inesperada, manteniendo los datos en un estado válido y consistente. En otras palabras, se asegura la **seguridad de los datos** dentro del objeto.
* **Control de acceso:** A través de modificadores de acceso (en lenguajes que los tienen, como *private*, *protected*, *public*), la clase puede controlar quién ve o modifica qué. Por ejemplo, puede permitir que solo ciertos métodos internos accedan a un atributo sensible. Esto establece una **barrera de protección** alrededor del objeto, reduciendo errores humanos al evitar usos indebidos de partes internas.
* **Ocultamiento de la complejidad (abstracción):** El encapsulamiento permite **ocultar los detalles de implementación** de una clase y presentar una interfaz pública sencilla y coherente. Otros desarrolladores u objetos pueden usar la clase sin necesidad de conocer su funcionamiento interno. Esto reduce la **complejidad aparente** del sistema, pues cada objeto expone solo lo necesario y oculta lo demás. Como resultado, **simplifica el uso** de los objetos desde el exterior y ayuda a **enfocarse en “qué hacen” en lugar de “cómo lo hacen”** internamente.
* **Flexibilidad y mantenimiento:** Al mediarlos accesos a los datos mediante métodos (getters/setters), es posible agregar lógica de validación o transformación de datos cuando se consultan o modifican. Además, si en el futuro se necesita cambiar la implementación interna (por ejemplo, calcular un valor de otra manera), esos cambios *no afectarán al código externo* mientras la interfaz pública se mantenga igual. Esto mejora la **mantenibilidad**, permitiendo actualizar o corregir la clase sin riesgo de efectos colaterales en otros módulos.
* **Reutilización y modularidad:** Una clase bien encapsulada es autónoma y confiable, por lo que puede reutilizarse fácilmente en distintas partes de un programa o incluso en proyectos diferentes. Al definir límites claros (interno vs. externo), fomenta la **modularidad**: las clases pueden combinarse como bloques, sabiendo que cada una maneja sus propios datos de forma segura.

En resumen, el encapsulamiento aporta **estructura** y **protección**. Ayuda a mantener las invariantes del objeto (sus reglas internas) y a prevenir usos incorrectos desde el exterior, lo cual redunda en software más robusto y fácil de mantener.

### Ejemplo de encapsulamiento en Python

En Python, a diferencia de lenguajes como Java o C++, no existen los modificadores de acceso formales (no hay palabras clave `private` o `protected`). Sin embargo, Python adopta una **convención** para indicar que un atributo o método es interno: usar un guion bajo `_` al comienzo del nombre (por ejemplo `_dato`) sugiere que es de uso interno (no debería accederse desde fuera de la clase). Para un nivel más fuerte de ocultamiento, Python permite prefijar el nombre con doble guion bajo `__`, lo que activa *name mangling* (enmangado de nombre): el nombre del atributo se modifica internamente (incluye el nombre de la clase) para dificultar el acceso externo directo.

A continuación, un ejemplo simple de encapsulamiento en Python usando estas convenciones:

```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre       # Atributo público
        self.__edad = edad         # Atributo "privado" (enmangling)

    def obtener_edad(self):
        return self.__edad        # Método público para acceder a edad

# Uso de la clase
p = Persona("Alice", 30)
print(p.nombre)       # "Alice" – acceso permitido al atributo público
print(p.__edad)       # Error – no se puede acceder al atributo privado directamente
print(p.obtener_edad())  # 30 – se accede a edad mediante el método público
```

En este ejemplo, la clase `Persona` encapsula el atributo `__edad` haciéndolo efectivamente privado. El intento de acceder a `p.__edad` desde fuera produce un error (`AttributeError`), porque Python renombra ese atributo internamente (a algo como `_Persona__edad`) para que no esté disponible con su nombre original. La única forma de obtener la edad es utilizando el método público `obtener_edad()`, que actúa como **interfaz controlada** hacia el dato interno. De esta manera, `Persona` puede validar o restringir cómo se lee/modifica la edad en un solo punto. Este mecanismo demuestra el encapsulamiento en Python: aunque no es estricto (un programador experto aún podría acceder al atributo mangled si lo intentara deliberadamente), **la convención y el diseño desalientan el acceso indebido**, protegiendo la integridad del objeto.

*Nota:* Python confía en la responsabilidad del programador (principio de “*somos todos adultos aquí*”). El encapsulamiento se implementa mayormente por acuerdo y no por imposición absoluta del lenguaje. Aún así, seguir estos lineamientos permite escribir código orientado a objetos más seguro y mantenible.

## Abstracción

### Definición conceptual

La **abstracción** en POO es el proceso de **identificar y enfocarse en las características esenciales de un objeto**, ignorando los detalles innecesarios. En otras palabras, es la capacidad de *aislar un elemento de su contexto* para considerar solo lo relevante de él. Aplicado a la programación, abstraer significa centrarse en **“¿qué hace?” un objeto o método, más que en “¿cómo lo hace?”**. Esto equivale al concepto de "caja negra": conocemos qué resultado produce o qué función cumple un objeto, sin necesitar saber los complejos mecanismos internos que lo permiten.

Otra forma de verlo: al modelar un sistema en objetos, la abstracción nos lleva a definir clases que representan conceptos del mundo real **con solo los atributos y comportamientos indispensables**. Las propiedades y operaciones irrelevantes para nuestros propósitos se dejan fuera del modelo. Así obtenemos una representación simplificada pero suficiente de la entidad. La abstracción establece **límites conceptuales** alrededor del objeto, distinguiendo qué es esencial (debe incluirse en la clase) de qué es secundario o circunstancial (puede omitirse). De este modo se crea un **modelo conceptual** manejable en código, que captura la esencia del objeto del mundo real sin abrumar con detalles.

### Analogía en la vida real

Podemos encontrar ejemplos de abstracción constantemente en nuestra interacción con la tecnología. Por ejemplo, piensa en un **reproductor de DVD**: desde fuera, solo tienes unos pocos botones (Reproducir, Pausa, Stop, etc.) con los que interactúas, y *no necesitas saber* qué ocurre dentro del aparato al presionarlos. Tú te enfocas en la operación **reproducir película**, sin preocuparte por cómo el DVD decodifica la señal de video, maneja el láser, o procesa la información; **toda esa complejidad interna está oculta para ti**. Lo importante para el usuario es *qué hace* el dispositivo (por ejemplo, “reproduce un video”) y no *cómo lo hace* internamente. Esta analogía refleja la abstracción: el fabricante del DVD diseñó una interfaz simple (los botones) que te permite lograr una tarea, ocultando los detalles técnicos complejos. De igual forma, en POO diseñamos clases centradas en las operaciones útiles que ofrecen, ignorando los pormenores internos durante el uso desde otras partes del programa.

Otra analogía común es un **automóvil**: cuando conducimos, usamos un volante, pedales y palancas (la interfaz pública del coche) para acelerar, frenar o girar. No es necesario comprender en detalle el funcionamiento interno del motor, la caja de cambios o los circuitos electrónicos para manejar el auto. El conductor trabaja a un nivel de abstracción alto (“quiero avanzar más rápido, giro la llave para encender, presiono el acelerador para moverme”) y el sistema del coche se encarga de todos los *cómos* detrás de escena. De nuevo, se enfatiza el *qué hace* (acelerar, frenar, girar) por encima del *cómo lo implementa* el vehículo. Esta separación conceptual es la esencia de la abstracción en sistemas orientados a objetos.

### Propósito y beneficios

La abstracción tiene como finalidad principal **manejar la complejidad** de los sistemas enfocándose solo en lo importante. Sus beneficios dentro de la POO incluyen:

* **Simplificación del diseño:** Al modelar clases con únicamente los aspectos esenciales, se consigue una representación más clara y entendible de cada componente del sistema. Quitar detalles irrelevantes hace que cada clase/objeto sea más simple de comprender. Una interfaz más reducida (pocos métodos bien definidos) facilita el uso correcto del objeto y reduce la posibilidad de errores. En resumen, la abstracción **convierte cosas complejas en modelos simples**, más cercanos a cómo pensamos en las entidades del mundo real.
* **Reducción de la complejidad y del impacto de cambios:** Debido a que los detalles internos quedan ocultos tras la abstracción, los usuarios de una clase *no necesitan conocer* (ni dependen de) esos detalles. Esto implica que si la implementación interna cambia en el futuro, mientras la interfaz (¿qué hace?) se mantenga, el código externo que usa esa clase no se ve afectado. Así, la abstracción **aísla los efectos de los cambios**, mejorando la mantenibilidad. Por ejemplo, si una clase “Archivo” inicialmente lee datos de disco y luego se modifica para leer de la nube, si ambos métodos están encapsulados y la interfaz de lectura es la misma, el resto del programa no tiene que adaptarse al cambio.
* **Enfoque en la funcionalidad de alto nivel:** La abstracción permite a los desarrolladores concentrarse en la **funcionalidad principal** de cada objeto y en cómo se relaciona con el problema que intentan resolver, en lugar de perderse en los detalles de implementación en las primeras etapas de diseño. Esto mejora el **diseño conceptual** del software: primero se define qué debe hacer cada objeto (sus responsabilidades) de forma general, y luego se implementa internamente cómo lo hace.
* **Reutilización de código a través de modelos genéricos:** Muchas veces la abstracción se concreta en la forma de **clases abstractas** o **interfaces** que definen un comportamiento general, que luego clases más concretas implementan o heredan. Esto fomenta la reutilización porque múltiples clases pueden compartir esa interfaz o clase base común en lugar de implementar por separado funcionalidades similares. Por ejemplo, se puede abstraer un concepto genérico de “Vehículo” (con métodos como `arrancar()` y `detener()`) y luego crear subclases `Coche` o `Moto` que reutilizan esa definición y la particularizan. La abstracción, por tanto, **promueve la generalización** de soluciones, haciendo el código más extensible y organizado.

En conjunto, la abstracción nos ayuda a **domar la complejidad**: permite construir piezas de software que exponen solo lo necesario y esconden los detalles, haciendo más fácil entender, usar y mantener el sistema en su conjunto. Como mencionan algunas fuentes, *“Ocultamos los detalles y la complejidad, mostrando solo lo esencial”*, lo que **reduce la complejidad** percibida y los efectos colaterales ante cambios.

### Ejemplo de abstracción en Python

Python soporta la abstracción principalmente a través de la definición de clases base abstractas y la utilización de métodos abstractos (mediante el módulo `abc` de la biblioteca estándar), aunque también se puede lograr mediante convenciones o utilizando clases base normales que las subclases deben sobreescribir. Veamos un ejemplo utilizando clases abstractas con `abc.ABC` (Abstract Base Class):

```python
from abc import ABC, abstractmethod

# Clase abstracta que define la interfaz (comportamiento esencial) de una figura geométrica
class Figura(ABC):
    @abstractmethod
    def area(self):
        """Calcula el área de la figura."""
        pass  # No se implementa aquí, las subclases deben hacerlo

# Subclases concretas que extienden Figura e implementan el método area
class Circulo(Figura):
    def __init__(self, radio):
        self.radio = radio
    def area(self):
        return 3.1416 * (self.radio ** 2)  # Fórmula del área de un círculo

class Rectangulo(Figura):
    def __init__(self, ancho, alto):
        self.ancho = ancho
        self.alto = alto
    def area(self):
        return self.ancho * self.alto  # Fórmula del área de un rectángulo

# Uso de las clases
# figura = Figura()  # Esto produciría un error: no se pueden instanciar clases abstractas
fig1 = Circulo(5)
fig2 = Rectangulo(3, 4)
print(fig1.area())  # 78.5398 (aprox.) – usa la implementación de Circulo
print(fig2.area())  # 12 – usa la implementación de Rectangulo
```

En este ejemplo, `Figura` es una **clase abstracta** que define un método `area()` sin implementación. Representa el concepto general de "figura geométrica" y actúa como un modelo conceptual: sabemos que cualquier figura tendrá un área, pero **¿cómo** se calcula depende de la figura específica. Las clases `Circulo` y `Rectangulo` **heredan** de `Figura` y proporcionan su propia implementación del cálculo de área.

Notemos cómo `Figura` encapsula la idea de "tener un área" (es el **qué hace** la figura), pero deja los detalles del cálculo a sus subclases (ellas definen **cómo lo hacen**). Esto es abstracción en acción: `Figura` ofrece una interfaz uniforme (`area()`) para todas las figuras, ocultando la complejidad de cada cálculo específico dentro de cada subclase. Además, **evita que se instancie** `Figura` directamente, ya que por sí sola no representa ninguna figura concreta (no tendría sentido crear un objeto `Figura` genérico). Solo se pueden instanciar las subclases concretas (`Circulo`, `Rectangulo`, etc.), que sí definen completamente su comportamiento.

El beneficio aparece cuando usamos estas clases: por ejemplo, podemos tratar a `fig1` y `fig2` como `Figura` genéricas en algunas funciones, sabiendo que **todas las figuras saben calcular su área**. Si en el futuro añadimos otra figura (un `Triangulo` que extienda `Figura`), simplemente implementará `area()` y podrá integrarse en el código existente sin modificarlo, gracias a que comparte la misma interfaz abstracta. Esto muestra cómo Python soporta la abstracción y a la vez sienta las bases para el polimorfismo (como veremos más adelante), todo mediante la definición de clases base e interfaces claras.

*Nota:* El uso de clases abstractas con `abc` es opcional en Python – uno podría simplemente documentar que ciertas clases deben implementar ciertos métodos. Sin embargo, emplearlas ayuda a **enforzar la abstracción** a nivel de código (evitando instancias de clases incompletas) y comunica claramente el diseño intentado.

## Herencia

### Definición conceptual

La **herencia** es un mecanismo de la POO que permite crear una **nueva clase a partir de una existente**, reutilizando y extendiendo sus características. En esencia, una clase **hija** (o subclase) hereda atributos y métodos de una clase **padre** (superclase), adquiriendo automáticamente todo su comportamiento, y tiene la opción de agregar nuevas funcionalidades o modificar (sobrescribir) las heredadas. Esto se suele describir como una relación “**es un**”: la subclase es un tipo más específico de la superclase. Por ejemplo, en términos de modelado, un **Perro** *es un* Animal; por tanto, la clase `Perro` podría heredar de la clase `Animal` todas sus propiedades generales (como nombre, edad, método `comer()`, etc.) y luego añadir comportamientos específicos (como `ladrar()`).

La herencia establece una **jerarquía de clases**. Una clase base general ocupa la cúspide (por ejemplo, `Animal`) y las clases derivadas especializadas se ubican por debajo (por ejemplo, `Perro`, `Gato`, que derivan de `Animal`). Cada subclase incorpora todo lo definido en su superclase sin necesidad de reescribir código, y solo define aquello adicional o diferente. En términos de software, *“heredar” código significa evitar reimplementarlo*: la subclase reutiliza lo ya existente y se concentra en las diferencias. Esto favorece la **extensibilidad** del sistema, ya que a partir de clases existentes comprobadas se pueden crear otras nuevas de forma más segura y rápida. La herencia, junto con la composición, es uno de los mecanismos más utilizados para alcanzar objetivos de **reutilización de código** en la POO.

### Analogía en la vida real

La herencia puede entenderse mediante analogías de categorías en el mundo real. Por ejemplo, pensemos en la **clasificación de vehículos**: existe un concepto general de *Vehículo* (con atributos como número de ruedas, capacidad, métodos como arrancar, frenar). Un **automóvil** sería una especialización de Vehículo — hereda todo lo común a cualquier vehículo (tiene ruedas, se puede conducir, frena, etc.), pero además tiene características particulares (cuatro ruedas, volante, etc.) que otros vehículos (como una motocicleta o un camión) pueden no compartir completamente. En diseño orientado a objetos, crearíamos una clase base `Vehiculo` y luego clases `Automovil`, `Motocicleta`, `Camion` que **heredan** de `Vehiculo`. Todas comparten propiedades/métodos de `Vehiculo`, pero cada una puede tener atributos propios (un camión tiene una capacidad de carga, la moto tal vez no) o comportamientos particulares. Esta estructura refleja una jerarquía de herencia similar a un árbol de clasificación.

Otra analogía: en biología se categorizan los seres vivos en reinos, géneros, especies, etc. Si tuviéramos una clase general `Animal` con métodos como `alimentarse` o `moverse`, una clase `Ave` podría heredar de `Animal` y agregar características como `volar()`, y a su vez una clase `Pingüino` podría heredar de `Ave` (y por ende también de `Animal`) con sus propios matices (quizá sobrescribe `volar()` porque los pingüinos no vuelan, y agrega `nadar()`). Cada nivel hereda características del nivel superior y añade las suyas. Esto demuestra cómo la herencia modela relaciones **jerárquicas “general-especial”** presentes en la realidad.

### Propósito y beneficios

La herencia ofrece varias ventajas clave dentro de la POO:

* **Reutilización de código:** Es el beneficio más inmediato. Permite definir atributos y métodos comunes en una clase base y que las subclases los aprovechen, evitando duplicación. En lugar de escribir el mismo código en múltiples clases parecidas, se escribe una vez en la superclase y *automáticamente* todas las clases derivadas lo tienen. Por ejemplo, si todas las figuras geométricas tienen un color, se define el atributo `color` en la clase base `Figura` y *todas* las figuras (círculo, cuadrado, triángulo) lo heredan sin código extra. Esto ahorra tiempo y reduce errores, pues las correcciones o mejoras al código común se hacen en un solo lugar.
* **Facilidad de extensión:** La herencia hace sencillo crear variantes o especializaciones de objetos ya existentes. Puedes tomar una clase ya implementada y extenderla (añadir o modificar comportamientos) en una subclase, en lugar de partir de cero. Esta flexibilidad permite adaptar el software cuando cambian o evolucionan los requisitos, agregando nuevas clases derivadas conforme se necesiten funcionalidades adicionales. Por ejemplo, si un programa tiene una clase base `Empleado` y de pronto se requieren empleados temporales, se puede crear `EmpleadoTemporal` que herede de `Empleado` y agregue su lógica particular, reutilizando todo lo demás.
* **Organización y jerarquía lógica:** Al agrupar características comunes en clases base generales, se crea una **estructura coherente** y escalable. Las clases derivadas mantienen una estructura y comportamiento consistentes definidos por la superclase. Esto refleja orden: facilita entender el sistema porque las relaciones *"es un"* están claras (un `Gato` es un `Animal`, un `Cuadrado` es una `Figura`, etc.). La jerarquía de clases actúa como un mapa conceptual del dominio del problema, donde niveles superiores proveen las definiciones generales y niveles inferiores los detalles específicos.
* **Relaciones tipo “es-un” y polimorfismo:** La herencia modela directamente relaciones naturales del mundo real, como vimos con el ejemplo Animal/Perro. Esto no solo es beneficioso conceptualmente, sino que es requisito para ciertos tipos de polimorfismo en lenguajes con tipado estático estricto: la posibilidad de tratar a un objeto de una subclase como si fuera de la superclase (por ejemplo, tratar a un `Perro` como un `Animal`) depende de la relación de herencia. Así, la herencia allana el camino para el polimorfismo *incluso cuando el lenguaje lo exige para el enlace dinámico*. En lenguajes dinámicos como Python esto no es obligatorio, pero igualmente la herencia facilita escribir código polimórfico al proveer una interfaz común en la clase base.
* **Abstracción y generalización:** La herencia bien utilizada permite sacar características comunes de varias clases y *abstraerlas* en una sola clase base. Esto evita redundancias y ayuda a entender mejor la esencia compartida entre objetos. Por ejemplo, si observamos que varias clases comparten ciertos atributos, podemos generalizarlos en una superclase. Así, la herencia también promueve la **factorización de conceptos comunes** en un nivel superior, haciendo el diseño más elegante.

En resumen, la herencia es fundamental para la **reutilización** y la **extensibilidad**. Evita reinventar la rueda con cada clase nueva, reduce la duplicación de código y crea sistemas organizados jerárquicamente que reflejan relaciones del dominio real. Es importante mencionar, sin embargo, que un uso excesivo o inadecuado de la herencia puede complicar el diseño (por ejemplo, jerarquías demasiado profundas pueden dificultar el seguimiento del código). Por eso, se suele equilibrar con otros mecanismos (como la composición) y aplicar herencia solo cuando tiene sentido la relación *general-especial* clara.

### Ejemplo de herencia en Python

Python soporta la herencia de forma sencilla usando la sintaxis de clases con paréntesis. Veamos un ejemplo básico con clases relacionadas:

```python
# Superclase
class Persona:
    def __init__(self, nombre):
        self.nombre = nombre
    def saludar(self):
        print(f"Hola, soy {self.nombre}.")

# Subclase que hereda de Persona
class Estudiante(Persona):
    def __init__(self, nombre, universidad):
        super().__init__(nombre)            # Reutiliza el constructor de Persona
        self.universidad = universidad
    # Agrega un nuevo método específico de Estudiante
    def mostrar_universidad(self):
        print(f"Estudio en {self.universidad}.")

# Uso de las clases
persona = Persona("Carlos")
estudiante = Estudiante("Ana", "Universidad XYZ")
persona.saludar()      # Salida: "Hola, soy Carlos."
estudiante.saludar()   # Salida: "Hola, soy Ana."  (método heredado de Persona)
estudiante.mostrar_universidad()  # Salida: "Estudio en Universidad XYZ."
```

En este ejemplo, `Estudiante` **hereda** de `Persona`. Gracias a ello, `Estudiante` automáticamente dispone del atributo `nombre` y del método `saludar()` definidos en `Persona` (lo comprobamos cuando llamamos `estudiante.saludar()` sin haberlo definido en `Estudiante`, funciona porque lo heredó). La subclase `Estudiante` extiende a `Persona` añadiendo un nuevo atributo `universidad` y el método `mostrar_universidad()`. Además, utiliza `super().__init__(nombre)` para aprovechar la inicialización de `Persona` (asignando el nombre) y así no repetir ese código.

Observemos los beneficios en la práctica: no fue necesario reescribir la lógica para almacenar el nombre ni la función para saludar en la clase `Estudiante`, todo eso se **reutilizó** de `Persona`. Si mañana cambiáramos cómo funciona `saludar()` en `Persona` (digamos, que imprima “Hola, soy {nombre}. ¡Mucho gusto!”), automáticamente todos los que heredan de `Persona` (`Estudiante` u otras subclases) adoptarían ese nuevo comportamiento sin modificar su código. Esto muestra la potencia de la herencia en Python: define relaciones *es-un* de manera natural (un estudiante *es una* persona) y permite que las subclases compartan código común de sus superclases.

Python también admite herencia múltiple (una subclase puede heredar de varias clases base), pero ese es un concepto más avanzado y debe usarse con cautela. En la mayoría de casos, la herencia simple (una sola superclase) como la mostrada es suficiente para modelar las relaciones de manera limpia.

*Nota:* Aunque la herencia es muy útil, es recomendable no abusar de ella. A veces es mejor **componer** objetos (tener objetos como atributos en lugar de heredar) si la relación no es estrictamente *“es un”*. Python brinda flexibilidad para ambos enfoques.

## Polimorfismo

### Definición conceptual

El término **polimorfismo** proviene del griego *“poli”* (muchos) y *“morfos”* (formas). En el contexto de POO, el polimorfismo denota la capacidad de un objeto de **presentarse en múltiples formas** o de **ser tratado de varias maneras equivalentes**. Más técnicamente, el polimorfismo permite que *objetos de distintas clases respondan a un mismo mensaje o llamada de función de forma similar*. Dicho de otro modo, diferentes tipos de objetos pueden compartir una misma interfaz (el mismo método, por ejemplo) pero cada uno puede tener su propia implementación detrás. Cuando se invoca ese método en un objeto concreto, **automáticamente se ejecuta la implementación específica de su clase**, incluso si el código que realiza la llamada no conoce el tipo exacto del objeto en tiempo de compilación.

Existen formas diferentes de polimorfismo, pero en la POO clásica basada en clases nos referimos principalmente al **polimorfismo de subtipo** (también llamado polimorfismo dinámico o de inclusión). Esto ocurre típicamente cuando tenemos una jerarquía de herencia: si la clase base define un método, y varias subclases lo sobrescriben a su manera, entonces un objeto de cualquiera de esas subclases puede ser tratado como objeto de la clase base y responderá al método común con su comportamiento particular. Por ejemplo, si la clase base `Animal` tiene un método `hacerSonido()`, y las clases `Perro` y `Gato` (subclases) cada una implementa `hacerSonido()` de forma diferente, podemos escribir código que invoque `hacerSonido()` sin preocuparse de si el objeto es perro o gato; en tiempo de ejecución, Python llamará al método específico del objeto que esté referenciado (al del perro si es un `Perro`, al del gato si es un `Gato`). Esa capacidad de *un mismo mensaje producir resultados distintos según el receptor* es el polimorfismo. En resumen, el polimorfismo nos **permite tratar objetos de clases diferentes de manera uniforme**, siempre que compartan cierta interfaz común.

Cabe mencionar que en Python (y lenguajes dinámicos) existe además polimorfismo por **duck typing**, donde no es necesaria una relación de herencia explícita: basta con que distintos objetos implementen el mismo método (aunque no hereden de la misma clase) para poder usarlos indistintamente. La famosa frase *“Si camina como un pato y suena como un pato, es un pato”* aplica aquí. Por ejemplo, dos clases no relacionadas `Pato` y `Persona` podrían ambas tener un método `caminar()`. En Python, podríamos llamar a `obj.caminar()` sin importar si `obj` es un pato o una persona, mientras tenga ese método. Esto también es polimorfismo (basado en la *forma* o interfaz, más que en la herencia). Sin embargo, en contextos académicos introductorios suele explicarse el polimorfismo apoyándose en la herencia, que es lo que haremos principalmente.

### Analogía en la vida real

Como concepto, el polimorfismo puede ilustrarse con situaciones en las que **una misma acción produce resultados diferentes según el contexto**. Un ejemplo típico: *imagina que tienes distintos animales (un perro, un gato, un pájaro) y les das la orden “hacer sonido”*. Todos los animales entienden la misma orden (interfaz común), pero **cada especie responde con un sonido distinto**: el perro ladra, el gato maúlla, el pájaro canta. Desde tu punto de vista, solo tuviste que dar una instrucción uniforme (“¡haz sonido!”) sin preocuparte de qué tipo de animal es cada uno, y sin embargo obtuviste comportamientos variados. Esto es exactamente lo que el polimorfismo logra en programación orientada a objetos. El “mensaje” es el mismo (`hacerSonido()`), pero el *objeto concreto* determina la acción ejecutada (ladra, maúlla, etc.).

Otra analogía: piensa en un **enchufe eléctrico estándar**. Tienes un enchufe en la pared (interfaz común de suministro eléctrico) y podrías conectar distintos aparatos: una lámpara, una licuadora, una computadora. Todos comparten la interfaz (el enchufe de 110V por ejemplo) y reaccionan al mismo “mensaje” (al recibir corriente eléctrica), pero el resultado es diferente en cada caso: la lámpara emite luz, la licuadora comienza a batir, la computadora enciende. Como usuario, te basta con saber que enchufando el dispositivo a la toma de corriente, este funcionará; no necesitas un tipo especial de enchufe para cada aparato. De forma análoga, en POO un mismo método invocado en distintos objetos puede desencadenar acciones distintas, pero el código que los utiliza no tiene que cambiar.

### Propósito y beneficios

El polimorfismo brinda **flexibilidad y elegancia** al diseño orientado a objetos. Sus beneficios incluyen:

* **Interfaz uniforme, comportamientos múltiples:** Permite escribir código genérico que funciona con objetos de distintos tipos sin distinguir entre ellos explícitamente, siempre que compartan la interfaz esperada. Esto simplifica la lógica del programa, ya que se evitan largas estructuras condicionales del tipo `if objeto es de tipo X haz A, else if es de tipo Y haz B`. En lugar de eso, simplemente se invoca `obj.metodoComun()` y gracias al polimorfismo *cada objeto “sabe” qué tiene que hacer*. El resultado es un código **más claro y sencillo**, enfocado en el *qué* (llamar al método) y no en el *quién/cómo* (qué clase específica es cada objeto y qué variante de código ejecutar).
* **Extensibilidad sin modificar código existente:** El polimorfismo trabaja en conjunto con la herencia para facilitar la extensión. Si en el sistema existe código que maneja objetos a través de una interfaz común (por ejemplo, procesar cualquier `Figura` llamando a su método `area()`), podemos agregar *nuevas clases* que implementen esa interfaz (una nueva figura, digamos `Triangulo` con su `area()`) y ese código seguirá funcionando sin cambios. Esto es muy poderoso: el software puede crecer en tipos soportados **sin tener que reescribir las funciones que los usan**, cumpliendo con el principio *abierto/cerrado* (abierto a extensión, cerrado a modificación). En definitiva, el polimorfismo promueve el diseño de código más **genérico y adaptable** a futuro.
* **Menor acoplamiento y más modularidad:** Al depender de interfaces comunes en lugar de clases concretas, los componentes del programa quedan menos acoplados. Por ejemplo, una función que opera sobre un objeto de tipo base `Animal` podrá trabajar con cualquier subclase de `Animal` que se le pase, sin saber detalles. Esto lleva a sistemas más modulares, donde las piezas se conectan a través de contratos (interfaces) bien definidos, y cada una puede variar independientemente mientras respete ese contrato.
* **Modelado natural de comportamientos variantes:** Muchos problemas del mundo real implican comportamientos que difieren según el tipo de objeto. El polimorfismo captura esa variabilidad de forma limpia. Un ejemplo clásico es el cálculo de áreas de diferentes polígonos: un programa puede tratar genéricamente polígonos y pedirles su área, y cada clase (`Triangulo`, `Rectangulo`, `Circulo`, etc.) realiza el cálculo con su fórmula, devolviendo el resultado a quien lo solicitó. El código cliente no necesita saber cuál fórmula usar, solo pide “area”. Esto no solo simplifica el código cliente, sino que **minimiza el conocimiento que unas partes del programa necesitan tener sobre otras**, mejorando el encapsulamiento general del sistema.

En suma, el polimorfismo aporta **flexibilidad** al permitir múltiples implementaciones bajo un mismo interface, y **eficiencia de diseño** al evitar condicionales extensos y permitir extender el sistema fácilmente. Es un concepto fundamental para lograr **código genérico y reutilizable** en la POO, ya que habilita a las funciones a trabajar con cualquier objeto apropiado sin importar su clase exacta, fomentando un estilo de programación más orientado a contratos (lo que un objeto *puede hacer*) y menos a tipos concretos.

### Ejemplo de polimorfismo en Python

En Python, el polimorfismo se manifiesta principalmente a través de la sobrescritura de métodos en clases derivadas y del uso de objetos intercambiables que comparten métodos. Vamos a demostrarlo con un ejemplo sencillo utilizando herencia para claridad. Tomemos la clase `Animal` y dos subclases, `Perro` y `Gato`, cada una con su implementación del método `hacer_sonido()`:

```python
class Animal:
    def hacer_sonido(self):
        print("Este animal hace un sonido genérico.")

class Perro(Animal):
    def hacer_sonido(self):
        print("El perro hace guau.")

class Gato(Animal):
    def hacer_sonido(self):
        print("El gato hace miau.")

# Polimorfismo en acción
animales = [Perro(), Gato()]   # una lista de objetos Perro y Gato
for animal in animales:
    animal.hacer_sonido()
# Salida:
# El perro hace guau.
# El gato hace miau.
```

En el código anterior, definimos `Perro` y `Gato` como subclases de `Animal`, ambas sobrescribiendo el método `hacer_sonido()` definido en `Animal`. Cuando instanciamos `Perro()` y `Gato()` y los recorremos en un bucle, en cada iteración la variable `animal` es tratada genéricamente (como un `Animal`). Sin embargo, Python determina en tiempo de ejecución la **verdadera clase** del objeto y llama al método correspondiente de esa clase. Así, aunque el código invoca el mismo método `hacer_sonido()` en la variable `animal` (sin saber si es perro o gato en ese momento), se producen resultados distintos: para el perro ejecuta la versión que imprime "guau", y para el gato la que imprime "miau". Este es un claro ejemplo de polimorfismo dinámico: *un mismo mensaje (`animal.hacer_sonido()`) desencadena comportamientos diferentes dependiendo del tipo del objeto*.

Es importante destacar que no tuvimos que escribir `if` o comprobar el tipo de `animal` para decidir qué función llamar — el polimorfismo se encargó de ello. Si más adelante agregáramos otra subclase, digamos `Vaca` que herede de `Animal` e implemente `hacer_sonido()` (por ejemplo "la vaca hace muuu"), podríamos incluirla en la lista `animales` y el bucle funcionaría inmediatamente, imprimiendo el sonido correspondiente, sin cambios adicionales en el código del bucle. Esto muestra la ventaja de extensibilidad mencionada: el código que usa la interfaz polimórfica (`hacer_sonido`) **no necesita ser modificado** para soportar nuevos tipos de Animal.

En Python, gracias a su tipado dinámico, también podríamos lograr algo similar aunque las clases no compartan una herencia común, siempre y cuando implementen el mismo método. Por ejemplo, podríamos tener una clase `Persona` con un método `hacer_sonido()` (que imprima "la persona habla") y si la agregamos a la lista `animales` anterior, el bucle seguirá llamando `hacer_sonido()` en cada objeto. Python no verificará la clase base, simplemente intentará invocar el método en cada objeto (*duck typing*). Si el método existe, se ejecutará. Esto demuestra un polimorfismo más basado en protocolos informales que en jerarquías, pero el efecto para el usuario del objeto es similar: invocar una operación común en objetos heterogéneos.

Para mantener nuestro diseño claro, usualmente estructuramos el polimorfismo mediante herencia o interfaces formales, como en el ejemplo con `Animal`. Así comunicamos explícitamente que esas clases comparten comportamiento y pretenden ser tratadas de forma uniforme. En nuestro ejemplo, `Perro` y `Gato` son **tipos de Animal**, lo que semánticamente tiene sentido y aprovecha el polimorfismo para simplificar el código cliente.

---

**Conclusión:** Los cuatro pilares de la POO —encapsulamiento, abstracción, herencia y polimorfismo— proporcionan las bases para crear software orientado a objetos de calidad. El **encapsulamiento** protege los datos y delimita interfaces claras, la **abstracción** reduce la complejidad enfocándose en lo esencial, la **herencia** permite reutilizar código y modelar jerarquías lógicas, y el **polimorfismo** brinda flexibilidad al permitir tratar objetos distintos de manera uniforme. Combinando estos principios, los desarrolladores pueden construir sistemas más **organizados, mantenibles y extensibles**, siguiendo modelos que reflejan mejor las entidades y relaciones del mundo real. Con Python hemos visto ejemplos concretos de cómo se aplican estos conceptos, recordando que si bien Python tiene sus particularidades (encapsulamiento por convención, duck typing), es totalmente capaz de soportar y aprovechar estos pilares fundamentales de la programación orientada a objetos.
