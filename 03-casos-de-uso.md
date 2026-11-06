# Casos de uso

Versión: 0.1 — Especificación inicial para revisión
Fecha: 6 de noviembre de 2026
Base: [Parámetros y requerimientos](01-parametros-y-requerimientos.md)
Autorización: [Matriz de permisos](02-matriz-de-permisos.md)

## 1. Convenciones y condiciones comunes

Actores: AP = administrador de plataforma; AE = administrador de empresa; GS = gestor de seguridad; RA = responsable de acciones; AU = auditor o lector. El sistema ejecuta validaciones y cálculos; no reemplaza al usuario que decide o aporta evidencia.

Salvo los casos de identidad y administración de plataforma, todos los flujos requieren sesión válida, empresa activa y membresía vigente. El servidor comprueba permiso y acceso al objeto en cada operación. Rechazar el acceso no revela datos empresariales ajenos. Listados y búsquedas usan paginación y los mismos permisos que el detalle. Las mutaciones conservan historial y rechazan conflictos de edición sin sobrescribir silenciosamente.

Las validaciones se completan antes de confirmar la operación. Los pasos y excepciones son criterios funcionales, no una elección de interfaz ni de tecnología. Cada caso incluye un escenario mínimo que servirá de base a pruebas posteriores; todavía no representa una prueba ejecutada.

## 2. Plataforma y acceso

### CU-01. Registrar empresa y aprovisionar esquema

- **Actor y objetivo:** AP crea un espacio empresarial separado y habilita a su primer administrador.
- **Previas:** sesión de plataforma válida; empresa y correo de contacto identificados; configuración técnica disponible.
- **Flujo:** 1) AP registra los datos mínimos y destinatario inicial. 2) El sistema genera un identificador interno de esquema y crea la empresa en preparación. 3) Aprovisiona estructura, versión y configuración. 4) Emite la invitación inicial de AE y activa la empresa únicamente con aprovisionamiento completo.
- **Alternativas:** datos duplicados o inválidos se rechazan; un fallo deja estado de error y acceso empresarial bloqueado. Un reintento controlado no duplica empresa o esquema. La invitación no concede acceso hasta su aceptación; si su emisión falla, AP puede reintentar el alta inicial sin leer contenido operativo.
- **Resultado:** empresa activa y esquema propio listo; invitación inicial pendiente o fallo identificable sin acceso parcial.
- **Verificación:** un fallo intermedio nunca permite consultar tablas incompletas ni datos de otra empresa.
- **Trazabilidad:** RF-01; SEG-03, SEG-05; PER-01, PER-02, PER-11.

### CU-02. Suspender o reactivar empresa

- **Actor y objetivo:** AP controla la disponibilidad de una empresa.
- **Previas:** empresa existente y sesión de plataforma autorizada.
- **Flujo:** 1) Consulta metadatos técnicos. 2) Selecciona suspensión o reactivación y registra motivo. 3) El sistema valida estado y compatibilidad del esquema. 4) Aplica el cambio y registra el evento de plataforma.
- **Alternativas:** una empresa incompleta o incompatible no se reactiva; repetir una suspensión no duplica efectos; no se elimina contenido.
- **Resultado:** la siguiente solicitud empresarial refleja el nuevo estado. AP conserva acceso a sus funciones de plataforma.
- **Verificación:** una sesión abierta antes de suspender no permite nuevas operaciones en esa empresa.
- **Trazabilidad:** RF-05; PER-02, PER-03; PA-06.

### CU-03. Iniciar y cerrar sesión

- **Actor y objetivo:** usuario identificado accede a su cuenta y termina su sesión.
- **Previas:** cuenta habilitada para iniciar sesión; sesión vigente para cerrarla.
- **Flujo:** 1) Envía credenciales. 2) El sistema verifica identidad y controles de intentos. 3) Crea sesión y presenta los accesos autorizados. 4) Al cerrar sesión invalida su uso posterior en servidor.
- **Alternativas:** credenciales inválidas producen mensaje genérico; usuario sin membresías no accede a información empresarial; empresa suspendida no aparece como operable. La caducidad exige autenticación nueva.
- **Resultado:** sesión válida o sesión finalizada; iniciar sesión no asigna membresías.
- **Verificación:** reutilizar una sesión cerrada para una operación protegida falla.
- **Trazabilidad:** RF-02; RNF-01; PER-04.

### CU-04. Recuperar acceso propio

- **Actor y objetivo:** titular de cuenta restablece sus credenciales.
- **Previas:** canal de recuperación verificado y configurado; su selección está pendiente.
- **Flujo:** 1) Solicita recuperación. 2) El sistema responde sin revelar si la cuenta existe. 3) Entrega un código o enlace temporal por el canal verificado. 4) Valida el comprobante y la nueva credencial. 5) Actualiza la credencial, consume el comprobante e invalida sesiones anteriores.
- **Alternativas:** comprobante vencido, usado o inválido no cambia credenciales; limitar intentos y solicitudes; un fallo de entrega no permite saltar la verificación.
- **Resultado:** credencial restablecida; roles y membresías permanecen sujetos a su vigencia, sin reactivación automática.
- **Verificación:** el mismo comprobante no funciona dos veces y no permite acceder a una empresa suspendida.
- **Trazabilidad:** RF-22; RNF-01; PER-04.

### CU-05. Seleccionar o cambiar empresa activa

- **Actor y objetivo:** usuario con membresías trabaja en una empresa autorizada.
- **Previas:** sesión válida y al menos una membresía operable.
- **Flujo:** 1) Consulta sus empresas disponibles. 2) Selecciona una. 3) El servidor valida membresía y estado y resuelve el esquema interno. 4) La interfaz actualiza contexto, permisos, listas y formularios.
- **Alternativas:** una empresa no autorizada o suspendida se rechaza; avisar antes de descartar cambios locales sin guardar. Una solicitud conserva su contexto explícito aunque se cambie de empresa en otra pestaña.
- **Resultado:** nuevas operaciones se realizan en la empresa seleccionada con su rol correspondiente.
- **Verificación:** AE en A y AU en B no puede editar en B, ni se guardan formularios de A accidentalmente en B.
- **Trazabilidad:** RF-04; SEG-01 a SEG-04, SEG-07; PER-05; PA-02, PA-08.

### CU-06. Consultar y configurar la empresa

- **Actor y objetivo:** AE actualiza datos básicos y zona horaria; AE, GS o AU los consultan; RA recibe solo el contexto mínimo permitido.
- **Previas:** condiciones comunes.
- **Flujo:** 1) Consulta información autorizada. 2) AE edita campos permitidos. 3) El sistema valida y guarda con historial. 4) Explica el efecto de la zona horaria en las fechas límite.
- **Alternativas:** no acepta nombre de esquema, privilegios, estado de plataforma o metodología común como campos editables; conflicto de edición exige revisar datos vigentes.
- **Resultado:** configuración empresarial actualizada sin cambiar identidad o aislamiento del esquema; eventos históricos conservan su marca temporal.
- **Verificación:** GS puede consultar, pero una solicitud directa para cambiar configuración se rechaza.
- **Trazabilidad:** alcance de información empresarial; RN-11; PER-06, PER-07. Esta edición precisa la configuración incluida en el alcance y se propone incorporarla como RF explícito en una revisión futura.

### CU-07. Invitar miembro y aceptar invitación

- **Actor y objetivo:** AE incorpora un miembro; el destinatario acepta su propia invitación. CU-01 cubre la emisión inicial por AP.
- **Previas:** AE autorizado; destinatario y rol empresarial válidos; canal de invitación pendiente de definición técnica.
- **Flujo:** 1) AE registra destinatario y rol. 2) El sistema genera invitación de un solo uso con vencimiento. 3) El destinatario verifica su identidad y acepta. 4) El sistema crea o utiliza su identidad y establece la membresía en la empresa de la invitación.
- **Alternativas:** otro destinatario no puede aceptar; invitación usada, vencida o revocada falla; empresa suspendida bloquea aceptación hasta reactivarse si sigue vigente. Una membresía existente no cambia de rol por aceptar un enlace antiguo; se usa CU-08.
- **Resultado:** miembro con el rol concedido únicamente en la empresa invitante.
- **Verificación:** modificar el rol o empresa del formulario no amplía la invitación; la segunda aceptación no duplica membresías.
- **Trazabilidad:** RF-03; PER-08, PER-09, PER-11; PA-11.

### CU-08. Administrar membresías e invitaciones

- **Actor y objetivo:** AE consulta miembros, cambia roles o revoca membresías e invitaciones.
- **Previas:** condiciones comunes; miembro o invitación de su empresa.
- **Flujo:** 1) Selecciona registro y operación. 2) Confirma cambio y motivo. 3) El sistema verifica rol permitido y existencia de al menos un AE activo. 4) Guarda el cambio e historial y aplica permisos a solicitudes posteriores.
- **Alternativas:** no revoca ni degrada al último AE; no asigna AP; controla cambios simultáneos. Si el miembro tiene tareas abiertas, las señala para reasignación, pero no conserva acceso revocado ni pierde el historial.
- **Resultado:** membresía o invitación actualizada solo en esa empresa; otras membresías siguen intactas.
- **Verificación:** dos revocaciones simultáneas no dejan la empresa sin AE; la sesión del revocado pierde acceso.
- **Trazabilidad:** RF-03, RF-05; RN-02; PER-08 a PER-10; PA-06, PA-07.

## 3. Inventario y catálogos

### CU-09. Gestionar procesos y responsables de negocio

- **Actor y objetivo:** AE o GS mantiene procesos y responsables para asociarlos a registros; AU consulta y RA accede a contexto limitado.
- **Previas:** condiciones comunes; edición reservada a AE y GS.
- **Flujo:** 1) Consulta catálogo empresarial. 2) Crea o edita nombre, descripción y responsable. 3) El sistema valida pertenencia y guarda. 4) Puede archivar registros sin destruir referencias previas.
- **Alternativas:** un proceso archivado no se asigna a registros nuevos; los registros existentes conservan su referencia; no se modifican cuentas o roles desde este módulo.
- **Resultado:** información de procesos y responsables disponible para los módulos de la empresa.
- **Verificación:** seleccionar un proceso de otra empresa se rechaza, aunque su identificador sea válido.
- **Trazabilidad:** RF-06; RN-08; PER-12, PER-13.

### CU-10. Registrar, editar y consultar activo

- **Actor y objetivo:** AE o GS mantiene el inventario; AU consulta.
- **Previas:** condiciones comunes; proceso y responsable válidos para el registro.
- **Flujo:** 1) Consulta inventario o inicia registro. 2) Introduce código, nombre, tipo, proceso, responsable y descripción. 3) El sistema comprueba campos y unicidad empresarial. 4) Guarda el activo y muestra su ficha e historial de cambios autorizado.
- **Alternativas:** código duplicado en esa empresa se rechaza; el mismo código en otra empresa es válido; referencias ajenas o archivadas para nuevas asignaciones se rechazan.
- **Resultado:** activo guardado en su empresa sin alterar otras; AU no puede guardar cambios.
- **Verificación:** registrar el mismo código en A y B funciona, duplicarlo dentro de A falla.
- **Trazabilidad:** RF-07, RF-19; RN-03; PER-14, PER-15; PA-01, PA-09.

### CU-11. Clasificar activo y determinar criticidad

- **Actor y objetivo:** AE o GS valora confidencialidad, integridad y disponibilidad y registra atributos de información aplicables.
- **Previas:** activo editable; metodología de criticidad versionada y definida antes de implementar.
- **Flujo:** 1) Selecciona valoraciones y completa clasificación y campos aplicables de datos personales. 2) El sistema valida escalas. 3) Calcula criticidad mediante reglas identificadas. 4) Guarda entradas, resultado, fecha y autor.
- **Alternativas:** faltantes producen clasificación pendiente; valores fuera de escala se rechazan; no se inventan valores para completar el cálculo.
- **Resultado:** clasificación trazable sin convertir ausencia de información en un nivel bajo.
- **Verificación:** casos límite y combinaciones de la metodología coinciden con resultados de referencia; el cálculo queda bloqueado como pendiente si falta un dato requerido.
- **Trazabilidad:** RF-08; RN-07, RN-10; PER-16.

### CU-12. Archivar o restaurar activo

- **Actor y objetivo:** AE o GS retira un activo del inventario activo o lo restaura.
- **Previas:** activo de su empresa; estado compatible.
- **Flujo:** 1) Revisa referencias y riesgos abiertos. 2) Registra motivo y confirma operación. 3) El sistema cambia estado y conserva historial y vínculos.
- **Alternativas:** archivar no cierra riesgos o tareas; un activo archivado no se ofrece para nuevos riesgos hasta restaurarse; no se elimina físicamente.
- **Resultado:** estado actualizado y referencias históricas consultables por quienes tienen permiso.
- **Verificación:** archivar un activo con riesgo mantiene el riesgo y su información, mostrando el activo como archivado.
- **Trazabilidad:** RF-07; RN-08; PER-17.

### CU-13. Consultar catálogo de controles y cláusulas

- **Actor y objetivo:** AE, GS o AU conoce los elementos que se evalúan o relacionan con riesgos.
- **Previas:** condiciones comunes; catálogo disponible y versionado.
- **Flujo:** 1) Selecciona versión. 2) Filtra por dominio, código o texto. 3) Consulta descripción e identificador. 4) AE o GS usa el elemento como referencia en una evaluación o implementación.
- **Alternativas:** una versión antigua sigue disponible para consultar registros históricos; consultar no permite editar el catálogo ni valoraciones empresariales.
- **Resultado:** referencia inequívoca de control o cláusula; sin alterar evaluaciones existentes.
- **Verificación:** dos versiones con código semejante se distinguen y un histórico mantiene la original.
- **Trazabilidad:** RF-09, RF-19; RN-04, RN-07; PER-18.

## 4. Evaluaciones

### CU-14. Crear evaluación periódica

- **Actor y objetivo:** AE o GS inicia un diagnóstico sin sobrescribir anteriores.
- **Previas:** catálogo y metodología identificados; responsable autorizado de su empresa.
- **Flujo:** 1) Indica fecha, alcance y responsable. 2) Selecciona versión aplicable. 3) El sistema crea borrador con elementos sin evaluar. 4) Presenta avance y pendientes.
- **Alternativas:** no se precargan calificaciones anteriores como si fueran evidencia actual; una versión no disponible bloquea el alta con explicación.
- **Resultado:** nueva evaluación independiente en borrador.
- **Verificación:** crear una segunda evaluación no modifica resultados de la primera.
- **Trazabilidad:** RF-10; PER-19, PER-20.

### CU-15. Diligenciar evaluación y registrar hallazgos

- **Actor y objetivo:** AE o GS registra valoraciones de cláusulas y controles y documenta brechas para su seguimiento.
- **Previas:** evaluación en borrador y elementos versionados disponibles.
- **Flujo:** 1) Selecciona elemento. 2) Registra aplicabilidad, calificación, observaciones, evidencia y, cuando corresponda, brecha y recomendación. 3) El sistema valida y guarda. 4) Actualiza avance y resultados con la metodología de agregación definida.
- **Alternativas:** «no aplica» exige motivo; «sin evaluar» no equivale a cero; faltantes permanecen visibles. Una evaluación en revisión o cerrada no permite edición directa. Una brecha se conserva vinculada al elemento para crear una acción con CU-22.
- **Resultado:** respuestas y hallazgos empresariales trazables; AU puede consultar mediante CU-26.
- **Verificación:** distinguir cero, pendiente y no aplicable al calcular y mostrar resultados.
- **Trazabilidad:** RF-11, RF-17; RN-05; PER-20, PER-34, PER-35.

### CU-16. Revisar, cerrar o corregir evaluación

- **Actor y objetivo:** AE o GS revisa un diagnóstico y conserva su historial.
- **Previas:** evaluación existente; criterios de completitud y evidencia definidos antes de habilitar cierre.
- **Flujo:** 1) Envía borrador a revisión. 2) Revisor autorizado verifica respuestas y evidencias. 3) Devuelve a borrador con motivo o cierra si cumple requisitos. 4) El sistema conserva versión de catálogo y metodología. 5) Si requiere corrección posterior, crea revisión vinculada en borrador conservando el original.
- **Alternativas:** faltantes impiden cierre y se enumeran; la revisión vinculada identifica respuestas heredadas y exige confirmarlas o actualizarlas antes del nuevo cierre; el autor puede revisar según la política inicial documentada en la matriz.
- **Resultado:** evaluación cerrada inmutable o borrador devuelto/revisión nueva trazable.
- **Verificación:** editar una evaluación cerrada por solicitud directa falla; corregirla genera otro registro vinculado.
- **Trazabilidad:** RF-10, RF-11; RN-07; PER-21, PER-22.

## 5. Riesgos y controles

### CU-17. Registrar y analizar riesgo

- **Actor y objetivo:** AE o GS documenta un riesgo asociado a un activo principal.
- **Previas:** activo vigente de su empresa; responsable de negocio válido.
- **Flujo:** 1) Selecciona activo. 2) Registra amenaza, vulnerabilidad, descripción, responsable y tratamiento propuesto. 3) El sistema valida vínculos y guarda en estado identificado. 4) El usuario completa el análisis y solicita transición a en análisis según campos requeridos.
- **Alternativas:** activo ajeno o archivado para nuevo registro se rechaza; aceptar riesgo exige CU-20 y no se aprueba seleccionando un tratamiento en el formulario.
- **Resultado:** riesgo identificado y vinculado a su activo sin compartir datos entre empresas.
- **Verificación:** no puede vincular riesgo de A a activo de B.
- **Trazabilidad:** RF-12; SEG-06; PER-23, PER-24.

### CU-18. Valorar riesgo inherente y residual

- **Actor y objetivo:** AE o GS registra y compara valoraciones mediante una metodología común versionada.
- **Previas:** riesgo accesible; reglas de escalas y combinación de controles acordadas antes de implementar.
- **Flujo:** 1) Registra probabilidad e impacto inherentes y su fundamento. 2) El sistema calcula el nivel. 3) El usuario registra controles efectivamente implementados y su evaluación. 4) Calcula o registra el residual de acuerdo con la metodología. 5) Guarda entradas, resultado, autor, fecha y versión.
- **Alternativas:** si falta información el residual queda pendiente; controles propuestos no reducen el nivel. La combinación de varios controles no se asume como una suma automática de porcentajes. Un cambio posterior conserva valoraciones anteriores y marca la necesidad de revisión sin reescribirlas.
- **Resultado:** valoraciones justificadas y matriz de riesgo alimentada con resultados vigentes identificables.
- **Verificación:** límites de probabilidad e impacto coinciden con casos de referencia; agregar una propuesta de control no disminuye por sí sola el riesgo.
- **Trazabilidad:** RF-13, RF-14; RN-06, RN-07, RN-10; PER-25.

### CU-19. Asociar y evaluar implementación de controles

- **Actor y objetivo:** AE o GS vincula controles y documenta su aplicación empresarial.
- **Previas:** riesgo accesible y referencia de catálogo válida.
- **Flujo:** 1) Selecciona control. 2) Crea o vincula una implementación de su empresa. 3) Registra estado, responsable, evidencia y efectividad. 4) La relaciona con uno o más riesgos autorizados. 5) Solicita revisar residual mediante CU-18 cuando corresponda.
- **Alternativas:** una referencia del catálogo no prueba implementación; evitar duplicar el mismo vínculo; cambios en una implementación compartida señalan todos los riesgos relacionados para revisión, sin alterar silenciosamente sus valoraciones.
- **Resultado:** relación muchos a muchos trazable entre riesgos e implementaciones empresariales.
- **Verificación:** una implementación puede atender dos riesgos de A, pero no un riesgo de B.
- **Trazabilidad:** RF-14, RF-17; RN-04, RN-06; PER-26, PER-34.

### CU-20. Aprobar aceptación de riesgo

- **Actor y objetivo:** AE aprueba formalmente aceptar un riesgo.
- **Previas:** valoración vigente, justificación y próxima fecha de revisión; riesgo de su empresa.
- **Flujo:** 1) Revisa riesgo y controles. 2) Registra fundamento de aceptación y fecha de revisión. 3) Confirma decisión. 4) El sistema comprueba permiso y conserva aprobador y fecha.
- **Alternativas:** GS puede proponer tratamiento, pero no aprobar aceptación; faltantes impiden confirmar; un cambio relevante exige nueva revisión sin borrar la decisión previa.
- **Resultado:** decisión de aceptación registrada; no cierra automáticamente el riesgo.
- **Verificación:** una solicitud de GS para aprobar se rechaza y no cambia la decisión.
- **Trazabilidad:** RF-12; RN-09; PER-27; PA-12.

### CU-21. Dar seguimiento y cerrar riesgo

- **Actor y objetivo:** AE o GS actualiza el estado y documenta el cierre.
- **Previas:** estado compatible y datos del análisis/tratamiento disponibles; cierre exige valoración vigente y justificación.
- **Flujo:** 1) Revisa valoración, controles y acciones. 2) Solicita transición de estado. 3) El sistema comprueba campos obligatorios de la transición. 4) Actor autorizado aprueba cierre con fundamento o mantiene seguimiento.
- **Alternativas:** completar tareas o aceptar riesgo no provoca cierre automático; se bloquea el cierre con valoración pendiente o acciones de tratamiento abiertas. Un riesgo cerrado conserva historial; un nuevo evento se documenta como nuevo riesgo relacionado, sin reescribir el cierre.
- **Resultado:** estado e historial actualizados; la aceptación formal sigue reservada a AE.
- **Verificación:** intentar cerrar sin valoración vigente muestra pendientes y no cambia estado.
- **Trazabilidad:** RF-12, RF-13; estados de riesgo; PER-28. El bloqueo por acciones abiertas es una precisión propuesta para el cierre inicial.

## 6. Acciones y evidencias

### CU-22. Crear, asignar y ajustar acción de mejora

- **Actor y objetivo:** AE o GS convierte un riesgo o hallazgo en una tarea verificable.
- **Previas:** origen válido de la misma empresa y responsable activo con rol AE, GS o RA.
- **Flujo:** 1) Selecciona origen y describe resultado esperado. 2) Define responsable, plazo y evidencia requerida. 3) El sistema guarda en pendiente. 4) Puede ajustar plazo, alcance, contexto visible o responsable con motivo; puede cancelar justificadamente antes de completarse.
- **Alternativas:** responsable de otra empresa o sin membresía vigente se rechaza; reasignar retira acceso por asignación al anterior; una tarea completada no se reescribe y una mejora posterior se registra como nueva acción relacionada.
- **Resultado:** acción con origen, plazo y responsable trazables; el vencimiento es indicador independiente del estado.
- **Verificación:** tarea atrasada sigue mostrando su estado real y el anterior responsable pierde acceso si no tiene otro permiso de consulta.
- **Trazabilidad:** RF-15; PER-29 a PER-31; PA-10.

### CU-23. Ejecutar acción y solicitar revisión

- **Actor y objetivo:** responsable actual, con rol AE, GS o RA, informa avance y entrega resultado.
- **Previas:** acción pendiente o en ejecución, asignación vigente y acceso autorizado.
- **Flujo:** 1) Consulta tarea y contexto permitido. 2) Pasa a ejecución y registra avances. 3) Adjunta evidencia requerida. 4) Solicita revisión. 5) El sistema valida completitud y cambia a en revisión.
- **Alternativas:** evidencia faltante impide solicitar revisión; otro miembro no puede enviar avances por él. En revisión no se edita la entrega hasta que sea devuelta. RA no modifica alcance, responsable, plazo ni valoración de riesgo.
- **Resultado:** entrega preparada para revisión por otra persona; el riesgo no cambia automáticamente.
- **Verificación:** RA ve su contexto mínimo, pero no puede abrir inventario o riesgo completo mediante enlaces o solicitudes directas.
- **Trazabilidad:** RF-15 a RF-17; PER-29, PER-32, PER-34; PA-04, PA-13.

### CU-24. Aprobar o devolver acción

- **Actor y objetivo:** AE o GS distinto del responsable verifica el resultado.
- **Previas:** acción en revisión y entrega disponible; identidad revisora diferente del responsable.
- **Flujo:** 1) Consulta resultado y evidencia. 2) Aprueba o devuelve con motivo. 3) El sistema revalida asignación, estado e identidad. 4) Marca completada o regresa a ejecución y registra decisión.
- **Alternativas:** autoaprobación se rechaza incluso si el responsable es AE o GS; sin revisor válido permanece en revisión; si otra persona ya decidió se informa conflicto.
- **Resultado:** tarea completada o devuelta con trazabilidad; la efectividad del control y el residual se revisan por separado.
- **Verificación:** responsable GS no aprueba su propia tarea y otro GS autorizado sí puede hacerlo.
- **Trazabilidad:** RF-16; RN-06; PER-33; PA-05.

### CU-25. Cargar, consultar y descargar evidencias

- **Actor y objetivo:** AE o GS adjunta evidencia a objetos editables; responsable actual aporta evidencia de su acción; usuarios con lectura autorizada la consultan.
- **Previas:** acceso al objeto y estado compatible con carga; reglas de archivo acordadas. Descarga exige autorización vigente independientemente de conocer el ID o enlace.
- **Flujo:** 1) Solicita carga o consulta desde objeto autorizado. 2) Para carga, el servidor valida tamaño, formato, contenido y destino. 3) Guarda archivo privado y metadatos de empresa, objeto y autor. 4) Para consulta o descarga, revalida permisos y entrega el archivo sin enlace público permanente.
- **Alternativas:** formato o tamaño inválidos se rechazan; fallo de guardado no deja evidencia disponible incompleta. RA solo descarga su evidencia y contexto explícitamente autorizado. Un rechazo no revela existencia o nombre de archivo ajeno.
- **Resultado:** evidencia trazable y protegida; no existe borrado físico en esta versión.
- **Verificación:** AU descarga evidencia operativa autorizada, pero no puede cargarla; usuario de otra empresa no puede descargar con el mismo identificador.
- **Trazabilidad:** RF-17; SEG-07 a SEG-09; RNF-06; PER-34, PER-35; PA-01, PA-09, PA-13.

## 7. Consulta y seguimiento

### CU-26. Consultar información, panel y matriz de riesgos

- **Actor y objetivo:** AE, GS o AU consulta registros y resultados empresariales; RA consulta exclusivamente sus tareas mediante CU-23.
- **Previas:** condiciones comunes; datos y metodologías identificables.
- **Flujo:** 1) Abre módulo o panel. 2) Selecciona evaluación, periodo y filtros aplicables. 3) El sistema consulta registros autorizados y presenta resultados, pendientes y fecha de referencia. 4) Permite navegar desde agregados al detalle autorizado y comparar evaluaciones sin mezclarlas.
- **Alternativas:** sin datos muestra ausencia de registros; evaluaciones incompletas muestran avance y limitaciones, no cumplimiento definitivo. La matriz distingue riesgos sin valoración de riesgos de nivel bajo.
- **Resultado:** listas, totales y gráficos conciliables con registros de la empresa y filtros seleccionados.
- **Verificación:** los conteos coinciden con el detalle; cambios de filtros, paginación o empresa no incluyen registros ajenos.
- **Trazabilidad:** RF-18, RF-19; PER-14, PER-19, PER-23, PER-29, PER-36; PA-08.

### CU-27. Generar reporte básico

- **Actor y objetivo:** AE, GS o AU obtiene una vista imprimible de información operativa autorizada.
- **Previas:** condiciones comunes y filtros definidos.
- **Flujo:** 1) Selecciona empresa activa, evaluación o periodo y secciones disponibles. 2) El sistema revalida acceso. 3) Genera vista con empresa, fecha, filtros, versiones de metodología y resultados o pendientes. 4) El usuario puede imprimir mediante el navegador.
- **Alternativas:** filtros sin resultados se indican; no incluye registro administrativo de actividad ni promete certificación. Si se pierde autorización antes de generarlo, se rechaza; no se permite seleccionar empresa ajena por parámetros.
- **Resultado:** reporte reproducible respecto de su fecha y filtros; otros formatos y exportación Excel quedan para etapas posteriores.
- **Verificación:** cifras concilian con CU-26 bajo los mismos filtros; AU no obtiene eventos administrativos por el reporte.
- **Trazabilidad:** RF-20; PER-37; PA-13.

### CU-28. Consultar registro de actividad

- **Actor y objetivo:** AE verifica quién realizó cambios relevantes en su empresa.
- **Previas:** condiciones comunes; eventos registrados con datos mínimos necesarios.
- **Flujo:** 1) Abre historial empresarial. 2) Filtra por fecha, actor, acción u objeto. 3) El sistema presenta eventos autorizados con marca temporal y motivo cuando aplique.
- **Alternativas:** no permite editar o borrar eventos; GS, RA y AU no acceden a este registro administrativo. Los avances propios de una tarea son información operativa distinta de este registro.
- **Resultado:** trazabilidad consultable sin secretos ni contenido sensible innecesario.
- **Verificación:** un cambio de rol queda registrado en su empresa y no aparece en el historial de otra.
- **Trazabilidad:** RF-21; SEG-09; RNF-09; PER-38.

## 8. Matriz de trazabilidad de requerimientos funcionales

| Requerimiento | Casos principales |
| --- | --- |
| RF-01 | CU-01 |
| RF-02 | CU-03 |
| RF-03 | CU-07, CU-08 |
| RF-04 | CU-05 |
| RF-05 | CU-02, CU-08 |
| RF-06 | CU-09 |
| RF-07 | CU-10, CU-12 |
| RF-08 | CU-11 |
| RF-09 | CU-13 |
| RF-10 | CU-14, CU-16 |
| RF-11 | CU-15, CU-16 |
| RF-12 | CU-17, CU-20, CU-21 |
| RF-13 | CU-18, CU-21 |
| RF-14 | CU-18, CU-19 |
| RF-15 | CU-22, CU-23 |
| RF-16 | CU-24 |
| RF-17 | CU-15, CU-19, CU-23, CU-25 |
| RF-18 | CU-26 |
| RF-19 | CU-10, CU-13, CU-26 |
| RF-20 | CU-27 |
| RF-21 | CU-28 |
| RF-22 | CU-04 |

CU-06 desarrolla configuración empresarial incluida en el alcance, pendiente de un RF explícito. Los escenarios PA-01 a PA-13 de la matriz complementan los flujos con verificaciones de denegación, revocación y aislamiento. Los requerimientos de respaldo, rendimiento y migración se desarrollarán como procedimientos y pruebas técnicas, no como operaciones de estos roles empresariales.

## 9. Pendientes antes de implementar

1. Resolver metodología de criticidad, agregación, no aplicables, riesgo residual y combinación de controles.
2. Definir campos obligatorios y evidencia exigida por transición de evaluación y riesgo; CU-16 y CU-21 no habilitan cierres sin esa definición.
3. Elegir canal de invitaciones y recuperación y fijar vencimientos y reglas de sesión.
4. Definir retención y corrección de evidencias, límites y cuotas de almacenamiento.
5. Revisar las precisiones propuestas de permisos y estados con estos documentos como referencia.

Estos pendientes conservan su estado de decisión abierta. Los documentos describen comportamiento esperado y no acreditan que exista software implementado.
