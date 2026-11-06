# Diseño de pantallas

Fecha: 6 de noviembre de 2026.
Estado: dirección visual Panorama elegida; tercera entrega revisada según observaciones de UX. Corrección de precarga incorporada; nuevos detalles de navegación, páginas, perfiles y estados disponibles para revisión, sin aprobación automática.

## 1. Alcance de esta etapa

Diseño y bocetos, sin implementación de la aplicación ni cambios en código frontend/backend. Se conservan los documentos 01 al 08. Las reglas y permisos aprobados en esos documentos prevalecen sobre los ejemplos visuales.

## 2. Acuerdo de dirección visual

El usuario expresa «me gustó en Panorama» después de comparar Panorama y Enfoque. Se registra Panorama como dirección elegida para continuar: navegación lateral en escritorio, acento azul y resultados de la empresa antes de las prioridades de seguimiento.

Esta elección no aprueba automáticamente cada cifra, etiqueta, espaciado, interacción, formulario o decisión metodológica pendiente. Todos los datos del boceto son ficticios.

## 3. Panel principal propuesto

- Empresa activa y todos los roles de la membresía visibles, sin selector de rol que cambie permisos.
- Evaluación identificada, puntaje provisional, avance y pendientes separados; resultados por dominio y cláusulas sin mezclarlos ni presentar un global PHVA.
- Resumen de activos, riesgos y acciones de la empresa al corte, con ámbito explícito independiente de la evaluación seleccionada.
- Riesgos pendientes o que requieren revisión separados de niveles residuales vigentes.
- Prioridades de seguimiento con estado de ejecución y vencimiento diferenciados.
- Una entrega propia en revisión explica que debe revisarla otra persona autorizada.
- Adaptación móvil mediante reorganización de navegación y apilamiento de bloques. Distribución exacta pendiente de revisión.

## 4. Permisos conservados

Roles combinados solo dentro de la empresa activa. AP no consulta datos operativos por su atribución de plataforma. RA solo, sin otros roles que concedan consulta, tiene su lista de tareas y contexto mínimo, no este panel general. AU solo no edita. GS no administra membresías ni acepta formalmente riesgos. La aprobación de acciones exige otra persona, aunque el responsable acumule roles. AE/GS puede cerrar una evaluación propia conforme a APR-01; esa excepción no se extiende a las acciones.

Cambiar empresa requerirá validar membresía y estado, actualizar permisos, advertir cambios sin guardar y evitar mostrar respuestas o datos del contexto anterior. La interfaz orienta; no sustituye la autorización del servidor.

## 5. Ejemplo ilustrativo conciliado

Empresa: Andina Servicios S.A.S. Persona: Laura Méndez, GS + RA. Corte: 6 de noviembre de 2026, 10:00, America/Bogota.

- 93 controles: 60 evaluados, 20 aplicables sin calificar, 5 con aplicabilidad sin decidir y 8 no aplicables justificados.
- Puntaje provisional: 3.840 / 60 = 64,00 puntos; avance: 60 / 85 = 70,59 %. Las 6 notas cero están incluidas en los 60 evaluados.
- Dominios: organizacionales 24 evaluados de 34, media 70,00; personas 6/7, físicos 10/12 y tecnológicos 20/32, cada uno con media 60,00. Los denominadores excluyen N justificados e incluyen aplicabilidad sin decidir.
- 48 activos vigentes: 39 clasificados y 9 pendientes.
- 18 riesgos abiertos: 12 con residual vigente (1 extremo, 4 altos, 5 moderados, 2 bajos), 3 pendientes y 3 que requieren revisión, separados en este ejemplo.
- 16 acciones abiertas: 5 pendientes, 7 en ejecución y 4 en revisión. Las 3 vencidas son un subconjunto de las abiertas, no otro estado ni un total adicional.

Cero es nota registrada, pendiente indica falta de información, no aplica exige justificación y provisional describe el agregado incompleto. No se asigna nivel global de madurez ni se afirma certificación.

## 6. Boceto HTML y navegación

[Abrir el panel Panorama](bocetos/panorama-panel.html).

HTML de presentación para abrir en navegador, sin conexión ni instalación. El mismo archivo ahora conecta Panel, Activos, Evaluaciones, Riesgos, Acciones y Reportes. Se incluye un acceso separado al recorrido de inicio de sesión, invitación, recuperación y selección de empresa. Los formularios y decisiones se previsualizan sin guardar datos ni cambiar los totales. El selector empresarial es ilustrativo: solo Andina tiene contenido diseñado. La entrada de demostración no implementa autenticación ni autorización real.

## 7. Pendientes de diseño

Se han elaborado propuestas de acceso por invitación, selección de empresa y recuperación; activos y clasificación; evaluaciones; riesgos y controles; acciones, entrega y revisión independiente. Están disponibles en HTML para revisión, sin aprobación del detalle funcional o visual por asociación con Panorama.

Revisar densidad, jerarquía, navegación móvil, listados y formularios. Los estados vacíos, de carga, error, conflicto y pérdida de autorización ya tienen bocetos según la sección 9. Se propone teclado, foco visible, contraste suficiente, objetivos táctiles y estados expresados también con texto; validación visual y de accesibilidad completa pendiente.

Las rúbricas, evidencia por criterio, umbrales agregados, política de no aplica en cláusulas, canales/duraciones de acceso y retención siguen pendientes según la documentación. La dirección visual no los resuelve.

## 8. Segunda entrega: opciones del menú y recorridos propuestos

El usuario solicita ver las demás opciones del menú. Se amplía únicamente el boceto documental, sin modificar los repositorios frontend/backend ni los documentos 01 al 08.

| Pantalla | Recorrido disponible en el boceto | Límite |
| --- | --- | --- |
| Activos | Buscar/filtrar/paginar 48 activos; abrir ficha; previsualizar registro/edición y clasificación; consultar riesgos vinculados | Catálogos y campos condicionales de datos personales requieren revisión; no archiva ni guarda |
| Evaluaciones | Consultar diagnóstico; recorrer 93 controles ilustrativos; filtrar respuestas; diligenciar una respuesta; consultar bloqueos de cierre e histórico; previsualizar nueva evaluación o corrección vinculada | No cierra evaluaciones ni publica metodología; no habilita N libremente en cláusulas |
| Riesgos | Buscar/filtrar/paginar 18 riesgos; consultar matriz 5×5 de residuales vigentes; abrir ficha; previsualizar alta y valoración categórica; crear acción desde riesgo | No valida suficiencia de evidencia ni confirma vigencia; GS no puede formalizar aceptación |
| Acciones | Consultar 16 tareas; filtrar todas, propias, revisables y vencidas; abrir contexto; previsualizar avance/entrega propia o decisión sobre entrega ajena | No ofrece aprobación para una entrega propia; entrega en revisión congelada; no cambia estados |
| Reportes | Consultar reporte con empresa, fecha, alcance, resultados, pendientes y metodología; imprimir o guardar como PDF mediante navegador | Sin Excel, certificación, historial administrativo ni exportaciones avanzadas |
| Acceso | Inicio simulado → membresías → Andina; invitación con roles fijos; recuperación con respuesta genérica | No envía correos, contraseñas, invitaciones ni solicitudes; no hay registro público |

### Decisiones nuevas de diseño, pendientes de comentarios

Registro histórico de la segunda entrega. La sección 9 sustituye la propuesta de diálogos extensos y ajusta la paginación móvil.

- Menú lateral interactivo y una sola pantalla principal visible; fichas, formularios y revisiones en diálogos. Revisar si los formularios largos deben convertirse en páginas.
- Listados de diez registros por página, búsqueda y filtro visibles; controles de evaluación de ocho por página dentro del diálogo. Son parámetros de presentación del boceto, no contratos API ni límites aprobados.
- Listados convertidos a fichas apiladas en móvil; matriz conservada con etiquetas de texto y conteos. Revisión visual móvil y contraste completos todavía pendientes.
- Acción principal contextual en cada pantalla y explicación explícita de los bloqueos en vez de permitir acciones sin autorización.
- Vista imprimible con resultados y pendientes separados; cláusulas sin global PHVA. No se incorporan indicadores metodológicos pendientes.

### Datos ilustrativos adicionales

Se expanden los conteos existentes a 48 activos, 18 riesgos, 16 acciones y 93 respuestas ficticias. Los códigos DEM de controles y las referencias de evidencia no son catálogos normativos ni archivos descargables. La evaluación cerrada de agosto es otra instantánea ficticia: 85 aplicables evaluados, 8 N justificados, 60,00 puntos; no modifica noviembre.

Las cuatro acciones en revisión son AC-018, AC-021, AC-022 y AC-023; solo AC-021 corresponde a Laura. Los plazos vencidos son AC-002, AC-010 y AC-014. El estado de estas últimas se conserva independiente del vencimiento.

### Verificación y límites de la entrega

Comprobación de sintaxis JavaScript, referencias de plantillas, conciliación de datos y ausencia de dependencias remotas en el HTML local. El navegador integrado rechazó abrir una URL de archivo local por su política de protocolos; no se declara inspección visual en navegador ni prueba integral de interacciones. Se conserva esa revisión como pendiente al abrir el archivo local en Edge o Chrome.

El HTML es un prototipo documental navegable. No contiene aplicación React, API, base de datos, seguridad real ni persistencia. Los formularios no cambian los totales de la demostración, no leen archivos y no siguen enlaces de evidencia.

## 9. Tercera entrega: observaciones de UX incorporadas

El usuario aporta cinco recomendaciones de revisión. Se corrige el defecto de precarga y se presentan los demás ajustes como propuestas visuales verificables. Se conservan los requisitos aprobados; no se implementa la aplicación ni se modifican los documentos 01 al 08.

| Observación | Ajuste del boceto | Estado |
| --- | --- | --- |
| Precarga incorrecta de clasificación | Ficha y formulario consumen los mismos C/I/D del activo. ACT-033 abre con baja/baja/baja (1/1/1). ACT-040 y los demás incompletos conservan C=5, I=null y D=1; I se representa como Pendiente, sin completar por defecto | Corregido y comprobado para los 48 activos |
| Exceso de espacio y desplazamiento en móvil | Cabecera compacta, detalles de persona plegables y navegación mediante selector. Resúmenes plegables en móvil. Cinco filas por página en móvil y diez en escritorio, con recálculo al cambiar de tamaño | Propuesta disponible; revisión visual pendiente |
| Diálogos extensos | Fichas de riesgo, tareas, evaluaciones y formularios largos pasan al área principal como páginas con Volver. Diálogos reservados para la ficha breve de activo, confirmaciones y decisiones cortas | Propuesta disponible |
| Falta de vistas por rol | Laboratorio de diseño separado del producto para simular personas; Mis acciones para RA solo, administración empresarial para AE, espacio técnico para AP y consulta para AU | Propuesta disponible, respetando PER |
| Estados pendientes | Bocetos de carga, error, conflicto, membresía revocada y sesión vencida; protección de borrador y cambio de empresa | Propuesta disponible |

### Personas de demostración y alcance

| Persona | Roles de Andina | Vista y restricciones |
| --- | --- | --- |
| Laura Méndez | GS + RA | Gestión y tareas asignadas; no administra miembros ni acepta riesgos; no revisa AC-021 propia |
| Diego Ruiz | RA | Solo Mis acciones, con sus seis tareas y contexto mínimo. Sin panel general, inventario, riesgos completos, reportes ni revisión de entregas |
| Camila Ríos | AE + GS + RA | Añade Miembros y roles, Configuración y Actividad; puede previsualizar aceptación de riesgo con valoración vigente. No revisa AC-018 ni AC-023 propias |
| Elena Torres | AU | Consulta empresarial y reporte, sin formularios editables ni decisiones de revisión |
| Nicolás Vega | AU + GS | Conserva edición por GS; no se presenta como solo lectura ni recibe administración de miembros |
| Samuel Pérez | AP sin membresía | Metadatos, estado y alta de empresas; sin navegación operativa ni contenido empresarial |

El laboratorio no es un selector de roles de la aplicación. Cambia personas ficticias únicamente para revisar el diseño; no crea, asigna ni modifica permisos reales. Los nombres y roles completos permanecen consultables en la cabecera.

Laura tiene otra membresía ficticia en Litoral únicamente como AU. Cambiar a Litoral retira las pantallas y cifras de Andina y presenta una ausencia de muestra, no valores cero. Los roles de Andina no se suman a los de Litoral. No hay datos operativos de Litoral en la demostración.

### Borradores y estados de interacción

- Al editar un formulario se indica Borrador modificado. Navegar, volver, cambiar empresa o cambiar de persona solicita permanecer o descartar. Permanecer conserva los campos en memoria; descartar continúa sin guardar. No se ofrece un guardado real inexistente.
- Carga: estructura neutral y mensaje de consulta, sin datos de otro contexto ni resultados cero; Simular respuesta disponible devuelve la vista previa.
- Error: mensaje comprensible, referencia técnica ilustrativa sin detalles internos y Reintentar; se conserva el borrador de la muestra.
- Conflicto: compara el borrador conservado con una referencia de versión reciente. No sobrescribe, fusiona ni descarta automáticamente. Se puede volver al borrador o consultar la referencia.
- Membresía revocada: retira el contenido y borradores empresariales, deshabilita la navegación operativa y explica el acceso retirado.
- Sesión vencida: retira el contenido y borrador y ofrece volver al acceso. No promete recuperación de datos no guardados ni fija una duración de sesión.

Estos estados se activan manualmente en el laboratorio; no representan fallos, sesiones o solicitudes reales. La opción Normal restablece el contexto de demostración cuando corresponde.

### Comprobaciones de esta revisión

Se comprueban sintaxis JavaScript, selección inicial de las tres dimensiones de los 48 activos, faltantes nulos, paginación móvil, alcance por persona, combinación AU+GS, separación empresarial, botones de revisión según asignación y guarda del borrador antes de navegar. Se mantienen los controles anteriores de conciliación de datos y ausencia de red/persistencia.

Son comprobaciones de datos y lógica del prototipo, sin navegador ni servidor. No se declara validación visual integral ni certificación de accesibilidad. La política del navegador integrado que rechazó archivos locales en la segunda entrega no se elude; la inspección visual final se realizará al abrir el HTML en el equipo del usuario.

Pendientes para acordar: comodidad del selector móvil, distribución de los formularios largos, presentación del contexto mínimo y claridad de los mensajes de error/conflicto. Los cambios no resuelven políticas metodológicas, de evidencia, sesión o retención aún abiertas.
