# Diccionario de datos y validaciones

Versión: 0.3 — Datos y validaciones ajustados a APR-01 y APR-02
Fecha: 6 de noviembre de 2026
Relacionados: [Requerimientos](01-parametros-y-requerimientos.md), [Permisos](02-matriz-de-permisos.md), [Casos de uso](03-casos-de-uso.md), [Reglas de cálculo](04-reglas-de-calculo-y-valoracion.md).

## 1. Propósito y límites

Definir los datos de la primera versión, su significado, origen, obligatoriedad y validaciones. Las entidades son conceptos funcionales: no representan todavía tablas, nombres SQL, tipos físicos, índices ni contratos definitivos de API.

La separación por esquema empresarial es una decisión confirmada. La distribución de entidades, longitudes, tipos físicos y restricciones adicionales no aprobadas siguen en revisión. APR-02 resuelve parte de DEC-01 a DEC-12; su estado individual está en la sección 11 del documento 4. Describir un campo calculado no aprueba otra fórmula por asociación.

APR-01 del documento 1 aprueba alcance y política de roles, actualizados aquí para admitir varios roles por membresía. Esa aprobación no confirma las demás propuestas de datos, límites o metodologías.

APR-02 aprueba criticidad por máximo, escala discreta y promedio por control, riesgo categórico y residual conjunto documentado, mínimos de evidencia/cierre y conservación histórica. Rúbricas detalladas, duraciones y límites operativos continúan pendientes.

No se necesita copiar cada columna de los Excel como campo de la primera versión. Se incluyen los datos necesarios para los flujos acordados y se identifica el detalle documental pendiente de alcance.

## 2. Convenciones

### 2.1. Tipos conceptuales y obligatoriedad

| Notación | Significado |
| --- | --- |
| ID | Identificador opaco generado por el sistema; no es una autorización ni el nombre de un esquema |
| REF(entidad) | Referencia a un registro existente y autorizado de la entidad indicada |
| Texto(n) | Texto con máximo propuesto de n caracteres Unicode; recortar espacios exteriores en campos simples, conservar contenido significativo |
| Texto largo | Texto de hasta 10.000 caracteres propuestos; se trata como contenido, no como HTML ejecutable |
| Código | Valor de catálogo identificado por un código estable, separado de la etiqueta visible |
| Decimal | Número decimal finito con unidad explícita; precisión física pendiente del modelo de datos |
| Fecha | Día calendario válido sin hora |
| Instante | Marca temporal uniforme del servidor; se propone UTC y presentación según zona empresarial |
| Booleano | Verdadero o falso; no usar falso como sustituto de falta de respuesta |
| M / C / O | Obligatorio al crear / obligatorio bajo condición indicada / opcional |
| U / S / D | Introducido por usuario autorizado / generado o validado por servidor / derivado por regla de cálculo |

Los límites de texto son propuestas, no propiedades heredadas de los Excel. No truncar silenciosamente. Las listas de referencias se describen como relaciones; no suponen guardar identificadores concatenados en una celda o columna.

### 2.2. Datos comunes heredados

Las entidades persistentes incluyen `id` (ID, M/S, inmutable) y `creado_en` (Instante, M/S). Los registros editables incluyen `actualizado_en` (Instante, S) y `revision_registro` (entero positivo, S), usado para detectar conflictos. Las operaciones sobre registros existentes reciben la revisión esperada; si cambió, se informa conflicto sin sobrescritura silenciosa.

Los registros de negocio incluyen `creado_por` y, cuando aplique, `actualizado_por` (REF(usuario), S), conservando atribución histórica aunque la membresía ya no esté vigente. Las operaciones técnicas se atribuyen a un actor de servicio identificado. Los eventos y versiones cerradas son inmutables y no reciben edición ordinaria. No se borran usuarios referenciados en los flujos iniciales.

Todo registro empresarial tiene una **pertenencia empresarial verificable** derivada del contexto autorizado. En un esquema por empresa no se exige duplicar `empresa_id` en todas las tablas: su representación y la validación de relaciones se resolverán en el modelo físico. El navegador no puede elegir libremente el esquema ni fijar campos de autoría, resultados o privilegios.

## 3. Distribución y protección

| Ámbito propuesto | Entidades | Restricción |
| --- | --- | --- |
| Común de plataforma | DD-01 a DD-08 | Compartir ubicación no implica visibilidad para todas las empresas; identidades y secretos tienen accesos específicos |
| Esquema de cada empresa | DD-09 a DD-25 | Relaciones locales dentro de la empresa; referencias al catálogo o identidad común pasan controles explícitos |
| Solicitud o vista derivada | DD-26 | Reportes y filtros conservan contexto empresarial; no se presupone persistencia del reporte |

Los permisos se heredan de PER-01 a PER-38, no de campos de roles enviados con formularios operativos. «Lectura operativa» la conceden AE, GS y AU de la empresa; RA por sí solo concede proyecciones mínimas de tareas y evidencias autorizadas. Si se combina con otros roles, se agregan sus concesiones, manteniendo las restricciones transversales. AP no obtiene lectura operativa por administrar la plataforma.

Los identificadores de usuario de la plataforma no habilitan enumerar personas de otras empresas. Selectores de responsables entregan únicamente identidad mínima y elegibilidad local, sin credenciales, invitaciones o membresías de otras empresas.

## 4. Identidad y plataforma

### DD-01. Empresa

**Acceso:** AP gestiona alta y estado (PER-01 a PER-03); AE edita datos básicos (PER-07). Lectura empresarial por PER-06. Contexto común; no contiene activos ni evidencias.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| nombre | Texto(200) | M/U | Nombre visible; no solo espacios; no usarlo como identificador único o nombre de esquema |
| descripcion | Texto largo | O/U | Descripción institucional; no incluir secretos operativos |
| zona_horaria | Código | M/U | Zona reconocida por el sistema; propuesta inicial America/Bogota, confirmada al alta |
| contacto_inicial | Correo | M/U | Destinatario verificado por invitación del primer AE; no se concede acceso por conocerlo |
| esquema_interno | Identificador técnico | M/S | Único en la instancia; generado y validado por servidor; nunca editable por roles empresariales |
| estado | Código | M/S | En preparación, activa, suspendida o error de aprovisionamiento; transición por CU-01/CU-02 |
| version_estructura | Texto(50) | C/S | Obligatoria para activar; versión técnica instalada, distinta de la metodología |
| motivo_estado | Texto(1000) | C/U | Obligatorio al suspender/reactivar; registrado con autor y fecha |
| referencia_fallo | ID técnico | C/S | Obligatorio si falla aprovisionamiento; detalles internos no visibles para roles sin permiso |

La recuperación de un alta fallida es idempotente. Un nombre repetido no implica la misma empresa; la política de detección de duplicados por identificador institucional queda pendiente.

### DD-02. Usuario e identidad

**Acceso:** autenticación y recuperación propias (PER-04); AE administra membresías, no credenciales de terceros. AP no consulta hashes desde interfaces. Cambios generales de perfil fuera de estos flujos quedan diferidos.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| nombre_visible | Texto(150) | M/U | Nombre para atribución y selectores autorizados |
| correo | Correo | M/U | Formato válido, máximo propuesto 254; verificación necesaria para aceptar invitación |
| clave_correo | Texto normalizado | M/S | Clave única de búsqueda de cuenta; política de normalización documentada antes de implementar; no quitar puntos o sufijos del proveedor |
| correo_verificado_en | Instante | C/S | Solo tras verificar propiedad; una fecha enviada por cliente no sirve como comprobante |
| estado_cuenta | Código | M/S | Pendiente de verificación, habilitada o deshabilitada; no sustituye estado de membresías |
| hash_credencial | Secreto de autenticación | C/S | Solo si se elige autenticación local; nunca guardar contraseña en texto ni devolver hash en API, reportes o logs |

La política de longitud y complejidad de contraseña y el mecanismo de autenticación están pendientes. La contraseña es una entrada transitoria, no un campo recuperable del diccionario. Deshabilitar una cuenta global no es una atribución del AE y su administración requiere especificación técnica separada.

### DD-03. Membresía y atribución de plataforma

**Acceso:** AE de la empresa por PER-08/PER-10; consulta propia para selección (PER-05). Un rol AP se mantiene como atribución independiente, asignada por procedimiento técnico fuera de formularios empresariales.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| empresa | REF(empresa) | M/S | Empresa a la que se concede pertenencia |
| usuario | REF(usuario) | M/S | Identidad global verificada; pareja empresa/usuario única |
| roles | Relación de códigos | M/U por AE autorizado | Uno o varios entre AE, GS, RA y AU; sin duplicados; conjunto no vacío para membresía activa; no incluye AP |
| estado | Código | M/S | Activa o revocada; no reutilizar una invitación para cambiar rol existente |
| revocada_en | Instante | C/S | Obligatorio si revocada; no destruye autoría histórica |
| motivo_cambio | Texto(1000) | C/U | Obligatorio al añadir/retirar roles o revocar; conservar actor y conjuntos anterior/nuevo |

Revalidar cambios de forma atómica para conservar al menos un AE activo, exceptuando la fase inicial de preparación con invitación pendiente de CU-01. Suspender empresa no revoca necesariamente membresías, pero bloquea su uso. Reactivar una membresía revocada exige un flujo explícito por definir; no ocurre por aceptar enlaces antiguos.

La pareja empresa/usuario sigue siendo única: no crear una membresía por rol. «AE activo» significa membresía activa cuyo conjunto incluye AE. La asignación conceptual de roles es una relación, no una cadena separada por comas; su representación física se definirá en el modelo de datos. Retirar un rol no retira permisos concedidos por otro válido; impedir cero roles activos y autoaprobación aun con varios roles. La elegibilidad de un responsable se comprueba por presencia de al menos uno de AE/GS/RA, sin exigir RA adicional.

### DD-04. Invitación

**Acceso:** AE gestiona invitaciones propias; AP solo emite/reintenta la invitación inicial; destinatario acepta con identidad y token (PER-09/PER-11).

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| empresa | REF(empresa) | M/S | Fijada al emitir, inmutable durante aceptación |
| correo_destinatario | Correo | M/U | Propiedad verificada al aceptar; no invita por nombre solamente |
| roles_ofrecidos | Relación de códigos | M/U por AE autorizado | Conjunto no vacío y sin duplicados entre AE/GS/RA/AU; la invitación inicial emitida por AP ofrece solo AE; inmutable para el destinatario |
| huella_token | Secreto derivado | M/S | Comprobante de un solo uso; no persistir token en claro ni mostrarlo en listados |
| vence_en | Instante | M/S | Posterior a creación; duración pendiente de política |
| aceptada_en | Instante | C/S | Si fue aceptada; consumo atómico para impedir reutilización |
| revocada_en | Instante | C/S | Si fue revocada; impide aceptación |
| estado | Código derivado | D | Pendiente, aceptada, revocada o vencida, consistente con fechas |

Aceptar requiere destinatario correcto, empresa activa, vigencia y ausencia de consumo/revocación. Invitación a una membresía ya existente no altera automáticamente su rol.

### DD-05. Sesión

**Acceso:** infraestructura de autenticación; el usuario puede terminar su sesión, sin leer secretos. No es un listado empresarial.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| usuario | REF(usuario) | M/S | Titular autenticado |
| comprobante_sesion | Secreto o referencia segura | M/S | Mecanismo pendiente; no almacenar identificadores sensibles en logs |
| expira_en | Instante | M/S | Vencimiento según política pendiente |
| invalidada_en | Instante | C/S | Al cerrar o invalidar; se rechaza uso posterior |
| contexto_solicitado | REF(empresa) | C | Contexto explícito de operación; no concesión persistente de permisos |

No confiar en roles o membresías congelados al iniciar sesión. Cambiar contexto en una pestaña no puede redirigir silenciosamente una operación iniciada en otra; el mecanismo concreto de sesión/contexto se definirá en arquitectura.

### DD-06. Comprobante de recuperación de acceso

**Acceso:** solo flujo de recuperación CU-04; no visible en listados empresariales.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| usuario | REF(usuario) | M/S | Cuenta existente; respuesta externa no revela existencia |
| huella_comprobante | Secreto derivado | M/S | Token/código de un solo uso; sin texto recuperable |
| canal_verificado | Referencia segura | M/S | Canal aprobado y vinculado al titular; pendiente de selección |
| vence_en | Instante | M/S | Posterior a emisión; duración pendiente |
| consumido_en | Instante | C/S | Solo tras cambio exitoso; consumo atómico |
| invalidado_en | Instante | C/S | Revocación del comprobante antes de consumo si procede |

No crear membresías ni reactivar empresas al recuperar acceso. Invalidar sesiones previas conforme CU-04; limitar intentos con una política aún por definir.

## 5. Catálogos y metodología común

### DD-07. Versión de catálogo y elemento

**Acceso:** AE, GS y AU consultan (PER-18); actualización técnica versionada fuera de interfaz empresarial inicial.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| catalogo_nombre | Texto(150) | M/S | Identifica el conjunto de referencia |
| catalogo_version | Texto(50) | M/S | Identificador inequívoco de la edición |
| tipo_elemento | Código | M/S | Control o cláusula |
| codigo_elemento | Texto(80) | M/S | Único dentro de versión y tipo; no asumir equivalencia entre A.9.x y A.5–A.8 |
| titulo | Texto(250) | M/S | Nombre visible |
| descripcion | Texto largo | M/S | Criterio o contenido de referencia validado |
| grupo | Código versionado | M/S | Dominio organizacional/personas/físico/tecnológico para controles; grupo de cláusulas para requisitos |
| referencia_origen | Texto(1000) | M/S | Fuente, versión y localizador; no confiere certificación |
| disponible_nuevos | Booleano | M/S | Puede impedir nuevos usos sin romper históricos |

Los campos de versión y elemento son conceptos relacionados, no una orden de duplicar el catálogo en cada respuesta. Publicar una corrección genera otra versión; las referencias históricas permanecen estables. CAL-REF-01 y DEC-11 siguen pendientes para NIST.

### DD-08. Metodología y reglas versionadas

**Acceso:** el sistema aplica versiones aprobadas; usuarios operativos consultan su identificación. No editable por empresa en versión inicial (RN-10).

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| codigo_metodologia | Texto(80) | M/S | Identifica la metodología funcional |
| version | Texto(50) | M/S | Única por metodología; no confundir con revisión de registro |
| estado_aprobacion | Código | M/S | Propuesta, aprobada o retirada para nuevos usos |
| reglas | Relación de definiciones | M/S | Escalas, intervalos, matriz, pesos, unidades, precisión y tratamiento de faltantes; sin código ejecutable enviado por usuario |
| fundamento | Texto largo | M/S | Justificación y referencias |
| aprobada_en | Instante | C/S | Obligatoria si aprobada |
| aprobada_por | Actor de aprobación | C/S | Obligatorio si aprobada; procedimiento de gobierno por definir |

Las reglas DEC abiertas no se etiquetan como aprobadas por cargar el documento. Una metodología utilizada no se modifica en sitio; se crea otra versión. El procedimiento de aprobación común no amplía permisos del AP o AE y debe especificarse antes de implementarlo.

## 6. Datos empresariales: procesos y activos

### DD-09. Proceso

**Acceso:** PER-12/PER-13; RA solo resumen contextual autorizado.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| nombre | Texto(200) | M/U | Nombre significativo; unicidad propuesta por nombre normalizado dentro de empresa |
| descripcion | Texto largo | O/U | Función del proceso |
| responsable_negocio | REF(responsable de negocio) | O/U | Debe ser local; no asigna permisos |
| incluido_alcance | Booleano | C/U | Solo si se aprueba indicador CAL-EVA-05; respuesta explícita, sin falso automático |
| estado | Código | M/S | Activo o archivado; archivar conserva referencias históricas |

Los conteos de procesos se derivan de registros elegibles, con filtros y fecha documentados. No mezclar un total manual de Excel con un conteo automático sin acuerdo de alcance (DEC-12).

### DD-10. Responsable de negocio

**Acceso:** PER-12/PER-13; selector mínimo, sin acceso al directorio global.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| nombre_o_cargo | Texto(200) | M/U | Persona, cargo o área que responde por el dato o activo |
| tipo | Código | M/U | Persona, cargo o área; catálogo propuesto |
| membresia_vinculada | REF(membresía) | O/U | Si se vincula a una cuenta, debe ser de la empresa; puede conservarse referencia revocada para historia |
| estado | Código | M/S | Activo o archivado |

Ser propietario/custodio o responsable de negocio no concede acceso a la aplicación. Las acciones requieren una membresía elegible, no solo este registro de texto.

### DD-11. Activo

**Acceso:** lectura PER-14; alta/edición PER-15; archivo/restauración PER-17. RA no recibe ficha completa.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| codigo | Texto(80) | M/U | Único por empresa, incluidos archivados; comparación normalizada propuesta sin distinguir mayúsculas; no truncar ni reciclar silenciosamente |
| nombre | Texto(250) | M/U | Denominación del activo |
| descripcion | Texto largo | M/U | Contenido, función o uso |
| tipo | Código | M/U | Información, hardware, software, servicio, recurso humano, instalación o red; catálogo inicial propuesto desde fuentes |
| proceso | REF(proceso) | M/U | Local y activo al asignarlo; historia admite proceso posteriormente archivado |
| propietario | REF(responsable de negocio) | M/U | Responsable principal del activo; no necesariamente usuario |
| custodio | REF(responsable de negocio) | O/U | Responsable de conservación o administración |
| ubicacion | Texto(1000) | O/U | Descripción de ubicación, sin credenciales; no implica descarga pública |
| contiene_datos_personales | Código | C/U | Sí, no o pendiente; debe resolverse antes de completar clasificación |
| contiene_datos_menores | Código | C/U | Sí, no o pendiente; resolver cuando contiene datos personales |
| tipos_datos_personales | Relación de códigos | C/U | Si contiene datos personales; catálogo versionado, puede haber varios tipos |
| finalidad_tratamiento | Texto largo | C/U | Si contiene datos personales; no sustituye una evaluación jurídica |
| autorizacion_tratamiento | Código | C/U | Sí, no, no aplica justificado o pendiente; no afirmar que todo tratamiento exige la misma autorización |
| justificacion_no_aplica | Texto(2000) | C/U | Obligatoria cuando se marca no aplica en autorización |
| estado | Código | M/S | Activo o archivado |
| motivo_archivo_restauracion | Texto(1000) | C/U | Requerido al archivar/restaurar; conservar evento |

Se propone permitir guardar el activo antes de resolver su clasificación; queda visiblemente incompleto. Las validaciones de datos personales son de consistencia de captura, no asesoría legal. Si contiene datos personales=no, no admitir contiene_datos_menores=sí ni categorías/finalidad contradictorias; una corrección de sí a no exige revisar datos previos y conservar su historia, no borrarlos silenciosamente.

### DD-12. Valoración de activo

**Acceso:** PER-16, lectura según activo; versiones históricas no editables directamente.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| activo | REF(activo) | M/S | Activo local, uno por valoración |
| metodologia | REF(metodología/version) | M/S | Identifica reglas CAL-ACT-01/02 aprobadas para resultados definitivos |
| confidencialidad | Código | C/U | Bajo/medio/alto; equivalencias aprobadas 1/3/5; resolver para completar |
| integridad | Código | C/U | Misma condición; catálogo de dimensión |
| disponibilidad | Código | C/U | Misma condición; catálogo de dimensión |
| fundamento | Texto largo | C/U | Justificación propuesta para confirmar valoración completa |
| criticidad | Código | D | APR-02: máximo de C/I/D completas, 1 baja, 3 media, 5 alta; nula si falta alguna |
| estado_resultado | Código | M/S | Pendiente o completo; completo exige entradas y metodología válidas |
| valorada_en | Instante | C/S | Al confirmar; no aceptar fecha arbitraria como prueba de evaluación |

Propuesta: versiones nuevas al confirmar cambios, manteniendo anteriores; borradores editables con revisión. Identificar de forma inequívoca la valoración vigente del activo sin sobreescribir la anterior.

## 7. Evaluaciones y hallazgos

### DD-13. Evaluación

**Acceso:** PER-19 a PER-22; sin acceso RA al conjunto completo.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| nombre | Texto(200) | M/U | Distingue diagnóstico o periodo |
| fecha_evaluacion | Fecha | M/U | Fecha de referencia de la evaluación; no sustituye fechas técnicas |
| alcance | Texto largo | M/U | Qué procesos/áreas comprende; propuesta de relaciones explícitas con procesos locales |
| responsable | REF(membresía) | M/U | Activa, local y con rol AE o GS al asignarla |
| version_catalogo | REF(catálogo/version) | M/U | Fija elementos y criterios; no cambiar después de cierre |
| metodologia | REF(metodología/version) | M/U | Reglas de puntuación y agregación identificadas |
| estado | Código | M/S | Borrador, en revisión o cerrada |
| evaluacion_anterior | REF(evaluación) | C/S | Si es corrección vinculada; local, cerrada, sin autorreferencia ni ciclos |
| motivo_revision | Texto(2000) | C/U | Si corrige una evaluación cerrada |
| cerrada_en | Instante | C/S | Obligatorio al cerrar |
| cerrada_por | REF(usuario) | C/S | Actor AE o GS autorizado al cierre |

Una evaluación nueva no sobrescribe otra. Grupos, medias, nivel y cobertura son resultados derivados por CAL-EVA-01 a CAL-EVA-04: no campos de entrada. Si se guardan como instantánea, deben incluir versión y entradas suficientes para reproducirlos. El listado de elementos del alcance debe quedar fijado para evitar que un cambio de catálogo altere el denominador histórico.

### DD-14. Respuesta de evaluación

**Acceso:** PER-20/PER-21; lectura PER-19; evidencias por PER-34/PER-35.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| evaluacion | REF(evaluación) | M/S | Local y editable para cambios |
| elemento | REF(elemento de catálogo) | M/S | Debe pertenecer a versión y alcance fijados; único por evaluación/elemento |
| aplicabilidad | Código | M/U | Sin decidir, aplica o no aplica; sin decidir al crear |
| justificacion_aplicabilidad | Texto(2000) | C/U | Obligatoria para no aplica; restringida por política de cláusulas pendiente |
| calificacion | Número en puntos | C/U | Si aplica y evaluada: solo 0,20,40,60,80,100 aprobados; no nota individual 50 |
| observaciones | Texto largo | O/U | Fundamento de la valoración; requisitos de cierre por decidir |
| confirmacion_heredada | Código | C/U | Si proviene de revisión: pendiente de confirmar o confirmada; no contar copia como reevaluación |

No aplica implica calificación nula, no cero; sin decidir también mantiene calificación nula. Si aplica sin nota, es pendiente; cero cuenta como evaluado. N requiere motivo y no se habilita libremente en cláusulas. Puntaje y avance son derivados separados; global de controles con igual peso por control evaluado, no por dominio, y provisional con pendientes. Cláusulas separadas sin PHVA ponderado en v1. Cierre exige fundamento por respuesta y evidencia según criterio, cuya especificación detallada sigue pendiente.

### DD-15. Hallazgo de evaluación

**Acceso:** AE/GS registra dentro de evaluación editable; AU consulta; RA solo resumen seleccionado para su acción.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| respuesta | REF(respuesta de evaluación) | M/S | Ubica cláusula/control y empresa de origen |
| descripcion_brecha | Texto largo | M/U | Situación encontrada y diferencia respecto del criterio |
| recomendacion | Texto largo | O/U | Propuesta de mejora; no ejecuta automáticamente un control |

Puede originar varias acciones locales. Al cerrar evaluación, hallazgo queda inmutable con ella; el seguimiento se hace mediante acciones, no editando el hallazgo cerrado. No se crea un flujo independiente de cierre de hallazgos en esta versión.

## 8. Controles y riesgos

### DD-16. Implementación empresarial de control

**Acceso:** PER-26; consulta operativa; RA solo contexto autorizado. Diferente de catálogo y respuesta de evaluación.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| elemento_catalogo | REF(elemento de catálogo) | M/U | Tipo control, con versión identificada |
| nombre | Texto(250) | M/U | Distingue implementaciones concretas del mismo control |
| descripcion_implementacion | Texto largo | M/U | Cómo se aplica realmente en la empresa |
| responsable | REF(responsable de negocio) | M/U | Responsable local; no otorga acceso por sí mismo |
| tipo_control | Código | C/U | Preventivo, detectivo o correctivo; atributo descriptivo, no coeficiente de descuento en v1 |
| modo_implementacion | Código | C/U | Manual o automático; atributo descriptivo sin pesos de mitigación automática |
| estado_implementacion | Código | M/U | Propuesto, en implementación o implementado; catálogo inicial propuesto, distinto de estado de tarea |
| fecha_implementacion | Fecha | C/U | Si implementado; no futura al confirmar implementación |
| resultado_verificacion | Código | M/U | Sin verificar, efectivo, parcialmente efectivo o no efectivo; catálogo propuesto, sin porcentajes implícitos |
| fundamento_verificacion | Texto largo | C/U | Si se registra un resultado distinto de sin verificar |
| verificado_por | REF(usuario) | C/S | Actor AE/GS que confirma verificación |
| verificado_en | Instante | C/S | Si se confirma verificación |
| proxima_revision | Fecha | C/U | Según política de vigencia aún pendiente DEC-06 |

Evidencias por DD-23: comprobación descrita, evidencia pertinente, resultado, autor y fecha. Implementado y efectivo son dimensiones separadas; no conceden descuento automático en v1 según APR-02. Rúbricas y vigencia siguen pendientes. Los cambios conservan eventos y señalan valoraciones relacionadas para revisión.

### DD-17. Riesgo

**Acceso:** PER-23/PER-24/PER-28; aceptación formal exclusiva PER-27.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| codigo | Texto(80) | M/U | Único por empresa; política de normalización igual a código de activo |
| activo_principal | REF(activo) | M/U | Local y activo al registrar nuevo riesgo |
| amenaza | Texto(1000) | M/U | Evento o causa inmediata identificada; catálogo futuro opcional |
| vulnerabilidad | Texto(1000) | M/U | Debilidad que permite materializar amenaza |
| descripcion | Texto largo | M/U | Evento y consecuencias sobre el activo |
| responsable | REF(responsable de negocio) | M/U | Responsable local del riesgo |
| tratamiento_propuesto | Código | O/U | Reducir, compartir, aceptar o evitar; proponer aceptar no lo aprueba |
| estado | Código | M/S | Identificado, en análisis, en tratamiento, en seguimiento o cerrado |
| justificacion_cierre | Texto largo | C/U | Si solicita cierre; valoración vigente y acciones resueltas según CU-21 |
| cerrado_por | REF(usuario) | C/S | AE/GS autorizado si cierre exitoso |
| cerrado_en | Instante | C/S | Si cerrado |
| riesgo_relacionado | REF(riesgo) | O/U | Local y distinto de sí mismo; permite documentar nuevo evento sin reabrir original |

No almacenar un riesgo por cada fila de control. El activo archivado conserva riesgos existentes. No derivar probabilidad directamente de criticidad ni tratamiento directamente del nivel sin una regla aprobada.

### DD-18. Asociación riesgo–implementación

**Acceso:** PER-26; sin relaciones entre empresas.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| riesgo | REF(riesgo) | M/U | Local |
| implementacion | REF(implementación de control) | M/U | Local; pareja riesgo/implementación única |
| efecto_esperado | Código | M/U | Probabilidad, impacto o ambos, como hipótesis documentada; no activa fórmula |
| justificacion | Texto largo | M/U | Explica cómo atiende ese riesgo concreto |

Una implementación puede vincularse a varios riesgos y un riesgo a varias implementaciones. No borrar vínculos usados por valoraciones cerradas; la forma de desactivar asociaciones para futuros análisis se precisará en el diseño de historial.

### DD-19. Valoración de riesgo

**Acceso:** PER-25; entrada por AE/GS; lectura operativa y resumen RA limitado.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| riesgo | REF(riesgo) | M/S | Local; mantiene varias valoraciones históricas |
| clase_valoracion | Código | M/U | Inherente o residual |
| metodologia | REF(metodología/version) | M/U | Reglas y matriz identificadas; resultados definitivos requieren aprobación |
| periodo_referencia | Texto(150) | M/U | Ventana a la que corresponde fundamento, no frecuencia sin unidad |
| probabilidad_indice | Decimal de referencia | D | Equivalente categórico 0,20/0,40/0,60/0,80/1,00, no probabilidad estadística medida ni entrada editable |
| impacto_indice | Decimal de referencia | D | Equivalente categórico 0,20/0,40/0,60/0,80/1,00; no porcentaje libre |
| frecuencia_valor | Decimal no negativo | O/U | Solo informativo mientras DEC-07 esté abierta |
| frecuencia_unidad | Código | C/U | Obligatoria con frecuencia; ejecuciones/año, horas/año u otra unidad documentada; no intercambiables |
| fundamento | Texto largo | C/U | Obligatorio para confirmar; evidencia y supuestos |
| inherente_base | REF(valoración de riesgo) | C/U | Si residual: misma empresa y riesgo, clase inherente, confirmada; no ciclos |
| modo_obtencion | Código | M/S | Selección categórica documentada en v1; sin fórmula automática de descuentos |
| controles_considerados | Relación de versiones | C/U | En residual con mitigación: implementaciones y verificaciones consideradas; fijar versión/instantánea para reproducibilidad |
| sin_mitigacion_confirmada | Booleano | C/U | Residual sin controles: confirmación explícita y fundamento; no valor por defecto |
| categoria_probabilidad | Código | C/U | Muy baja/baja/media/alta/muy alta; obligatoria para confirmar; fundamento y periodo requeridos |
| categoria_impacto | Código | C/U | Leve/menor/moderado/mayor/catastrófico; obligatoria para confirmar con fundamento |
| nivel | Código | D | Bajo, moderado, alto o extremo mediante matriz aprobada; nulo si pendiente |
| estado_resultado | Código | M/S | Borrador o confirmado; confirmación exige datos, fundamento y política válidos |
| requiere_revision | Booleano | S | Señal de desactualización; no reescribe valor histórico |
| motivo_revision | Texto(2000) | C/S | Si requiere revisión |
| valorada_en | Instante | C/S | Al confirmar valoración |

Una confirmada conserva entradas/resultados inmutables; revisión por separado. Se identifica valoración vigente por clase. En v1 no se usa q nominal ni descuento por tipo, modo o nota del control, tampoco con un solo control. AE/GS justifica categorías residuales conjuntas, con implementaciones verificadas consideradas o ausencia de mitigación explícita. El sistema calcula nivel mediante matriz aprobada y equivalencias internas; las rúbricas específicas aún deben completarse.

### DD-20. Decisión de aceptación de riesgo

**Acceso:** AE aprueba por PER-27; GS puede proponer tratamiento en DD-17, sin llenar campos de aprobación.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| riesgo | REF(riesgo) | M/S | Local |
| valoracion_referencia | REF(valoración de riesgo) | M/U | Confirmada, vigente y del mismo riesgo al aprobar |
| justificacion | Texto largo | M/U | Fundamento de aceptación |
| fecha_proxima_revision | Fecha | M/U | Posterior a fecha empresarial de aprobación; sin aceptación indefinida implícita |
| aprobada_por | REF(usuario) | M/S | Identidad con membresía AE activa al aprobar |
| aprobada_en | Instante | M/S | Hora de decisión del servidor |

Conservar decisiones anteriores; un cambio relevante o vencimiento de revisión genera pendiente de revisión, no una aprobación renovada automática. Aceptar no cierra riesgo ni elimina acciones.

## 9. Acciones y seguimiento

### DD-21. Acción de mejora

**Acceso:** PER-29 a PER-33; RA solo asignación actual y contexto mínimo. AE/GS gestiona; no puede aprobar si es responsable.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| codigo | Texto(80) | M/S | Identificador legible único por empresa, generación por definir |
| riesgo_origen | REF(riesgo) | C/U | Exactamente un origen: riesgo o hallazgo |
| hallazgo_origen | REF(hallazgo) | C/U | Excluyente con riesgo_origen; mismo contexto empresarial |
| descripcion | Texto largo | M/U | Trabajo a ejecutar |
| resultado_esperado | Texto largo | M/U | Criterio observable que revisará otra persona |
| responsable | REF(membresía) | M/U | Activa, local y rol AE, GS o RA al asignar; revalidar al ejecutar |
| fecha_limite | Fecha | M/U | Sin valor ficticio; propuesta: no anterior al día empresarial al crear; tareas vencidas siguen siendo válidas después |
| evidencia_requerida | Texto(2000) | M/U | Qué debe aportar; si no se exige archivo, justificación explícita según política pendiente |
| estado | Código | M/S | Pendiente, en ejecución, en revisión, completada o cancelada |
| resumen_contexto_ra | Texto largo | M/U | Contexto mínimo preparado por AE/GS; sin copiar ficha completa o datos personales innecesarios |
| motivo_cambio | Texto(2000) | C/U | Al cambiar plazo, alcance, responsable o cancelar |
| accion_anterior | REF(acción) | O/U | Local, distinta de sí misma, sin ciclos; mejora posterior a completada |
| vencida | Booleano | D | Fecha límite anterior al día empresarial y estado no completada/cancelada; no sustituye estado |

El contexto RA se entrega mediante una vista limitada de tarea, nombre de activo y resumen autorizado, no serializando objetos completos. Reasignar retira acceso por asignación al anterior y conserva avances. El plazo no cambia por presentar un porcentaje de avance.

### DD-22. Avance y decisión de revisión

**Acceso:** avance solo por responsable actual (PER-32); decisión solo por AE/GS distinto del responsable (PER-33). Lectura por acceso a tarea.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| accion | REF(acción) | M/S | Local |
| tipo_evento | Código | M/S | Avance, solicitud de revisión, aprobación o devolución; proviene de operación autorizada |
| comentario | Texto largo | M/U | Descripción del avance o motivo de decisión |
| autor | REF(usuario) | M/S | Identidad autenticada, no nombre enviado por formulario |
| responsable_al_evento | REF(membresía) | M/S | Conserva asignación vigente al registrar evento |
| entrega_referencia | REF(evento de solicitud) | C/S | Si aprueba/devuelve; misma acción, última entrega en revisión sin decidir |

Propuesta: registros append-only para avances y decisiones, sin edición silenciosa. Solicitar revisión requiere los datos/evidencias exigidos y congela la entrega hasta devolución. La comprobación de identidad diferente y estado se repite al confirmar; dos revisores no deciden la misma entrega dos veces. Porcentaje manual de ejecución no se incluye inicialmente, evitando confundirlo con efectividad del control.

## 10. Evidencias y trazabilidad

### DD-23. Evidencia privada o referencia documental verificable

**Acceso:** PER-34/PER-35 según objeto/estado; RA no accede por conocer ruta. Archivos en almacenamiento privado y metadatos dentro de empresa. APR-02 admite referencia en lugar de archivo cuando el criterio lo permita; las condiciones siguientes distinguen ambas modalidades.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| tipo_objeto | Código | M/S | Respuesta, implementación de control o acción |
| objeto | REF(según tipo_objeto) | M/S | Un único destino local; se valida tipo y pertenencia, no referencia polimórfica sin control |
| modalidad | Código | M/U | Archivo o referencia documental; permitida por criterio; nunca ambos incompletos |
| referencia_documental | Texto(2000) | C/U | Para referencia: identificador de documento, versión/fecha y localizador verificable sin credenciales; no solo texto «existe» |
| nombre_original | Texto(255) | C/U | Para archivo; etiqueta, no ruta de almacenamiento |
| clave_almacenamiento | Identificador interno | C/S | Para archivo disponible: generada, privada, separada por empresa; nunca ruta del cliente |
| tipo_detectado | Código MIME | C/S | Para archivo disponible: validar en servidor, no solo extensión/cabecera |
| tamano_bytes | Entero positivo | C/S | Para archivo: medido en servidor; límite propuesto 10 MB, unidad por acordar |
| huella_contenido | Hash | C/S | Para archivo: propuesta SHA-256; no sustituye autorización ni pertinencia |
| descripcion | Texto(1000) | O/U | Qué demuestra el archivo |
| estado_carga | Código | M/S | Pendiente, disponible o fallida; disponibilidad técnica, no aprobación del fundamento; referencia no tiene descarga local |

Formatos propuestos PDF/PNG/JPEG sujetos a política final. Carga parcial no satisface cierre. La referencia disponible exige metadatos completos y modalidad permitida; quien revisa el objeto debe comprobar accesibilidad, versión y pertinencia antes de usarla para cierre. No se afirma que una URL cargada sea evidencia verificada. No descargar referencias externas automáticamente, ni almacenar credenciales o enlaces públicos permanentes a archivos privados. Retención/corrección de evidencias sigue pendiente y no hay borrado físico en la interfaz inicial.

### DD-24. Evidencia de contexto autorizada para una acción

**Acceso:** AE/GS selecciona contexto por PER-30/PER-31; RA lee solo si sigue asignado y la evidencia es visible (PER-35).

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| accion | REF(acción) | M/U | Local |
| evidencia | REF(evidencia) | M/U | Local, disponible y accesible al gestor; pareja única |
| motivo_contexto | Texto(1000) | M/U | Justifica necesidad de acceso para ejecutar tarea |
| seleccionada_por | REF(usuario) | M/S | AE/GS autorizado |

Seleccionar una evidencia para RA es una divulgación explícita y debe mostrar su alcance antes de confirmar. El permiso permite leer ese archivo, no su objeto completo ni archivos vecinos. Una evidencia cargada en la propia tarea se autoriza por su tarea y no necesita duplicarse en esta relación.

### DD-25. Evento de actividad empresarial

**Acceso:** lectura administrativa exclusiva AE (PER-38); escritura solo por sistema; diferente de avances operativos de tarea. Eventos de plataforma tienen ámbito y acceso técnico separado.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| actor | Identidad/actor servicio | M/S | Quién ejecutó; conserva atribución histórica |
| accion_evento | Código | M/S | Acción relevante de negocio, cambio de rol o decisión |
| tipo_objeto | Código | M/S | Tipo de registro afectado |
| objeto_id | ID contextual | M/S | Localizador histórico, no permiso de acceso ni vínculo cruzado empresarial |
| resultado | Código | M/S | Éxito o rechazo/error registrado cuando corresponda |
| motivo | Texto(2000) | C/S | Si operación lo exige; evitar información sensible innecesaria |
| cambios_permitidos | Estructura restringida | O/S | Campos relevantes anteriores/nuevos según política; nunca contraseñas, tokens, hashes de credenciales o contenido completo de evidencias |
| correlacion | ID técnico | M/S | Relaciona eventos de la misma operación sin revelar secretos |

Usa creado_en como instante del evento. No editable o eliminable desde la aplicación. Intentos ajenos se registran sin introducir contenido de otra empresa en su historial; detalles técnicos de seguridad quedan en un registro restringido independiente.

### DD-26. Consulta, filtros y reporte básico

**Acceso:** PER-36/PER-37 para AE/GS/AU; RA usa lista de sus tareas, no reporte general. Datos transitorios o derivados; no impone una tabla de reportes.

| Campo | Tipo | Obligación/origen | Significado y validación |
| --- | --- | --- | --- |
| empresa_contexto | REF(empresa) | M/S | Resuelta contra membresía, no aceptada por confianza en el navegador |
| evaluacion | REF(evaluación) | C/U | Si consulta resultados de una evaluación; local y accesible |
| fecha_desde / fecha_hasta | Fecha | O/U | Si ambas están presentes, desde ≤ hasta; definir si filtran fecha de evento, evaluación o vencimiento según vista |
| filtros | Estructura permitida | O/U | Solo campos y operadores previstos; estados/códigos válidos; sin SQL o nombres de esquema |
| pagina | Entero positivo | M/S | Valor por defecto propuesto 1; no altera permisos |
| tamano_pagina | Entero positivo | M/S | Propuesta 25 por defecto y máximo 100; validar en servidor |
| generado_en | Instante | D | Fecha del reporte o consulta materializada |
| referencias_metodologia | Relación de versiones | D | Versiones utilizadas en resultados; diferencias visibles |
| resultados_y_pendientes | Vista derivada | D | Agregados conciliables con detalle autorizado; riesgos contados por ID, no por control |

No incluir historial administrativo mediante parámetros de reporte operativo. Validar acceso al generar y descargar, si luego se incorpora un archivo. La versión inicial ofrece vista imprimible; exportación Excel sigue diferida.

## 11. Validaciones transversales y aplicación

La interfaz orienta al usuario, el servidor toma la decisión y la base de datos refuerza restricciones representables. Una validación de pantalla no reemplaza las otras capas. No se afirma aún un mecanismo SQL concreto.

| ID | Validación | Aplicación prevista | Respuesta funcional |
| --- | --- | --- | --- |
| DVAL-01 | Obligatorios, tipo, longitud, formatos y números finitos | Interfaz y servidor; base para nulos/tipos/límites viables | Rechazar sin truncar; señalar campo |
| DVAL-02 | Unicidad local de códigos y global de identidades | Servidor y restricción atómica de base | Conflicto comprensible; no revelar cuentas ajenas |
| DVAL-03 | Pertenencia de referencias empresariales y de catálogo | Servidor y relaciones/restricciones de base según modelo | Rechazar vínculo cruzado sin exponer objeto ajeno |
| DVAL-04 | Rol, membresía, estado empresarial y acceso al objeto | Servidor en cada operación | Denegar aunque se manipule URL o cuerpo |
| DVAL-05 | Campos generados/calculados no editables por cliente | Lista explícita de entradas en servidor | Rechazar intento; recalcular en servidor |
| DVAL-06 | Estados y condiciones de transición | Servidor y actualización atómica | Mantener estado anterior y listar pendientes autorizados |
| DVAL-07 | Revisión esperada y concurrencia | Servidor y base mediante estrategia por diseñar | Conflicto; no sobrescritura silenciosa |
| DVAL-08 | Vigencia y consumo único de invitación/recuperación | Servidor y operación atómica de base | No reutilizar comprobante |
| DVAL-09 | Último AE activo y separación de revisor de tarea | Servidor y transacción | Rechazar operación incluso bajo concurrencia |
| DVAL-10 | Faltantes, cero, N, categorías e igual peso por control según APR-02 | Servidor y representación coherente en base | Pendiente/N/error distintos de cero; equivalencias derivadas y sin descuento automático |
| DVAL-11 | Coherencia condicional de campos y fechas | Interfaz/servidor y restricciones viables de base | Ej.: no aceptar N con calificación ni revisión anterior a aprobación |
| DVAL-12 | Validación y disponibilidad de archivos privados | Servidor y almacenamiento | Rechazar archivo inválido; no publicar carga parcial |
| DVAL-13 | Protección de secretos y minimización de vistas | Serialización, servidor y registros técnicos | No incluir secretos o datos completos en vista RA |
| DVAL-14 | Versiones e historial inmutables | Servidor y permisos/restricciones de persistencia | Crear nueva versión; no reescribir cierres |
| DVAL-15 | Contexto empresarial de consultas y procesos | Servidor, conexiones, cachés y tareas | Evitar contaminación entre solicitudes |

Los mensajes no incluyen SQL, rutas internas, tokens o nombres de objetos de otra empresa. Errores de campos pueden mostrarse con detalle a quien está autorizado; autenticación y recuperación responden sin revelar existencia de cuentas. Si hay varios errores se muestran juntos cuando sea posible sin revelar información restringida.

## 12. Validaciones por momento del flujo

| Momento | Datos mínimos y condición |
| --- | --- |
| Crear empresa | Identificación, contacto, zona; preparación técnica antes de activar |
| Crear activo | Código, nombre, descripción, tipo, proceso y propietario; clasificación puede quedar pendiente |
| Confirmar clasificación | Tres dimensiones y fundamento; reglas aprobadas; datos personales resueltos según condición |
| Crear evaluación | Fecha, alcance, responsable, catálogo y metodología identificados; respuestas inician pendientes |
| Guardar respuesta | Estado editable y consistencia de aplicabilidad/calificación; no exige completar otras respuestas |
| Cerrar evaluación | Sin aplicabilidad pendiente ni respuestas aplicables pendientes, evidencias según DEC-06; metodología aprobada |
| Registrar riesgo | Activo, amenaza, vulnerabilidad, descripción y responsable; valoraciones pueden quedar pendientes |
| Confirmar valoración | Categorías, metodología aprobada y fundamento; residual identifica inherente base y verificaciones o ausencia de mitigación confirmada |
| Aceptar riesgo | AE, valoración vigente, justificación y fecha de revisión |
| Crear acción | Un origen local, resultado esperado, responsable elegible, plazo y evidencia requerida |
| Solicitar revisión de acción | Responsable vigente, estado permitido, entrega y evidencia requerida disponibles |
| Aprobar acción | AE/GS distinto del responsable, última entrega en revisión, motivo y ausencia de conflicto |
| Cerrar riesgo | Valoración vigente, justificación y acciones resueltas según CU-21; no se cierra por aceptar tratamiento |

Estas filas precisan mínimos propuestos; no resuelven por anticipado los criterios de evidencia o metodología aún abiertos.

## 13. Cobertura de fuentes y alcance pendiente

Las siguientes correspondencias se basan en el análisis del documento 4, no en una migración ni una nueva interpretación de las fuentes.

| Fuente funcional | Entidades que la representan | Datos por decidir o diferidos |
| --- | --- | --- |
| Datos de entidad, áreas y procesos | DD-01, DD-09, DD-10 | Misión/organigrama/contexto institucional detallado según alcance futuro |
| Escalas y catálogo de controles/cláusulas | DD-07, DD-08, DD-13, DD-14 | Metodologías DEC-01 a DEC-05 y correspondencias de versiones |
| Inventario y C/I/D | DD-11, DD-12 | Series/subseries, fechas de archivo, idioma, soporte y campos de reserva legal: confirmar necesidad antes de incluirlos; no se declaran soportados en v1 |
| Riesgos, controles y residual | DD-16 a DD-20 | Frecuencia/unidades y consecuencia económica/reputacional según DEC-07; pesos y combinación DEC-09/DEC-10 |
| Seguimiento y evidencias | DD-21 a DD-25 | Retención, eliminación, corrección de archivos y vigencia de evidencias |
| Panel y reportes | DD-26 y vistas derivadas | NIST independiente, exportación Excel e importaciones diferidas |

## 14. Escenarios de aceptación del diccionario

Estos escenarios son especificaciones para pruebas futuras, no resultados de una aplicación implementada.

| ID | Escenario | Resultado esperado |
| --- | --- | --- |
| DAT-01 | Código de activo equivalente por normalización dentro de A | Rechazo de duplicado; mismo código en B permitido |
| DAT-02 | Riesgo de A referencia activo, responsable o control de B | Rechazo de vínculo cruzado |
| DAT-03 | Cliente envía criticidad, autor o rol AP como campo editable | No se acepta como entrada autorizada |
| DAT-04 | Respuesta no aplica con calificación 0 | Rechazo de combinación; N tiene calificación nula y motivo |
| DAT-05 | Clasificación sin disponibilidad | Guardado pendiente si corresponde; sin criticidad ficticia |
| DAT-06 | Cambiar riesgo_origen y hallazgo_origen ambos a no nulos o ambos a nulos | Rechazo; exactamente un origen |
| DAT-07 | Usuario con solo RA envía plazo, revisa tarea ajena o pide ficha completa | Denegación; proyección mínima únicamente |
| DAT-08 | AE responsable intenta aprobar su propia entrega | Denegación por identidad, no solo por rol |
| DAT-09 | Dos cambios simultáneos con misma revisión esperada | Uno detecta conflicto; no pérdida silenciosa de cambios |
| DAT-10 | Dos consumos simultáneos de un mismo token | Máximo un consumo exitoso |
| DAT-11 | Cliente envía probabilidad_indice=0,65 o 65 para sustituir categoría | Rechazo de campo derivado editable; seleccionar categoría y derivar equivalencia en servidor |
| DAT-12 | Actualización de catálogo o control tras valoración confirmada | Historial intacto, referencia de versión preservada y revisión señalada |
| DAT-13 | Extensión PDF con contenido no admitido, o carga parcial | No disponible como evidencia ni válida para cierre |
| DAT-14 | Revocar último AE mediante cambios simultáneos | Conserva al menos un AE activo |
| DAT-15 | Cambiar de empresa en otra pestaña mientras se envía formulario | Operación conserva su contexto explícito autorizado o se rechaza; no se redirige |
| DAT-16 | Membresía revocada conserva avances históricos | Autoría consultable según permiso, acceso revocado en nuevas solicitudes |
| DAT-17 | Recuperar contraseña y reutilizar sesión anterior | Sesión anterior invalidada; membresías no se reactivan |
| DAT-18 | Reporte operativo solicita eventos administrativos por parámetros | No entrega historial restringido |
| DAT-19 | Guardar roles duplicados, AP o conjunto vacío en membresía activa | Rechazo; solo conjunto empresarial válido gestionado por AE |
| DAT-20 | Usuario AU+GS edita y después pierde GS | Edición concedida inicialmente por GS; al retirar GS conserva solo lectura por AU |
| DAT-21 | Usuario GS+RA responsable intenta aprobar su entrega | Rechazo por identidad aunque combine permisos |
| DAT-22 | Aceptar invitación con varios roles o añadir roles no ofrecidos | Conceder solo conjunto ofrecido; rechazar manipulación y no duplicar membresía |
| DAT-23 | C/I/D=(5,1,1), o (5,vacío,1) | Alta y pendiente respectivamente; no regla anterior de dos altos |
| DAT-24 | Dominio con un 100 y otro con tres 0 | Global 25, no media de dominios 50 |
| DAT-25 | Nota 80 de control y riesgo con un control | No derivar reducción de 80 %; categorías residuales justificadas por AE/GS |
| DAT-26 | Referencia documental sin versión/localizador o no admitida por criterio | No satisface evidencia requerida; no fingir archivo o verificación |

## 15. Trazabilidad y pendientes

| Entidades | Requerimientos | Casos de uso | Reglas principales |
| --- | --- | --- | --- |
| DD-01 a DD-06 | RF-01 a RF-05, RF-22 | CU-01 a CU-08 | RN-01/RN-02; SEG-01 a SEG-10 |
| DD-07/DD-08 | RF-09; RN-07/RN-10 | CU-13 a CU-19 | CAL-GEN-03; DEC-01 a DEC-11 |
| DD-09 a DD-12 | RF-06 a RF-08 | CU-09 a CU-12 | CAL-ACT-01/02; CAL-EVA-05 |
| DD-13 a DD-15 | RF-10/RF-11/RF-17 | CU-14 a CU-16 | CAL-EVA-01 a CAL-EVA-04 |
| DD-16 a DD-20 | RF-12 a RF-14 | CU-17 a CU-21 | CAL-RIE-01 a CAL-RIE-07 |
| DD-21/DD-22 | RF-15/RF-16 | CU-22 a CU-24 | RN-06; PER-29 a PER-33 |
| DD-23/DD-24 | RF-17 | CU-25 | RNF-06; PER-34/PER-35 |
| DD-25 | RF-21 | CU-28 | RNF-09; PER-38 |
| DD-26 | RF-18 a RF-20 | CU-26/CU-27 | CAL-EVA-02/03; CAL-RIE-07 |

Pendientes específicos antes del modelo físico:

1. Aplicar decisiones APR-02 y completar solo los puntos aún pendientes de DEC: rúbricas, umbrales de madurez, política de cláusulas, vigencia y catálogo; sin PHVA ponderado ni descuentos automáticos en v1.
2. Confirmar límites de texto, normalización de correos/códigos, identificador institucional y tipos físicos de números y fechas.
3. Definir políticas de contraseña, sesiones, invitaciones, recuperación y procedimiento de aprobación de metodología común.
4. Confirmar datos personales y campos documentales de inventario necesarios para la primera versión, evitando recopilar información innecesaria.
5. Definir retención, corrección de archivos, unidad exacta del límite de carga y cuotas por empresa.
6. Aprobar los catálogos propuestos de implementación/verificación, guardado incompleto y condiciones de fechas y evidencias.
7. Diseñar relaciones, restricciones atómicas y control de versiones entre esquema común, empresarial y almacenamiento privado; esto requiere un documento posterior de modelo de datos.

El próximo paso es el modelo de datos y relaciones, acompañado por la especificación detallada de estados y transiciones. Este diccionario no implementa tablas ni cambia archivos Excel o políticas pendientes.
