### RNF - 01 Conservación de expedientes clínicos
**Requisito:** Preservación en lugar de eliminación total de pacientes en el sistema.

**Descripción:**
El sistema deberá diseñarse para que la "eliminación" de pacientes no implique la eliminación completa de sus datos en la base de datos, por lo que será archivado, preservando el expediente clínico del paciente conservando: organizacón visual en el manejo de pacientes activos  del terapeuta y un expediente archivado y listo en caso de regreso del paciente.

**Restricciones:**

- El administrador será el que decide el estado del paciente
- No se permitirá la eliminación permanente de expedientes clínicos desde la interfaz del sistema, este mismo solo será agregado a archivación.
- El diseño deberá tener un atributo de estado (activo/archivado) con el fin de mantener separados los pacientes activos de los que ya fueron archivados.
- Los pacientes archivados no deberán aparecer en listados activos, por lo que maneja una lista de pacientes activos y otra la cual será de los pacientes archivados.
- La información archivada no podrá ser modificada, por lo que para poder ser modificada debera volver la lista de pacientes activos.

**Criterios de aceptación:**
- El sistema muestra un atributo de estado para el expediente de cada paciente.
- el sistema maneja listas para pacientes activos y archivados.
- El sistema impide la modificación de la información en el expediente del paciente una vez este ha sido archivado.
- El sistema debera de permitir el cambio de estado del paciente de activo a archivado y viceversa 

---

### RNF - 02 Control de acceso a expedientes clínicos basado en atributos
**Requisito:** El sistema deberá restringir el acceso a expedientes clínicos únicamente a usuarios con asignación válida al expediente correspondiente.

**Descripción:** 
Es de importancia mantener la confidencialidad de los expedientes para evitar el acceso, divulgación o consulta indebida de datos clínicos y personales, garantizando el cumplimiento de la _Norma Oficial Mexicana  NOM-024-SSA3-2010_. Para ello, se usará el framework _Java: Spring Security_ como mecanismo de seguridad para controlar la autorización antes de la ejecución de cada operación. Asimismo, se adoptará un enfoque _ABAC (attribute-based access control)_ dado que el acceso a los expedientes no depende únicamente del rol del usuario (terapeuta), sino de atributos específicos de este, como la asignación registrada entre el terapeuta y el expediente clínico. Por lo tanto, se definen las siguientes reglas de autorización:
- Se permitirá la consulta y modificación de expedientes únicamente cuando el terapeuta se encuentre asignado al identificador del expediente correspondiente.
- Se permitirá registrar nuevas sesiones clínicas únicamente a terapeutas asignados a dicho expediente.

**Restricciones:**
- La autorización debe hacerse antes de la ejecución de cualquier operación que conlleve tanto la consulta como modificación de los expedientes.
- Únicamente el rol permitido para interactuar con los expedientes será el terapeuta.

**Criterios de aceptación:**
- Cuando el usuario solicite consultar un expediente asignado a su identificador, el sistema deberá permitir su visualización.
- Si un usuario intenta acceder a un expediente que no esté asignado a su identificador, el sistema deberá denegar el acceso.
- Toda operación que implique tanto el acceso como la modificación de expedientes deberá validar las reglas de autorización antes de ejecutar la acción solicitada.

![Esquema](/Documentación/Diagramas/BPMN%20-%20RNF%2002.jpeg)

---

### RNF - 03 Aislamiento de datos clínicos por asignación

**Requisito:** Aislamiento de información clínica entre terapeutas.

**Descripción:**
El sistema deberá garantizar el aislamiento de los expedientes clínicos mediante controles de acceso basados en la asignación de pacientes a terapeutas, evitando que usuarios accedan a información clínica de pacientes que no forman parte de su ámbito de atención.

**Restricciones:**
- Las consultas a datos clínicos deberán aplicar filtrado por asignación en la capa de acceso a datos..
- El sistema no deberá permitir el acceso a expedientes clínicos de pacientes no asignados al terapeuta.
- La validación de asignación deberá realizarse en el backend para cada operación que implique acceso a información clínica.
- 
**Criterios de aceptación:**
- Las consultas realizadas por terapeutas retornan únicamente pacientes que les han sido asignados.
- Las solicitudes de acceso a expedientes de pacientes no asignados son rechazadas por el servidor.
- Las reglas de aislamiento de datos se aplican tanto en operaciones de lectura como de modificación.

---

### RNF - 04 Registro de eventos de auditoría en expedientes clínicos

**Descripción:**
El sistema deberá registrar, mediante un mecanismo en backend, todos los eventos sensibles relacionados con el acceso y gestión de expedientes clínicos y sus reportes asociados, con el fin de garantizar la trazabilidad de las acciones realizadas por los usuarios y detectar accesos no permitidos.

**Restricciones:**

- La auditoría deberá ejecutarse exclusivamente en backend.
- Los eventos auditables incluyen: consulta de expediente clínico, acceso denegado a expediente clínico, registro de entrevista socioeconómica, registro de informe de consentimiento, registro de reporte de sesión, modificación de reporte de sesión, envío de reporte de sesión para revisión, aprobación o rechazo de reporte de sesión.
- La fecha y hora del evento deberá registrarse en formato ISO 8601.
- El campo “resultado” deberá utilizar valores controlados: {EXITO, DENEGADO}.

**Criterios de aceptación:**

- Cada evento definido genera automáticamente un registro de auditoría.
- Se registran tanto accesos permitidos como denegados.
- No existen acciones auditables que se ejecuten sin generar un registro.
- El registro se realiza sin intervención del usuario.

--- 

### RNF-05 Estructura de los registros de auditoría

**Descripción:**  
El sistema deberá registrar los eventos de auditoría utilizando una estructura de datos estandarizada que permita identificar claramente las acciones realizadas, garantizando la integridad e inmutabilidad de los registros.

**Restricciones:**

- Cada registro debe incluir: id del usuario, rol del usuario, fecha y hora, acción realizada, recurso afectado, id del recurso y resultado de la operación.
- Los registros deberán almacenarse en formato estructurado.
- No se permitirá la modificación o eliminación de logs desde la interfaz del sistema.
- Los registros deben ser persistentes.
- Solo se permitirán operaciones de inserción sobre los logs.

**Criterios de aceptación:**

- Todos los registros contienen los campos definidos.
- No se almacenan registros incompletos.
- No existe funcionalidad para editar o eliminar logs desde el sistema.
- Los registros permanecen íntegros tras reinicios o actualizaciones.

--- 

### RNF-06 Auditoría de accesos a expedientes

**Descripción:**  
El sistema deberá registrar todos los intentos de acceso a expedientes clínicos, tanto exitosos como denegados, con el objetivo de mantener control sobre el acceso a información sensible.

**Restricciones:**

- Todo acceso debe generar un registro.
- Se debe registrar explícitamente el resultado de la operación.
- No se deben omitir intentos fallidos.
- No se deben almacenar datos clínicos sensibles completos dentro de los logs.

**Criterios de aceptación:**

- Se registra cada consulta de expediente clínico.
- Se registra cada intento de acceso denegado.
- Cada registro incluye usuario, rol, fecha y hora, acción, id del expediente y resultado.
- Los registros son accesibles para auditoría por usuarios autorizados.

--- 

### RNF-07 Trazabilidad de operaciones sobre reportes clínicos

**Descripción:**  
El sistema deberá registrar todas las operaciones realizadas sobre los reportes clínicos contenidos en los expedientes, permitiendo rastrear su ciclo de vida desde su creación hasta su aprobación o rechazo.

**Restricciones:**

- Todas las operaciones sobre reportes deben generar un registro.
- Los registros deben asociarse a un identificador único de reporte.
- El historial debe mantenerse íntegro y persistente.

**Criterios de aceptación:**

- Se registran los eventos de registro, modificación, envío para revisión y aprobación o rechazo de reportes.
- Cada registro incluye usuario, rol, fecha y hora, acción, id del reporte y resultado.
