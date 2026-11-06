# Parámetros y requerimientos del sistema

Versión: 0.1 — Propuesta inicial para revisión
Fecha: 6 de noviembre de 2026
Nombre provisional: Sistema de Gestión de Seguridad de la Información

## 1. Objetivo

Desarrollar una aplicación web multiempresa para inventariar activos de información, evaluar controles de seguridad, gestionar riesgos y hacer seguimiento a acciones de mejora. La información de cada empresa se almacenará en un esquema propio de la base de datos.

El sistema apoyará la gestión y la documentación. Una calificación o un reporte generado por la aplicación no constituye una certificación de cumplimiento.

## 2. Decisiones y parámetros iniciales

| Parámetro | Definición | Estado |
| --- | --- | --- |
| Tipo de software | Aplicación web | Confirmado por el usuario |
| Modelo de operación | Varias empresas en una plataforma | Confirmado por el usuario |
| Separación de datos | Un esquema de base de datos por empresa | Confirmado por el usuario |
| Base funcional | Los tres archivos Excel existentes | Confirmado por el contexto del proyecto |
| Desarrollo | Desde cero, por etapas | Confirmado por el usuario |
| Idioma inicial | Español | Propuesto |
| Base de datos | Motor relacional con soporte de esquemas; candidato: PostgreSQL | Pendiente de decisión técnica |
| Acceso inicial | Empresas y usuarios creados por invitación; sin registro público | Propuesto |
| Despliegue | Entorno de desarrollo y entorno de demostración separados | Propuesto |
| Datos de demostración | Empresas y usuarios ficticios | Propuesto |
| Diseño visual y tecnologías web | Se definirán después del alcance y los requerimientos | Pendiente |

Los estados «propuesto» y «pendiente» no representan decisiones aprobadas por el usuario.

## 3. Fuentes funcionales

| Archivo | Uso previsto |
| --- | --- |
| articles-401779_recurso_1.xlsx | Información de la organización, escala de valoración, cláusulas, controles por dominio y referencia NIST |
| articles-401780_recurso_1.xlsx | Inventario, responsables, clasificación y criticidad de activos |
| articles-401781_recurso_1.xlsx | Amenazas, vulnerabilidades, controles, valoración inherente y residual, tratamiento y seguimiento |

Antes de programar los cálculos se documentarán las escalas, fórmulas, redondeos, límites y casos sin datos. Se revisará la correspondencia entre las versiones y referencias de controles utilizadas en los archivos. Los valores ya diligenciados no se asumirán como datos reales de una empresa ni como reglas universales.

## 4. Alcance propuesto de la primera versión

Incluye empresas, usuarios y membresías; procesos y responsables; inventario de activos; evaluaciones de cláusulas y controles; riesgos y controles asociados; acciones de mejora; evidencias; panel de resultados; reporte básico y registro de actividad.

Quedan para etapas posteriores: importación automática de Excel, reportes avanzados, integración con servicios externos, inicio de sesión corporativo, notificaciones por correo, simulaciones y un módulo NIST independiente. La primera versión no ejecutará escaneos de vulnerabilidades ni implementará controles técnicos sobre los sistemas de las empresas.

## 5. Roles y permisos propuestos

Los roles pertenecen a una membresía dentro de una empresa. Un mismo usuario podrá tener roles distintos en empresas diferentes. No se admitirán roles personalizados en la primera versión.

| Rol | Permisos principales | Límites |
| --- | --- | --- |
| Administrador de plataforma | Crear, habilitar o suspender empresas; gestionar el servicio y su aprovisionamiento | Sin lectura habitual de activos, riesgos o evidencias de empresas desde la aplicación |
| Administrador de empresa | Gestionar miembros, procesos, configuración y todos los módulos de su empresa | No puede administrar otras empresas ni asignar privilegios de plataforma |
| Gestor de seguridad | Gestionar activos, evaluaciones, riesgos, acciones y revisiones | No administra membresías ni privilegios |
| Responsable de acciones | Consultar sus tareas y el contexto mínimo necesario; registrar avances y evidencias | No consulta el inventario completo ni aprueba sus propias tareas |
| Auditor o lector | Consultar información y reportes de su empresa | Sin modificaciones de registros operativos |

El acceso de emergencia o soporte al contenido de una empresa queda excluido de la primera versión. Los privilegios de infraestructura y base de datos se administrarán por separado de estos roles de aplicación. Sus operadores pueden tener acceso técnico y requieren controles operativos específicos.

## 6. Requerimientos funcionales

| ID | Requerimiento | Criterio de aceptación |
| --- | --- | --- |
| RF-01 | Registrar empresas y aprovisionar su esquema | Una empresa queda disponible solamente cuando su esquema y configuración están completos; un fallo no deja una empresa operativa a medias |
| RF-02 | Iniciar y cerrar sesión | Una sesión válida permite acceder a empresas autorizadas; al cerrarla se invalida su acceso autenticado |
| RF-03 | Gestionar invitaciones y membresías | Solo el administrador autorizado puede invitar y asignar roles de su empresa; una invitación vencida, revocada o usada no puede reutilizarse |
| RF-04 | Seleccionar la empresa activa | El selector muestra solo membresías vigentes; cambiar de empresa actualiza datos y permisos de toda la interfaz |
| RF-05 | Suspender empresas o revocar membresías | Las solicitudes posteriores a la suspensión o revocación se rechazan, incluso con una sesión previamente iniciada |
| RF-06 | Registrar procesos y responsables | Un activo puede asociarse a un proceso y responsable válidos de su empresa |
| RF-07 | Gestionar activos | Crear, consultar, actualizar y archivar activos con código único por empresa, nombre, tipo, proceso, responsable y descripción |
| RF-08 | Clasificar activos | Registrar confidencialidad, integridad, disponibilidad y campos aplicables de datos personales; calcular criticidad con reglas documentadas y distinguir datos faltantes |
| RF-09 | Consultar el catálogo de controles y cláusulas | Cada elemento tiene identificador, versión, descripción y dominio o categoría; las referencias históricas conservan su versión |
| RF-10 | Crear evaluaciones periódicas | Una empresa puede tener varias evaluaciones con fecha, responsable y estado; una nueva evaluación no sobrescribe la anterior |
| RF-11 | Diligenciar y cerrar evaluaciones | Guardar calificación, aplicabilidad, justificación y evidencia por elemento; los pendientes quedan visibles y una evaluación cerrada no se edita directamente |
| RF-12 | Gestionar riesgos | Registrar un activo principal, amenaza, vulnerabilidad, descripción, responsable y tratamiento por riesgo; todas las relaciones pertenecen a la misma empresa |
| RF-13 | Calcular y registrar valoraciones del riesgo | Mostrar probabilidad, impacto y nivel inherente y residual según una metodología identificada; los campos incompletos producen un estado pendiente, no un resultado válido de cero |
| RF-14 | Asociar controles a riesgos | Permitir varios controles por riesgo y varios riesgos por control, registrando implementación, evidencia y efectividad; proponer un control no modifica automáticamente el residual |
| RF-15 | Gestionar acciones de mejora | Crear tareas con responsable autorizado, fecha límite, descripción y vínculo al riesgo o hallazgo; mostrar vencimiento separado del estado de ejecución |
| RF-16 | Revisar acciones | El responsable solicita revisión; un gestor o administrador distinto del responsable aprueba o devuelve la tarea con motivo |
| RF-17 | Adjuntar y consultar evidencias | Vincular archivos a evaluaciones, controles o acciones; validar formato y tamaño; exigir autorización en carga y descarga |
| RF-18 | Mostrar panel y matriz de riesgos | Presentar resultados de la empresa activa, evaluaciones pendientes, riesgos por nivel y acciones vencidas; los totales coinciden con los registros accesibles |
| RF-19 | Buscar y filtrar | Filtrar registros por campos relevantes y paginar resultados sin incluir datos de otra empresa |
| RF-20 | Generar un reporte básico | Producir una vista imprimible con empresa, fecha, filtros y metodología; respetar los mismos permisos de consulta |
| RF-21 | Consultar el registro de actividad | El administrador de empresa consulta eventos propios con actor, fecha, acción y objeto; el registro no se modifica desde la interfaz |
| RF-22 | Recuperar el acceso | Permitir restablecer credenciales mediante un mecanismo verificado, con enlaces o códigos temporales de un solo uso; el canal se definirá antes de implementar |

## 7. Aislamiento entre empresas

Un esquema separado organiza los datos, pero no reemplaza la autorización. La aplicación debe aplicar las siguientes reglas:

| ID | Regla verificable |
| --- | --- |
| SEG-01 | El servidor valida identidad, membresía vigente, empresa activa, permiso de acción y acceso al objeto en cada solicitud |
| SEG-02 | La empresa solicitada se resuelve contra un registro interno autorizado; un identificador enviado por el navegador nunca basta para conceder acceso |
| SEG-03 | Los nombres de esquema son identificadores internos generados y validados por el sistema; no se construyen directamente con nombres de empresa o texto del usuario |
| SEG-04 | Cada operación de datos usa un contexto de empresa acotado; al reutilizar conexiones no se conserva el contexto de una solicitud anterior |
| SEG-05 | Las credenciales de ejecución no son superusuario; el aprovisionamiento y las migraciones usan privilegios separados. El modelo exacto de permisos de base de datos se definirá en el diseño técnico |
| SEG-06 | No se permiten relaciones operativas entre objetos de esquemas de empresas diferentes |
| SEG-07 | Evidencias, reportes, búsquedas, cachés y tareas en segundo plano mantienen el contexto y la autorización de empresa; los adjuntos no tienen enlaces públicos permanentes |
| SEG-08 | Modificar URL, parámetros, cuerpo de una solicitud o identificadores de objetos no permite leer, crear, editar, eliminar ni descargar datos ajenos |
| SEG-09 | Los registros técnicos no exponen contraseñas, tokens, archivos adjuntos ni contenidos sensibles innecesarios; los rechazos no revelan datos de otras empresas |
| SEG-10 | Se prueba el aislamiento con al menos dos empresas y usuarios de distintos roles, incluyendo peticiones alternadas que reutilicen conexiones |

Se propone un esquema común para identidades, empresas, membresías y versiones de catálogos. Cada esquema empresarial contendrá sus procesos, activos, evaluaciones, valoraciones de controles, riesgos, acciones, metadatos de evidencias e historial. Los archivos físicos se guardarán en almacenamiento privado con separación lógica por empresa. Este reparto es preliminar, no el modelo definitivo de tablas.

## 8. Reglas de negocio

- RN-01: Un usuario necesita una membresía activa para operar en una empresa activa. La pertenencia a una empresa no concede acceso a otra.
- RN-02: Una empresa debe conservar al menos un administrador activo; una operación que elimine al último se rechaza.
- RN-03: Los códigos de activos son únicos dentro de cada empresa. Empresas diferentes pueden usar el mismo código.
- RN-04: Un control del catálogo se distingue de su evaluación y de su implementación concreta. Las evaluaciones y evidencias pertenecen a una empresa.
- RN-05: «No aplica», «sin evaluar» y una calificación de cero son estados diferentes. «No aplica» requiere justificación. Su efecto sobre promedios se definirá con la metodología.
- RN-06: Una acción completada requiere revisión y evidencia cuando corresponda; no supone por sí sola que el control sea efectivo ni que el riesgo haya disminuido.
- RN-07: Las valoraciones conservan fecha, autor y versión de la metodología. Las actualizaciones del catálogo no alteran silenciosamente resultados históricos.
- RN-08: Los registros referenciados se archivan en lugar de eliminarse físicamente en los flujos habituales. El archivado no destruye su historial.
- RN-09: Un riesgo puede aceptarse únicamente con justificación, aprobador autorizado y fecha de revisión. Se propone reservar esa aprobación al administrador de empresa.
- RN-10: Las escalas y fórmulas iniciales serán versionadas y comunes; personalizarlas por empresa queda fuera de la primera versión.
- RN-11: Las fechas límite se interpretan según la zona horaria empresarial; los eventos del sistema conservan una marca temporal uniforme.

## 9. Estados iniciales propuestos

| Entidad | Estados y transición básica |
| --- | --- |
| Empresa | En preparación → Activa ↔ Suspendida; Error de aprovisionamiento requiere resolución antes de activar |
| Activo | Activo → Archivado; restauración autorizada con historial |
| Evaluación | Borrador → En revisión → Cerrada; desde revisión puede regresar a borrador con motivo; una corrección posterior al cierre crea una revisión vinculada |
| Riesgo | Identificado → En análisis → En tratamiento → En seguimiento → Cerrado; los cambios requieren sus datos y justificaciones aplicables |
| Acción | Pendiente → En ejecución → En revisión → Completada; revisión rechazada retorna a ejecución; cancelación exige motivo |

Los estados de riesgo y acción son independientes. Aceptar un riesgo es una decisión de tratamiento y no equivale automáticamente a cerrarlo. Un cierre exige justificación, valoración vigente y aprobación del administrador o gestor autorizado. «Vencida» se calcula por fecha y no sustituye el estado de la acción.

## 10. Requerimientos no funcionales y metas propuestas

| ID | Requerimiento | Verificación prevista |
| --- | --- | --- |
| RNF-01 | Seguridad de acceso | Credenciales protegidas con hash adecuado, secretos fuera del repositorio, consultas parametrizadas, sesiones con vencimiento y protección contra intentos repetidos; HTTPS fuera del desarrollo local |
| RNF-02 | Usabilidad | Formularios en español con etiquetas, errores comprensibles, navegación por teclado, foco visible y estados que no dependan solo del color |
| RNF-03 | Diseño adaptable | Operaciones principales utilizables en escritorio y móvil; tablas extensas con navegación adecuada |
| RNF-04 | Integridad | Validaciones de servidor, restricciones de base de datos y transacciones para operaciones relacionadas; conflictos de edición detectados sin sobrescritura silenciosa |
| RNF-05 | Rendimiento | Meta inicial: 95 % de consultas paginadas habituales en hasta 2 segundos de servidor, con 10 empresas, 1.000 activos y 500 riesgos por empresa y 20 usuarios concurrentes; excluir cargas, descargas y reportes; documentar el entorno de prueba |
| RNF-06 | Adjuntos | Propuesta inicial: PDF, PNG y JPEG, hasta 10 MB por archivo; comprobar contenido además de extensión y evitar ejecución de archivos |
| RNF-07 | Recuperación | Copias diarias y prueba de restauración en un entorno separado. Metas propuestas: pérdida máxima de 24 horas de datos y recuperación en 8 horas; validar viabilidad con el alojamiento elegido |
| RNF-08 | Mantenimiento | Migraciones versionadas aplicables a todos los esquemas, estado de versión por empresa y manejo de fallos; una migración incompleta no habilita acceso incompatible |
| RNF-09 | Trazabilidad | Registrar cambios relevantes y decisiones con actor, empresa, fecha y objeto; limitar lectura y escritura del historial |
| RNF-10 | Calidad | Verificar permisos, aislamiento, cálculos, transiciones y recuperación; usar datos ficticios para las pruebas |

Las cifras de capacidad, límites y recuperación son metas propuestas, no garantías actuales. Se ajustarán antes de fijar la arquitectura y el despliegue.

## 11. Criterios mínimos para aceptar la primera versión

1. Crear dos empresas con esquemas separados y completar en cada una el flujo activo → riesgo → control → acción → revisión.
2. Un usuario de la empresa A no accede a datos de B por la interfaz, solicitudes directas, adjuntos o reportes, aunque conozca identificadores válidos.
3. Un usuario con membresías en ambas cambia de contexto y recibe únicamente los permisos correspondientes a la empresa activa.
4. Revocar una membresía o suspender una empresa impide nuevas operaciones con sesiones existentes.
5. Las valoraciones coinciden con casos de referencia documentados, incluidos límites, ceros, campos faltantes y elementos no aplicables.
6. Una evaluación nueva conserva las anteriores; cambiar el catálogo no modifica los resultados cerrados.
7. Una acción no se aprueba por su propio responsable y conserva el motivo de devolución o aprobación.
8. El panel y el reporte básico concilian con los datos autorizados y no presentan pendientes como resultados completos.
9. Se demuestra una restauración y se comprueba que ambos esquemas conservan sus datos y aislamiento.

## 12. Decisiones pendientes antes del diseño técnico

- Confirmar el alcance y los cinco roles propuestos; determinar si el auditor necesita comentar en una etapa posterior.
- Acordar la metodología exacta de criticidad, promedios, riesgo residual y combinación de varios controles; resolver discrepancias entre los Excel.
- Definir campos obligatorios y evidencias requeridas para cerrar evaluaciones, acciones y riesgos.
- Seleccionar tecnologías, alojamiento y mecanismo de invitación y recuperación de acceso.
- Definir retención de evidencias e historial, cuotas por empresa y procedimiento de retiro o eliminación de una empresa.
- Confirmar metas de capacidad, respaldo y recuperación según los recursos disponibles.

## 13. Orden de trabajo

1. Revisar y ajustar este documento de requerimientos.
2. Especificar casos de uso y matriz detallada de permisos.
3. Definir el modelo de datos común y el esquema empresarial, junto con el aprovisionamiento y aislamiento.
4. Diseñar pantallas y recorridos del usuario.
5. Seleccionar tecnologías y organizar la implementación por módulos.
6. Implementar y verificar cada etapa con los criterios acordados.

Este documento define una base revisable. No inicia la implementación ni convierte sus propuestas en decisiones confirmadas.
