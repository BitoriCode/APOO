Enunciado 1: Parqueadero Inteligente

Se quiere construir una aplicación llamada Parqueadero Inteligente que permita administrar la entrada y salida de vehículos en un parqueadero, llevando control de la ocupación, los tiquetes de estacionamiento y el cobro al momento de la salida.

El parqueadero tiene una capacidad máxima de cupos. Cuando un vehículo ingresa, el sistema debe registrar la entrada, asignar un cupo disponible y generar un tiquete con un identificador único. Un mismo vehículo puede ingresar múltiples veces en diferentes momentos (cada ingreso genera un tiquete nuevo). Al salir, el sistema debe calcular el valor a pagar con base en el tiempo transcurrido y una tarifa por hora (o fracción), registrar la salida y liberar el cupo.

La aplicación debe permitir, como mínimo:

registrar entrada de vehículo,

registrar salida y calcular cobro,

consultar cupos disponibles y ocupados,

buscar un tiquete por su ID,

listar los vehículos actualmente estacionados (tiquetes activos).


Datos mínimos a manejar

De cada vehículo:

Placa (identificador del vehículo).

Tipo (por ejemplo: carro, moto).


De cada tiquete:

ID único del tiquete.

Vehículo asociado.

Fecha/hora de entrada.

Fecha/hora de salida (cuando aplique).

Estado (activo o cerrado).

Valor pagado (cuando se cierra).


Del parqueadero:

Capacidad total de cupos.

Tarifa por hora (puede ser general o por tipo de vehículo).


Restricciones y consideraciones

No se puede registrar entrada si el parqueadero está lleno.

No se puede registrar salida de un tiquete que no exista o que ya esté cerrado.

Un tiquete activo representa un vehículo actualmente estacionado.

No se deben usar excepciones (raise). Si ocurre un error, la operación no se ejecuta y se informa con un mensaje o un valor de retorno.



---

Enunciado 2: Clínica Veterinaria

Se quiere construir una aplicación llamada Clínica Veterinaria que permita gestionar propietarios, mascotas, veterinarios y citas médicas. La clínica debe poder registrar a los propietarios, registrar las mascotas de cada propietario, agendar citas con veterinarios, cancelar citas y registrar la atención de una cita.

Cuando un propietario solicita una cita para su mascota, el sistema debe verificar disponibilidad básica: que el veterinario exista y que no se duplique una cita en la misma fecha y hora para el mismo veterinario. Una cita puede ser cancelada. Cuando se realiza la atención, se debe guardar un resumen (motivo, observaciones/diagnóstico) y el costo del servicio, dejando la cita con estado realizada. El sistema también debe permitir consultar la agenda por fecha y el historial de atenciones de una mascota.

La aplicación debe permitir, como mínimo:

registrar propietario,

registrar mascota asociada a un propietario,

registrar veterinario,

agendar cita,

cancelar cita,

registrar atención de una cita,

consultar agenda por fecha,

consultar historial de una mascota.


Datos mínimos a manejar

De cada propietario:

ID único (por ejemplo, documento o correo).

Nombre.

Contacto (teléfono y/o correo).


De cada mascota:

ID único de mascota.

Nombre.

Especie (perro, gato, etc.).

Propietario asociado.


De cada veterinario:

ID único.

Nombre.

Especialidad (opcional).


De cada cita:

ID único de cita.

Fecha y hora.

Mascota asociada.

Veterinario asociado.

Estado (programada, cancelada, realizada).

Motivo.

Observaciones/diagnóstico (cuando se realiza).

Costo (cuando se realiza).


Restricciones y consideraciones

No debe haber dos propietarios con el mismo ID.

No debe haber dos mascotas con el mismo ID.

No debe haber dos veterinarios con el mismo ID.

No se debe permitir agendar dos citas para el mismo veterinario en la misma fecha y hora.

No se puede “realizar” una cita cancelada.

No se deben usar excepciones (raise). Si ocurre un error, la operación no se ejecuta y se informa con un mensaje o un valor de retorno.
