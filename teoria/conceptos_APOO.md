
# Introducción a la Programación Orientada a Objetos (POO)

## ¿Qué es la POO? (Definición y analogías reales)

La **Programación Orientada a Objetos (POO)** es un paradigma de desarrollo de software centrado en organizar el código en torno a **objetos**. En lugar de separar datos y funciones por completo (como en la programación estructurada), en POO cada objeto reúne **información** (datos) y **comportamiento** (código) relacionados. Dicho de otro modo, un objeto es una entidad del programa que contiene datos en forma de *atributos* (también llamados *propiedades*) y funcionalidades en forma de *métodos*. Esta forma de estructurar el código refleja mejor cómo son las cosas en el mundo real: un objeto en POO representa una **entidad del mundo real** con cierto estado (datos) y acciones que puede realizar (comportamientos). Por ejemplo, podríamos modelar un **coche** como un objeto: con atributos como su color, marca o velocidad, y métodos como arrancar, frenar o acelerar, análogamente a cómo un coche real tiene propiedades y operaciones asociadas.

Una forma común de entender la POO es usando **analogías de la vida real**. Imaginemos que una **clase** es como un plano o diseño arquitectónico, y un **objeto** es la casa construida siguiendo ese plano. La clase define las características y comportamientos comunes (especifica qué propiedades y métodos tendrán sus objetos), mientras que cada objeto es una instancia concreta con sus propios valores para esas propiedades. Siguiendo esta analogía, si la clase es el plano de un *televisor*, cada objeto sería un televisor individual ensamblado conforme a ese diseño. Todos los televisores construidos a partir de la misma clase comparten la misma estructura y funciones (por ejemplo, encender/apagar, cambiar canal), aunque cada uno tenga su propio estado (ciertos canales sintonizados, volumen actual, etc.). Gracias a este enfoque, la POO permite “transformar el mundo real en código” de manera más natural, facilitando la representación de problemas complejos de forma modulada y comprensible.

## Conceptos fundamentales: clases, objetos, atributos y métodos

Para adentrarnos en la POO, debemos dominar algunos conceptos básicos:

* **Clase:** es una plantilla o definición general de un conjunto de objetos. En una clase se describen qué datos (*atributos*) y qué funciones (*métodos*) tendrán los objetos de ese tipo. La clase actúa como un molde: define las propiedades y comportamientos comunes. Por ejemplo, podríamos tener una clase `Televisor` que especifique que todos los televisores tienen atributos como tamaño, marca, y métodos como encender() o cambiarCanal(). La clase no es un objeto en sí mismo, sino el *diseño* a partir del cual se pueden crear objetos (instancias). Una clase encapsula tanto datos como funciones relacionadas en una sola unidad lógica.

* **Objeto:** es una instancia concreta de una clase, es decir, un ejemplar creado según el “molde” de la clase. Si la clase es el plano, el objeto es el producto final. Siguiendo el ejemplo anterior, cada televisor específico (el televisor de tu sala, el de tu vecino, etc.) sería un objeto de la clase `Televisor`. Cada objeto tiene su **estado propio** (valores particulares en sus atributos, como un televisor puede tener cierto volumen y canal actual) y puede ejecutar los métodos definidos por su clase. Un objeto de POO, al igual que su contraparte del mundo real, combina un estado (atributos con valores concretos) y un conjunto de comportamientos (acciones que sabe realizar). Importante: cuando hablamos de “objeto” nos referimos a algo que existe en tiempo de ejecución (en la memoria del programa), mientras que la clase es una definición estática en el código que describe a esos objetos.

* **Atributos (propiedades):** son las variables que pertenecen a una clase u objeto y describen su estado o características. A menudo, en POO se usa *atributo* y *propiedad* como sinónimos, ya que ambos términos se refieren a datos asociados a un objeto. Por ejemplo, un objeto de clase `Coche` podría tener atributos como `color`, `modelo` y `velocidad_actual`. Cada objeto almacena valores propios en sus atributos (un coche puede ser rojo y otro azul). En términos de la vida real, los atributos representan cualidades o características de la entidad modelada (el color de un coche, la capacidad de un televisor, etc.). Estos datos suelen representarse con **sustantivos**, porque describen cosas (ej. *color*, *tamaño*, *saldo*).

* **Métodos:** son las funciones o procedimientos asociados a una clase/objeto, que definen el comportamiento de los objetos. Los métodos permiten que los objetos “hagan” cosas o reaccionen a mensajes. Continuando con el ejemplo del `Coche`, podríamos tener métodos como `acelerar()`, `frenar()` o `pintar(nuevo_color)`. Los métodos se definen dentro de la clase y pueden acceder o modificar los atributos del objeto para cambiar su estado. En la analogía real, los métodos son las acciones que el objeto puede realizar o las respuestas a ciertas interacciones (un televisor puede *cambiar de canal*, un coche puede *arrancar*). Convencionalmente, los nombres de métodos suelen ser **verbos** ya que representan acciones (ej. *imprimirFactura()*, *enviarMensaje()*), mientras que los atributos son sustantivos.

&#x20;Un ejemplo de objeto “Calculadora” mostrando sus datos (*atributos*, como color, tamaño, número de botones) y comportamientos (*métodos*, como sumar(), multiplicar(), dividir()). Como se aprecia, los atributos describen al objeto (estado) y los métodos definen lo que el objeto puede hacer (comportamiento).

En resumen, una *clase* define atributos y métodos; un *objeto* es una instancia que posee valores concretos para esos atributos y puede ejecutar los métodos definidos. Esta compartimentación de datos y funcionalidades dentro de objetos es el corazón de la POO, y nos permite modelar componentes de software que se asemejan a “cosas” del mundo real con las que es más intuitivo razonar.

## Los cuatro pilares o principios de la POO

Existen cuatro principios fundamentales comúnmente asociados a la Programación Orientada a Objetos. Estos pilares —**encapsulamiento, herencia, polimorfismo** y **abstracción**— proveen la base teórica para construir sistemas de software robustos y flexibles. A continuación, explicamos cada principio de forma sencilla, con ejemplos ilustrativos:

### Encapsulamiento

El **encapsulamiento** consiste en ocultar los detalles internos de un objeto y exponer solo aquello que sea necesario para su uso desde el exterior. En la práctica, esto significa que los atributos de un objeto suelen mantenerse *privados* o protegidos, mientras que se proporciona una *interfaz pública* (métodos públicos) para interactuar con él. De este modo, se protegen los datos internos de modificaciones indebidas y se controla cómo se accede o cambia el estado del objeto.

*Ejemplo:* Pensemos en una clase *CuentaBancaria*. Esta podría tener atributos privados como `saldo` o `titular`, y métodos públicos como `depositar(cantidad)` y `retirar(cantidad)`. El atributo `saldo` no sería accesible directamente desde fuera de la clase; en su lugar, el método `retirar` se encarga de disminuir el saldo solo si hay suficiente fondo disponible, y quizás otro método `consultar_saldo()` permite leer el valor actual. Así, la lógica interna (cómo se guarda y valida el saldo) queda encapsulada dentro del objeto, evitando que cualquier código externo lo modifique arbitrariamente. Este principio promueve la **ocultación de la información**: los objetos exponen únicamente lo necesario, manteniendo sus datos internos en un estado válido y consistente. Desde el punto de vista del diseño, el encapsulamiento mejora la **seguridad** y la **mantenibilidad** del código, ya que cambios en la implementación interna de una clase no afectan a otros componentes siempre y cuando la interfaz pública se mantenga.

### Herencia

La **herencia** permite crear nuevas clases a partir de clases existentes, reutilizando y especializando su comportamiento. Mediante herencia, una clase **hija** (o *subclase*) extiende a una clase **padre** (o *superclase*), adquiriendo automáticamente sus atributos y métodos. La subclase puede añadir nuevos atributos/métodos propios y también redefinir (sobrescribir) algunos métodos de la superclase para adaptar o extender su funcionalidad.

*Ejemplo:* Supongamos que tenemos una clase base `Persona` con atributos como `nombre` y `edad`, y un método `presentarse()` que imprime un saludo. Podemos definir una subclase `Empleado` que **herede** de `Persona`. `Empleado` automáticamente tendrá `nombre`, `edad` y `presentarse()` como cualquier persona, pero además podría agregar un atributo `salario` y sobrescribir el método `presentarse()` para incluir el cargo o la información de empleado en la presentación. Gracias a la herencia, evitamos reescribir código desde cero: aprovechamos lo ya definido en `Persona` y solo especificamos las diferencias en `Empleado`. En código, esto se vería así por ejemplo:

```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.edad = edad
    def presentarse(self):
        print(f"Hola, soy {self.nombre} y tengo {self.edad} años.")

class Empleado(Persona):  # Empleado hereda de Persona
    def __init__(self, nombre, edad, salario):
        super().__init__(nombre, edad)      # Reutiliza el constructor de Persona
        self.salario = salario             # Añade nuevo atributo
    def presentarse(self):                # Sobrescribe el método de Persona
        super().presentarse()             # Opcional: llama a la versión base
        print(f"Además, soy empleado y gano {self.salario} al año.")
```

En este ejemplo, `Empleado` hereda lo común de `Persona` y luego lo especializa con información adicional. La herencia favorece la **reutilización de código** y la creación de jerarquías de clases organizadas. Sin embargo, es importante usarla con criterio: una relación de herencia indica normalmente una relación *"es un(a)"* —por ejemplo, un Empleado **es una** Persona—. Si la relación no es natural, podría convenir usar **composición** en lugar de herencia (principio de "favorir la composición sobre la herencia" en buenas prácticas).

### Polimorfismo

El término **polimorfismo** proviene del griego y significa "muchas formas". En POO, el polimorfismo se refiere a la capacidad de diferentes objetos de responder al **mismo mensaje o método de maneras distintas**, típicamente porque pertenecen a clases distintas que implementan ese método cada una a su forma. En otras palabras, dos o más clases pueden definir un método con el mismo nombre, y la llamada a ese método se comportará según la clase del objeto que la reciba.

Existen dos manifestaciones comunes del polimorfismo:

* **Sobreescritura de métodos:** Ocurre cuando una subclase redefine un método heredado de la superclase. Si tenemos una referencia genérica (por ejemplo, una variable de tipo `Persona`) que en tiempo de ejecución apunta a un objeto de la subclase (`Empleado`), al invocar un método sobre esa referencia se ejecutará la versión específica del objeto real (la de `Empleado` en este caso). Es decir, un mismo método `presentarse()` actúa diferente si el objeto concreto es una Persona general o un Empleado específico.
* **Sobrecarga de métodos:** (En algunos lenguajes) Permite que existan múltiples versiones de un método en la misma clase, con diferentes parámetros. Python no soporta sobrecarga estricta de métodos por firma, pero sí se puede lograr comportamiento polimórfico mediante valores por defecto o inspección de tipos dentro del método.

*Ejemplo:* Imaginemos una clase `Vehiculo` con un método `moverse()`. Podemos tener varias subclases de `Vehiculo` (por ejemplo `Auto`, `Bicicleta`, `Avion`), cada una implementando su propia versión de `moverse()`. Un auto al moverse podría imprimir "El auto avanza sobre ruedas", mientras que una bicicleta podría imprimir "La bicicleta pedalea en la carretera". Si escribimos código que llame al método `moverse()` sin importar el tipo exacto de vehículo, cada objeto responderá según su propia implementación. Por ejemplo:

```python
class Vehiculo:
    def moverse(self):
        print("El vehículo se está moviendo.")

class Auto(Vehiculo):
    def moverse(self):
        print("El auto se está moviendo sobre cuatro ruedas.")

class Bicicleta(Vehiculo):
    def moverse(self):
        print("La bicicleta avanza pedaleando.")
```

Ahora, si iteramos sobre una lista de vehículos mixtos y hacemos que cada uno `moverse()`, veremos polimorfismo en acción:

```python
vehiculos = [Auto(), Bicicleta(), Vehiculo()]
for v in vehiculos:
    v.moverse()
```

La salida podría ser:

```
El auto se está moviendo sobre cuatro ruedas.
La bicicleta avanza pedaleando.
El vehículo se está moviendo.
```

Cada objeto invoca una versión distinta del método, dependiendo de su clase concreta. El código que los llama no necesita saber si es un Auto o una Bicicleta; simplemente envía el mensaje `moverse` y el objeto adecuado responde de forma específica. Este poder de tratar objetos de diferentes clases de forma uniforme aumenta la **flexibilidad** y escalabilidad del software, pues permite, por ejemplo, intercambiar componentes fácilmente siempre que respeten cierta interfaz común.

### Abstracción

La **abstracción** es el principio de manejar la complejidad ocultando los detalles de implementación y destacando solamente las características esenciales de un objeto o sistema. En POO, la abstracción se refleja en la idea de *modelar entidades del mundo real en clases*, concentrándose en los aspectos relevantes para el problema y dejando de lado lo innecesario. Mediante la abstracción definimos **modelos genéricos** que capturan lo importante de un conjunto de cosas.

En términos prácticos, la abstracción suele manifestarse a través de **clases abstractas** o **interfaces** (en lenguajes que las soportan), que especifican un conjunto de métodos que sus implementaciones concretas deberán tener, pero sin dar detalles del cómo los implementan. Esto fuerza a las subclases a proveer su propia versión de ciertos comportamientos, manteniendo un contrato común.

*Ejemplo:* Pensemos en la clase abstracta `Vehículo` que mencionamos antes. `Vehículo` podría ser considerada una abstracción de cualquier medio de transporte: define métodos genéricos como `arrancar()`, `detenerse()` o `acelerar()` que *cualquier* vehículo debería tener, pero la implementación de esos métodos dependerá del tipo específico de vehículo. Un `Auto` arrancará encendiendo un motor de combustión o eléctrico, mientras que un `Bicicleta` “arranca” cuando el ciclista comienza a pedalear. Al definir `Vehículo` como abstracción, no nos preocupamos por esos detalles; solo establecemos que todos los vehículos comparten ciertas funcionalidades comunes. Cada subclase concreta proveerá la lógica específica. En Python, podríamos expresar esta idea usando el módulo `abc` (Abstract Base Classes):

```python
from abc import ABC, abstractmethod

class Vehiculo(ABC):
    @abstractmethod
    def arrancar(self):
        pass
    @abstractmethod
    def detenerse(self):
        pass

class Auto(Vehiculo):
    def arrancar(self):
        print("El auto enciende su motor.")
    def detenerse(self):
        print("El auto frena con sus frenos de disco.")

class Bicicleta(Vehiculo):
    def arrancar(self):
        print("La bicicleta comienza a pedalear.")
    def detenerse(self):
        print("La bicicleta frena con los pedales hacia atrás.")
```

Aquí `Vehiculo` actúa como interfaz abstracta: declara qué métodos deben existir, pero son `Auto` y `Bicicleta` quienes implementan los detalles. La abstracción nos ayuda a **simplificar** y **organizar** el diseño, permitiendo razonar en términos de conceptos generales (p.ej. “vehículo”) sin entrar al detalle de cada caso hasta que sea necesario. También reduce la duplicación de código al agrupar en una sola abstracción lo común de múltiples implementaciones.

**Resumen de los pilares:** Encapsulamiento protege datos y oculta complejidad interna; Herencia permite reutilizar y organizar el código en jerarquías; Polimorfismo brinda flexibilidad al manejar objetos distintos bajo un interfaz común; Abstracción se enfoca en los conceptos esenciales, ignorando detalles irrelevantes para el contexto. Juntos, estos principios hacen posible construir sistemas orientados a objetos más **organizados, mantenibles y extensibles**.

## Implementación de la POO en Python (sintaxis y ejemplos)

Ahora que entendemos los conceptos, veamos **cómo se implementan en Python** estos elementos de la programación orientada a objetos. Python es un lenguaje multiparadigma que soporta la POO de forma natural, permitiendo definir clases, crear objetos, heredar entre clases, etc. Veremos ejemplos prácticos de cada concepto en código Python.

### Definición de clases y creación de objetos en Python

En Python, se define una clase usando la palabra clave `class` seguida del nombre de la clase. Dentro de la clase, normalmente definimos un método especial `__init__` (el **constructor**) que se ejecuta al crear un nuevo objeto (instancia) de esa clase. Este constructor suele recibir parámetros para inicializar los atributos del objeto mediante `self` (que es la referencia al propio objeto). También se pueden definir otros métodos que describan comportamientos.

Veamos un ejemplo simple de una clase `Persona` con atributos y métodos, y cómo instanciarla:

```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre    # atributo de instancia
        self.edad = edad        # atributo de instancia

    def presentarse(self):
        print(f"Hola, mi nombre es {self.nombre} y tengo {self.edad} años.")
```

En esta clase, `nombre` y `edad` son atributos que cada `Persona` tendrá, y `presentarse()` es un método que imprime un saludo usando esos atributos. Notemos el uso de `self`: dentro de cualquier método de instancia, `self` refiere al propio objeto, permitiendo acceder a sus atributos (ej. `self.nombre`). Para **crear un objeto** (instancia) de la clase, simplemente llamamos a la clase como si fuese una función, pasando los argumentos esperados por `__init__`:

```python
p1 = Persona("Juan", 30)   # crea una instancia de Persona
p2 = Persona("Ana", 25)    # otra instancia distinta

p1.presentarse()  # Llamamos el método presentarse() de Juan
p2.presentarse()  # Llamamos el método presentarse() de Ana
```

La salida sería:

```
Hola, mi nombre es Juan y tengo 30 años.
Hola, mi nombre es Ana y tengo 25 años.
```

Cada objeto (`p1`, `p2`) mantiene su propio estado interno (`nombre` y `edad` específicos), y el método `presentarse` utiliza ese estado. En Python, las clases proporcionan una manera de empaquetar datos y funcionalidad juntos: al crear una nueva clase, definimos un **nuevo tipo de objeto**, y las instancias de esa clase tendrán sus propios datos (atributos) y podrán ejecutar sus métodos. Todos los objetos en Python son en esencia instancias de alguna clase.

Además de atributos de instancia (los definidos en `__init__` con `self.`), Python también permite atributos *de clase* (compartidos por todas las instancias) y métodos estáticos o de clase, pero esos son detalles que van más allá de esta introducción básica.

### Herencia en Python

Python soporta herencia de clases de forma sencilla. Para indicar que una clase nueva hereda de otra, se coloca el nombre de la superclase entre paréntesis en la definición de la subclase. La subclase puede entonces extender o modificar el comportamiento de la superclase. Usemos la clase `Persona` anterior para ilustrar la herencia creando una subclase `Empleado`:

```python
class Empleado(Persona):                   # hereda de Persona
    def __init__(self, nombre, edad, salario):
        super().__init__(nombre, edad)     # invoca al constructor de Persona
        self.salario = salario            # añade un nuevo atributo

    def presentarse(self):                # sobrescribe el método de Persona
        super().presentarse()             # opcional: usar la versión de Persona
        print(f"Soy un empleado con salario {self.salario}.")
```

**Explicación:** Al definir `class Empleado(Persona):`, indicamos que `Empleado` es una especialización de `Persona`. Dentro de `Empleado.__init__`, usamos `super().__init__(nombre, edad)` para llamar al constructor de `Persona` y así reutilizar la inicialización de `nombre` y `edad`. Luego definimos el nuevo atributo `salario`. También redefinimos el método `presentarse()`: primero llamamos al `presentarse` original de `Persona` (para no repetir el código de saludo básico) y luego añadimos información adicional del empleado. Gracias a la herencia, `Empleado` automáticamente tiene también los demás métodos de `Persona` (si hubiera otros) sin necesitarlos redefinir.

Veamos cómo funciona:

```python
e = Empleado("Carlos", 40, 50000)
e.presentarse()
```

Salida:

```
Hola, mi nombre es Carlos y tengo 40 años.
Soy un empleado con salario 50000.
```

Primero se ejecutó la lógica de `Persona.presentarse` y luego la línea añadida por `Empleado.presentarse`. Podemos observar que `Empleado` **heredó** los atributos `nombre` y `edad`, así como el método `presentarse` de la clase base, pero al sobrescribir dicho método pudo **polimórficamente** alterar su comportamiento.

Python también permite que una subclase acceda a atributos o métodos de la superclase usando la función `super()`, como vimos. Además, es posible la **herencia múltiple** (una clase hereda de varias), aunque debe usarse con cuidado debido a la complejidad que añade. En síntesis, la herencia en Python sigue el mismo objetivo general: factorizar código común en clases base y especializar comportamientos en subclases, promoviendo reutilización.

### Polimorfismo en Python

En Python, al ser un lenguaje dinámico, el polimorfismo se suele lograr principalmente mediante **sobrescritura de métodos** y el concepto de *duck typing*. La sobrescritura ya la vimos: una subclase puede implementar de forma diferente un método definido en su superclase. Cuando se llama ese método en un objeto de la subclase, Python ejecutará la versión de la subclase (y no la de la superclase), logrando así polimorfismo.

Retomando el ejemplo de los vehículos, definamos dos clases con el mismo método y veamos cómo responden:

```python
class Vehiculo:
    def moverse(self):
        print("El vehículo se está moviendo.")

class Barco(Vehiculo):
    def moverse(self):
        print("El barco navega en el agua.")

class Avion(Vehiculo):
    def moverse(self):
        print("El avión vuela por el aire.")
```

Aquí `Barco` y `Avion` heredan implícitamente de `Vehiculo` (ya que no especificamos otra superclase, Python toma `object` por defecto, pero podemos conceptualmente verlos como vehículos) y cada uno tiene su propia versión de `moverse()`. Ahora:

```python
mi_barco = Barco()
mi_avion = Avion()

mi_barco.moverse()  # Llamará al método de Barco
mi_avion.moverse()  # Llamará al método de Avion

# Podemos tratar ambos objetos de forma genérica:
for vehiculo in [mi_barco, mi_avion]:
    vehiculo.moverse()
```

La salida sería:

```
El barco navega en el agua.
El avión vuela por el aire.
El barco navega en el agua.
El avión vuela por el aire.
```

Vemos que, aunque `vehiculo` en el bucle no tiene un tipo fijo (podría ser cualquier objeto con método `moverse`), cada iteración invoca la versión específica correspondiente. Este es un ejemplo de **duck typing** en Python: "si algo se comporta como un pato (duck), entonces es un pato". Es decir, no importa la clase exacta, mientras el objeto tenga el método solicitado, Python lo ejecutará. Esto permite polimorfismo sin necesidad de una relación de herencia estricta, siempre que los objetos compartan la interfaz (en este caso, el nombre del método).

En lenguajes estáticos se suele enfatizar que polimorfismo funciona a través de una clase base común. En Python, gracias al tipado dinámico, podemos tener polimorfismo incluso entre clases no relacionadas, simplemente porque implementan métodos con el mismo nombre. Por ejemplo, si dos clases distintas tienen un método `__str__`, ambas se pueden "imprimir" mediante `str(objeto)`; Python llamará al método de cada una sin preocuparle su tipo. Esta flexibilidad es poderosa, pero requiere disciplina para asegurar que los objetos realmente tengan los métodos esperados cuando se usan polimórficamente.

### Encapsulamiento en Python

Python no aplica el encapsulamiento de forma tan estricta como lenguajes como Java o C++. No existen palabras clave como `private` o `protected` que impidan el acceso a atributos desde afuera. Sin embargo, Python emplea una **convención de nombres** para indicar que algo es interno/privado: se usan uno o dos guiones bajos prefijados al nombre del atributo o método. Por ejemplo, por convención:

* Un nombre que comienza con `_` (un guión bajo) indica que es una **variable interna** o de uso interno en la clase (no debería usarse fuera de ella, aunque es accesible).
* Un nombre que comienza con `__` (doble guión bajo) activa una característica llamada *name mangling* (enmangado de nombres), donde Python renombra internamente el atributo para dificultar su acceso desde fuera de la clase (lo precede con el nombre de la clase). Esto hace que, efectivamente, `__mi_atributo` no sea directamente accesible como tal desde el exterior.

Veamos un ejemplo con la clase `CuentaBancaria` mencionada:

```python
class CuentaBancaria:
    def __init__(self, titular, saldo=0):
        self.titular = titular      # atributo público (convención: no empieza con _)
        self.__saldo = saldo        # atributo privado (nombre con __)

    def depositar(self, cantidad):
        self.__saldo += cantidad    # modificar saldo internamente

    def retirar(self, cantidad):
        if cantidad > self.__saldo:
            print("Saldo insuficiente.")
        else:
            self.__saldo -= cantidad

    def obtener_saldo(self):
        return self.__saldo
```

En esta clase, `__saldo` es un atributo pretendidamente privado. Podemos depositar o retirar dinero mediante los métodos públicos, pero desde fuera no deberíamos acceder a `__saldo` directamente. Si intentamos:

```python
cuenta = CuentaBancaria("Alice", 100)
print(cuenta.__saldo)
```

Obtendríamos un AttributeError, algo como `'CuentaBancaria' object has no attribute '__saldo'`. Esto sucede porque Python internamente renombró `__saldo` a `_CuentaBancaria__saldo` para evitar colisiones de nombres en posibles subclases y para desincentivar su acceso externo. Aún así, cabe señalar que no es imposible acceder (un código malicioso podría usar `cuenta._CuentaBancaria__saldo` debido al name mangling), pero la idea es que el desarrollador respete la convención. En la práctica, se considera que todo atributo con uno o dos guiones bajos iniciales **no debe ser tocado desde fuera** de la clase (principio de encapsulamiento). En lugar de eso, se usan métodos públicos como `obtener_saldo()` para consultar el valor.

Otra manera *pythónica* de manejar el encapsulamiento es mediante **propiedades**. Python ofrece el decorador `@property` para definir métodos que se usan como si fueran atributos, permitiendo controlar la lectura/escritura. Por ejemplo, podríamos hacer:

```python
class Persona:
    def __init__(self, nombre):
        self.__nombre = nombre   # atributo "privado"

    @property
    def nombre(self):
        return self.__nombre    # proporcionamos solo lectura del nombre

    @nombre.setter
    def nombre(self, nuevo_nombre):
        if len(nuevo_nombre) > 0:
            self.__nombre = nuevo_nombre
        else:
            print("Nombre no puede ser vacío.")
```

Así, `persona.nombre` accede al nombre a través del método getter (y no directamente al atributo interno), y al intentar asignar `persona.nombre = ""` se invocaría el setter, que valida la entrada. Esta técnica de usar propiedades es muy útil para **encapsular lógica de validación o control** manteniendo una sintaxis simple al usar la clase.

En resumen, aunque Python no impone encapsulación, es posible implementarla por convención y herramientas del lenguaje. El encapsulamiento sigue siendo una buena práctica en Python para mantener la integridad del estado de los objetos, y normalmente se aconseja: “*Somos todos adultos aquí*”, es decir, los programadores deben respetar los guiones bajos y no romper el encapsulamiento a menos que sea absolutamente necesario.

### Abstracción en Python

La abstracción en Python se logra principalmente a nivel de diseño más que por restricciones del lenguaje. Como vimos, Python ofrece el módulo `abc` para definir **clases abstractas** y métodos abstractos (como en el ejemplo de `Vehiculo` con `@abstractmethod`). Sin embargo, no es obligatorio usarlo; muchos desarrolladores simplemente establecen convenciones o documentan qué métodos deberían implementarse en las subclases.

Por ejemplo, podemos definir una clase base que se espera no sea instanciada directamente, sino solo a través de sus derivadas. Se puede lanzar una excepción en el constructor o método si se detecta que la clase base está siendo usada indebidamente. Pero la forma más clara es usar `abc.ABC` como se hizo arriba, de modo que Python impida instanciar la clase abstracta y obligue a implementar los métodos marcados.

Otra forma de ver la abstracción en Python es mediante la **simplificación de interfaces**: proveer clases/metodos fáciles de usar sin exponer la complejidad interna. Esto se relaciona con encapsulamiento y con el principio de diseño llamado *"ocultar la implementación y resaltar la intención"*. Un ejemplo común es diseñar APIs de clases donde el usuario de la clase no necesita saber cómo hace algo internamente (porque esos detalles están abstractos/encapsulados). Por ejemplo, la clase `ListaEnlazada` (estructura de datos) puede exponer métodos como `agregar(elemento)` o `eliminar(elemento)` sin que quien la use tenga que saber si internamente es un array, una lista ligada simple o doble, etc. La **abstracción** está en que la complejidad de la estructura interna queda oculta tras una interfaz sencilla.

En síntesis, Python soporta la abstracción a través de:

* Clases base abstractas (`abc.ABC`) y métodos abstractos.
* El uso de propiedades para exponer una interfaz de atributos controlada.
* El diseño de clases con interfaces claras que no revelan la lógica interna innecesaria.

Aunque Python no aplica abstracción de manera forzada (como Java con interfaces o métodos abstractos obligatorios), un buen diseño orientado a objetos en Python aún aprovechará estos conceptos para producir código más limpio y entendible.

## Buenas prácticas de diseño orientado a objetos

Aplicar correctamente la POO va más allá de conocer su sintaxis; implica seguir **buenas prácticas de diseño** para crear software mantenible, flexible y extensible. A continuación, se enumeran algunas recomendaciones y principios importantes al trabajar con Orientación a Objetos, especialmente relevantes para futuros ingenieros de software:

* **Alta cohesión y bajo acoplamiento:** Procura que cada clase tenga una responsabilidad bien definida (*cohesión*) y evita que esté demasiado dependiente de otras (*acoplamiento*). Una clase debería representar un concepto o funcionalidad singular (Principio de *Responsabilidad Única* de SOLID). Esto facilita que las clases sean reutilizables y que los cambios en una no repercutan innecesariamente en otras. Por ejemplo, en un sistema de venta, podríamos tener clases separadas para `CarritoDeCompra`, `Producto`, `Pedido`, etc., en lugar de una sola clase gigante que maneje todo (evitando así una clase *God Object*). Mantener bajo acoplamiento también implica preferir interacciones simples y bien definidas (p. ej., métodos claros en la interfaz pública) en lugar de que una clase manipule directamente la estructura interna de otra (lo cual violaría el encapsulamiento, ver *Ley de Demeter*). En la práctica, una buena orientación a objetos busca sistemas **modulares**, donde componentes independientes puedan desarrollarse y probarse por separado.

* **Encapsulación efectiva:** Sigue las convenciones de encapsulamiento para proteger la integridad de los objetos. Utiliza atributos privados (con `__` o `_`) para los datos que no deban ser manipulados directamente desde fuera, y ofrece métodos (getters/setters o propiedades) si es necesario exponerlos. Esto asegura que cualquier cambio de estado pase por un punto de control donde puedes validar o mantener invariantes. Además, no abuses de los métodos *setter*; si el diseño lo permite, es preferible crear objetos inmutables o establecer la mayor parte de su estado en el momento de la construcción. El encapsulamiento bien empleado contribuye a código más seguro y con menos errores, ya que cada objeto mantiene control sobre su propio estado.

* **Reutilización y herencia consciente:** Reutiliza código a través de herencia **solo cuando tiene sentido una relación jerárquica fuerte** ("es un tipo de"). La herencia mal utilizada puede complicar el diseño; por eso, evalúa si una subclase realmente extiende a la superclase cumpliendo el principio *Liskov* (la subclase debe poder usarse donde la superclase es esperada, sin alterar las propiedades deseadas del programa). Si la relación es débil (por ejemplo, un **Auto** *no es un* **Avión** aunque compartan algunos atributos como velocidad), tal vez es mejor encapsular atributos/métodos comunes en otra clase o usar composición. Considera el principio **"Composición sobre herencia"**: muchas veces es más flexible y claro que una clase contenga a otra (por ejemplo, una clase `Vehiculo` podría *tener* un objeto `Motor` en lugar de derivar de `Motor`). La composición evita algunas limitaciones de la herencia múltiple y reduce el acoplamiento entre clases base y derivadas.

* **Polimorfismo e interfaces consistentes:** Aprovecha el polimorfismo para escribir código más genérico y extensible. Diseña **interfaces coherentes** para tus clases – es decir, métodos con nombres y comportamientos esperados – de forma que distintas clases puedan intercambiarse con mínimo impacto. Por ejemplo, si varias clases distintas (p.ej. distintas formas geométricas) tienen un método común `calcular_area()`, podrás tratarlas polimórficamente sin saber su clase exacta. Asegúrate de documentar o respetar contratos: si implementas una interfaz (formal o informalmente), todas las clases que la compartan deben adherirse a su propósito. Esto evita sorpresas al usar objetos polimórficos. En Python, aunque no existen interfaces formales, es buena práctica garantizar que los métodos con el mismo nombre tengan semánticas compatibles.

* **SOLID y principios de diseño:** Familiarízate con los principios SOLID, que son cinco lineamientos clásicos para diseño OO de calidad:

  * *S*: **Responsabilidad Única** – cada clase debe tener un solo propósito o motivo de cambio.
  * *O*: **Abierto/Cerrado** – las clases deben estar abiertas a extensión pero cerradas a modificación (es decir, lograr que para agregar funcionalidad no tengamos que editar código existente, sino extenderlo).
  * *L*: **Sustitución de Liskov** – cualquier clase hija debe poder sustituir a su padre sin romper la lógica (las subclases respetan la interfaz esperada de la superclase).
  * *I*: **Segregación de Interfaces** – es mejor muchas interfaces específicas que una general gigante; los clientes no deberían depender de métodos que no usan.
  * *D*: **Inversión de Dependencias** – las clases de alto nivel no deberían depender de clases de bajo nivel directamente, ambas deberían depender de abstracciones; además, las abstracciones no deberían depender de detalles, sino al revés.

  Estos principios son *buenas prácticas* reconocidas que ayudan a escribir código más limpio, mantenible y fácil de escalar. Aunque puedan sonar teóricos al principio, aplicarlos evita problemas comunes en diseño de software.

* **Patrones de diseño y refactorización:** A medida que ganes experiencia, aprenderás soluciones típicas a problemas recurrentes en POO, conocidas como *patrones de diseño* (Singleton, Factory, Observer, etc.). Si bien no es necesario memorizarlos todos de entrada, sí es útil reconocer cuándo la estructura de tu código está adoptando uno y si es beneficioso. Por ejemplo, en Python el uso de **iteradores** o **generadores** se alinea con el patrón Iterator para recorrer colecciones. Otra buena práctica es la **refactorización constante**: mejora el diseño iterativamente. Si notas código duplicado, considera extraer una nueva clase o método. Si una clase hace “demasiado”, quizá deba dividirse. POO no garantiza buen diseño por sí sola; hay que trabajar en él, simplificando donde haga falta y abstrayendo donde convenga.

* **Legibilidad y estilo claro:** Finalmente, recuerda que un código orientado a objetos debe ser legible y comprensible. Usa nombres de clases y métodos descriptivos (p.ej. `Cliente.agregar_producto(carrito)` es más claro que `X.f(Y)`). Organiza las clases en módulos o paquetes de manera lógica (por funcionalidad o dominio) para que otros desarrolladores (y tu yo futuro) entiendan fácilmente la arquitectura. Aplica la convención de código de Python (PEP 8) para nombrar clases (PascalCase), métodos y atributos (snake\_case), etc., de modo que tu código siga un estándar familiar. Un diseño orientado a objetos bien pensado facilita que *“los programadores dediquen más tiempo a leer código que a escribirlo”*, por lo que es crucial priorizar la claridad y simplicidad por encima de trucos complicados.

En conclusión, la Programación Orientada a Objetos, cuando se domina y se aplican sus principios correctamente, permite **dividir un sistema complejo en componentes más simples (modularidad), reutilizar código** entre partes similares, y **adaptarse a cambios** de forma más ordenada. Siguiendo buenas prácticas como las mencionadas, los futuros ingenieros de software podrán construir sistemas orientados a objetos robustos, mantenibles y elegantes, cumpliendo con los objetivos de *flexibilidad* y *escalabilidad* que motivaron la creación de este paradigma.
