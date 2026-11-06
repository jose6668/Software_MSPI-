# Matriz de permisos

Versión: 0.3 — Políticas APR-01 y APR-02 incorporadas
Fecha: 6 de noviembre de 2026
Base: [Parámetros y requerimientos](01-parametros-y-requerimientos.md)
Flujos: [Casos de uso](03-casos-de-uso.md)

## 1. Alcance y actores

Esta matriz desarrolla los cinco roles y la política aprobada en APR-01 del documento de requerimientos. Define autorizaciones funcionales; la estrategia de permisos de base de datos y los detalles todavía propuestos se establecerán posteriormente. La aprobación de roles no confirma metodologías ni tecnologías pendientes.

| Código | Actor | Alcance |
| --- | --- | --- |
| AP | Administrador de plataforma | Administración del servicio y del ciclo de vida de empresas |
| AE | Administrador de empresa | Administración y operación de una empresa donde tiene membresía |
| GS | Gestor de seguridad | Gestión operativa de seguridad en su empresa |
| RA | Responsable de acciones | Trabajo sobre acciones asignadas y su contexto mínimo |
| AU | Auditor o lector | Consulta de información operativa de su empresa |

Cada usuario tiene una membresía única por empresa, con uno o varios roles entre AE, GS, RA y AU. No se repiten códigos dentro del conjunto y una membresía activa nunca queda sin roles. AP es una atribución de plataforma independiente: si esa persona tiene además una membresía empresarial, sus permisos empresariales provienen exclusivamente de ella. No se suman permisos de otras empresas.

Para una acción se combinan las concesiones de los roles de la empresa activa y luego se aplican todas las restricciones transversales y de objeto/estado. Una celda «—» significa que ese rol no concede el permiso, no que anule una concesión válida de otro rol. Una prohibición explícita, como autoaprobar una tarea, se aplica a la persona y no puede superarse acumulando roles ni cambiando un selector de rol.

AU sin otros roles es de solo lectura. AU+GS puede editar por GS. RA sin otro rol de consulta solo ve tareas propias y contexto mínimo; RA+GS conserva la consulta operativa que GS concede. Un AE o GS puede ejecutar tareas asignadas sin añadir RA. La interfaz debe mostrar todos los roles asignados y el alcance efectivo, evitando presentar AU+GS como solo lectura.

## 2. Convenciones

- **P:** permitido dentro del alcance del rol y con las condiciones generales.
- **C:** permitido únicamente bajo la condición específica de la fila o sección 4.
- **—:** denegado. Una operación ausente de la matriz también se deniega hasta especificarla.
- «Su empresa» significa la empresa activa validada por el servidor, con membresía y estado vigentes.
- Un permiso de consulta no implica permiso de edición, descarga masiva, aprobación o administración.

Las restricciones de estado, validación y concurrencia de los casos de uso aplican incluso donde figura P.

## 3. Matriz de acciones

### 3.1. Plataforma, identidad y membresías

| ID | Acción | AP | AE | GS | RA | AU | Condición o límite |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PER-01 | Crear empresa y aprovisionar esquema | P | — | — | — | — | El AP designa al primer AE mediante invitación de activación |
| PER-02 | Consultar metadatos de empresas del servicio | P | — | — | — | — | Identificación, estado y versión técnica; no contenido operativo |
| PER-03 | Suspender o reactivar empresa | P | — | — | — | — | Motivo registrado; reactivación exige esquema compatible |
| PER-04 | Iniciar sesión, cerrarla y recuperar acceso propio | P | P | P | P | P | Acciones sobre la identidad propia; no permiten acceder a empresas por sí mismas |
| PER-05 | Listar y seleccionar membresías propias | C | P | P | P | P | AP solo si también tiene membresía; solo empresas activas y autorizadas |
| PER-06 | Consultar datos básicos de la empresa activa | — | P | P | C | P | RA recibe solo nombre y zona horaria para interpretar sus tareas |
| PER-07 | Editar datos y configuración empresarial | — | P | — | — | — | Sin cambiar metodología común, esquema, estado de servicio ni privilegios de plataforma |
| PER-08 | Listar miembros e invitaciones de la empresa | — | P | — | — | — | GS accede únicamente al selector mínimo de responsables de PER-12 |
| PER-09 | Invitar y revocar invitaciones | — | P | — | — | — | Rol empresarial válido; no crea roles AP |
| PER-10 | Asignar o retirar roles, o revocar membresía | — | C | — | — | — | Solo AE; conjunto empresarial válido no vacío si activa; conservar al menos una membresía activa que incluya AE; prohibido asignar AP |
| PER-11 | Aceptar invitación propia | C | C | C | C | C | Identidad verificada del destinatario y token vigente; no requiere membresía previa |

Iniciar sesión y solicitar recuperación son operaciones disponibles antes de estar autenticado. Aceptar una invitación se autoriza por identidad y token, no por el rol que se va a recibir. No hay registro público ni creación de empresas por usuarios empresariales.

### 3.2. Procesos, activos y catálogos

| ID | Acción | AP | AE | GS | RA | AU | Condición o límite |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PER-12 | Consultar procesos y selector mínimo de responsables | — | P | P | C | P | RA solo el contexto explícito de sus tareas; el selector no revela invitaciones ni privilegios administrativos |
| PER-13 | Crear, editar y archivar procesos y responsables | — | P | P | — | — | No modifica identidades ni membresías; preservar referencias históricas |
| PER-14 | Consultar, buscar y filtrar activos | — | P | P | — | P | RA no accede al inventario; su contexto se entrega por PER-29 |
| PER-15 | Crear y editar activos | — | P | P | — | — | Proceso y responsable válidos de la misma empresa |
| PER-16 | Registrar clasificación y calcular criticidad | — | P | P | — | — | APR-02: máximo C/I/D completo; datos faltantes quedan pendientes |
| PER-17 | Archivar y restaurar activos | — | P | P | — | — | Conservar riesgos y referencias; no es eliminación física |
| PER-18 | Consultar catálogo versionado de controles y cláusulas | — | P | P | — | P | La carga o actualización del catálogo pertenece al mantenimiento técnico, fuera de la interfaz inicial |

### 3.3. Evaluaciones y riesgos

| ID | Acción | AP | AE | GS | RA | AU | Condición o límite |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PER-19 | Consultar evaluaciones y resultados históricos | — | P | P | — | P | Solo evaluaciones de la empresa activa |
| PER-20 | Crear evaluación y diligenciar borrador | — | P | P | — | — | Identificar catálogo, metodología, fecha y responsable |
| PER-21 | Enviar evaluación a revisión, devolverla y cerrarla | — | C | C | — | — | Estado y requisitos de cierre válidos; devolución con motivo |
| PER-22 | Crear revisión de evaluación cerrada | — | P | P | — | — | Nuevo borrador vinculado; original inmutable |
| PER-23 | Consultar, buscar y filtrar riesgos | — | P | P | — | P | RA solo contexto mínimo por PER-29 |
| PER-24 | Crear y editar riesgos y su tratamiento | — | P | P | — | — | Editar tratamiento no permite aprobar aceptación reservada a PER-27 |
| PER-25 | Registrar valoraciones inherentes y residuales | — | P | P | — | — | APR-02: categorías justificadas y residual conjunto; sin descuentos automáticos |
| PER-26 | Vincular controles y registrar implementación o efectividad | — | P | P | — | — | Catálogo e implementación diferenciados; vínculos solo de su empresa |
| PER-27 | Aprobar aceptación de riesgo | — | C | — | — | — | Justificación, valoración vigente, aprobador y próxima revisión |
| PER-28 | Cambiar estado o cerrar riesgo | — | C | C | — | — | Cierre con valoración vigente y justificación; no sustituye PER-27 |

Para la primera versión está aprobado permitir a AE o GS revisar y cerrar una evaluación aunque la hayan diligenciado. La separación obligatoria de personas aplica a la aprobación de acciones, incluso con varios roles.

### 3.4. Acciones, evidencias y seguimiento

| ID | Acción | AP | AE | GS | RA | AU | Condición o límite |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PER-29 | Consultar acciones y contexto mínimo | — | P | P | C | P | RA solo acciones actualmente asignadas y contexto limitado de sección 4 |
| PER-30 | Crear, asignar o reasignar acciones | — | P | P | — | — | Responsable con membresía activa y rol AE, GS o RA |
| PER-31 | Editar plazo, alcance o cancelar acción | — | C | C | — | — | Motivo registrado; una completada conserva su cierre y no se reescribe |
| PER-32 | Registrar avance y solicitar revisión | — | C | C | C | — | Solo responsable actual; pendiente o en ejecución; la solicitud pasa a revisión |
| PER-33 | Aprobar o devolver acción | — | C | C | — | — | Solo en revisión, actor distinto del responsable; decisión con motivo |
| PER-34 | Cargar archivo o registrar referencia de evidencia | — | C | C | C | — | Objeto editable; referencia verificable si criterio lo admite; RA solo sus acciones editables |
| PER-35 | Consultar o descargar evidencia | — | C | C | C | C | Mismo acceso que al objeto; RA solo archivos explícitamente visibles en sus tareas |
| PER-36 | Consultar panel general y matriz de riesgos | — | P | P | — | P | RA usa su lista de tareas, sin agregados generales |
| PER-37 | Generar reporte básico empresarial | — | P | P | — | P | No amplía permisos ni incluye registro administrativo de actividad |
| PER-38 | Consultar registro de actividad empresarial | — | P | — | — | — | Solo eventos propios de la empresa; sin edición o eliminación desde la aplicación |

No hay eliminación física de registros operativos o evidencias en la interfaz inicial. La política de retención y la corrección de archivos adjuntados por error deben definirse antes de implementar su ciclo de vida; mientras tanto, no se presupone un permiso para eliminarlos.

## 4. Condiciones transversales

1. **Empresa y objeto:** comprobar sesión, membresía, estado empresarial, acción y pertenencia del objeto en cada solicitud. Aplicar igual criterio a consultas directas, búsquedas, descargas y reportes.
2. **Contexto concedido por RA:** incluir identificador y descripción de tarea, plazo, estado, responsable, avances, nombre del activo y resumen del riesgo o hallazgo necesario para ejecutarla. AE o GS selecciona las evidencias de contexto visibles. El permiso RA por sí solo no habilita navegación al registro completo ni otros riesgos, documentos o datos personales. Si existe GS/AE/AU, sus permisos de consulta adicionales se validan de forma independiente.
3. **Cambio de responsable:** el anterior pierde acceso por asignación y capacidad de enviar avances; los avances históricos conservan su autoría. El nuevo responsable ve el historial autorizado de la tarea.
4. **Revisión independiente:** se compara la identidad de la persona, no solo su rol. Un AE o GS responsable de la tarea no puede aprobarla. Si no hay otra persona con permiso, la acción permanece en revisión hasta que exista un revisor válido; no hay excepción automática.
5. **Revocación:** retirar membresía, añadir/retirar roles o suspender empresa afecta la siguiente solicitud, incluyendo sesiones abiertas y descargas nuevas. Al retirar un rol se recalcula el conjunto efectivo; un permiso aún concedido por otro rol válido permanece. No revoca documentos ya descargados ni datos previamente recibidos.
6. **Roles y privilegios:** se deniega por defecto; ningún formulario acepta libremente roles de plataforma o nombres de esquema. Las asignaciones de roles se validan en servidor.
7. **Sin mezcla de empresas:** los identificadores recibidos se resuelven en el contexto autorizado; no se reutiliza el contexto de otra empresa en conexiones, cachés o procesos en segundo plano.
8. **Cambios simultáneos:** comprobar la versión del registro y revalidar autorización en la operación. Informar conflicto antes de sobrescribir cambios ajenos.
9. **Historial:** registrar cambios de negocio y permisos con actor, empresa, objeto, fecha y motivo cuando corresponda, sin copiar secretos o documentos completos al log.
10. **Responsables de negocio:** un custodio o propietario indicado en un activo no obtiene permisos de acceso por aparecer en el registro. Los permisos provienen de la membresía. Una acción sí exige un responsable que pueda acceder al sistema.

## 5. Escenarios de verificación de permisos

| ID | Escenario | Resultado esperado |
| --- | --- | --- |
| PA-01 | Usuario de A solicita un activo, riesgo o evidencia de B con ID conocido | Denegación sin contenido ni confirmación de datos sensibles de B |
| PA-02 | Usuario AE en A y AU en B cambia a B e intenta editar | Denegación; no conserva el rol de A |
| PA-03 | AP sin membresía intenta leer un riesgo de una empresa | Denegación; su privilegio de plataforma no concede lectura operativa |
| PA-04 | Usuario con solo RA consulta activos o una tarea no asignada | Denegación; solo lista y detalle de sus tareas |
| PA-05 | Responsable AE o GS intenta aprobar su tarea | Denegación aunque tenga permiso de revisión en otras tareas |
| PA-06 | Se revoca membresía o se suspende empresa con sesión abierta | Siguiente solicitud empresarial rechazada |
| PA-07 | AE revoca o degrada al último AE activo, incluso con solicitudes simultáneas | Se conserva al menos un AE activo |
| PA-08 | Se alternan peticiones de A y B reutilizando conexiones o cachés | Cada respuesta contiene exclusivamente datos de la empresa autorizada |
| PA-09 | Usuario con solo AU intenta cargar evidencia o cambiar estado | Denegación sin modificaciones |
| PA-10 | Responsable anterior intenta consultar tarea después de reasignarla | Denegación si no tiene otro permiso válido para consultarla |
| PA-11 | Usuario intenta reutilizar invitación vencida, usada o revocada | No crea ni modifica membresía |
| PA-12 | Usuario GS sin AE intenta gestionar miembros o aceptar formalmente un riesgo | Denegación; acciones reservadas a AE |
| PA-13 | Usuario con solo RA modifica reporte o descarga evidencia ajena | Denegación sin exposición por reportes o archivos |
| PA-14 | Usuario AU+GS edita un activo autorizado | Permitido por GS; AU no anula esa concesión |
| PA-15 | Usuario GS+RA intenta aprobar su propia tarea | Denegación por identidad, aunque reúna permisos de ejecución y revisión |
| PA-16 | Se retira GS a una membresía AU+GS | Queda AU y pierde edición en la siguiente solicitud |
| PA-17 | Se intenta retirar el último rol o AE del último administrador activo | Rechazo; activa exige roles y la empresa conserva AE |
| PA-18 | Usuario AE+GS en A y solo AU en B intenta editar en B | Denegación; no combina roles de empresas distintas |

## 6. Trazabilidad y decisiones pendientes

- PER-01 a PER-11: RF-01 a RF-05 y RF-22.
- PER-12 a PER-18: RF-06 a RF-09.
- PER-19 a PER-28: RF-10 a RF-14, RN-09 y estados de riesgo.
- PER-29 a PER-35: RF-15 a RF-17.
- PER-36 a PER-38: RF-18 a RF-21.
- Todas las filas: SEG-01 a SEG-10 según su aplicación y RN-01 a RN-11.

APR-01 aprueba alcance y roles multirrol; APR-02 aprueba decisiones de cálculo, evidencia mínima y ciclos de vida descritas en el documento 1. El detalle de contexto mínimo de RA no cubierto por esas aprobaciones sigue en revisión. Quedan pendientes canal de invitación/recuperación, retención, rúbricas y evidencia por criterio; los mínimos de cierre sí están aprobados.
