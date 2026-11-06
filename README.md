# Software MSPI — Documentación

Repositorio de documentación para una aplicación web de gestión de seguridad de la información, con varias empresas y un esquema de base de datos por empresa.

## Documentos

| Orden | Documento | Contenido |
| --- | --- | --- |
| 1 | [Parámetros y requerimientos](01-parametros-y-requerimientos.md) | Objetivo, alcance, funciones, aislamiento y criterios de aceptación |
| 2 | [Matriz de permisos](02-matriz-de-permisos.md) | Cinco roles, 38 acciones autorizables y escenarios de verificación |
| 3 | [Casos de uso](03-casos-de-uso.md) | 28 flujos con condiciones, alternativas, resultados y trazabilidad a los 22 requerimientos funcionales |
| 4 | [Reglas de cálculo y valoración](04-reglas-de-calculo-y-valoracion.md) | 18 reglas, 17 hallazgos en los Excel, 36 casos de referencia y estado de 12 decisiones metodológicas |
| 5 | [Diccionario de datos y validaciones](05-diccionario-de-datos-y-validaciones.md) | 26 entidades conceptuales, campos y condiciones, 15 validaciones transversales y 26 escenarios de aceptación |
| 6 | [Estados y transiciones](06-estados-y-transiciones.md) | Ciclos de vida, condiciones, actores, efectos, concurrencia, diagramas y 31 escenarios de aceptación |
| 7 | [Modelo de datos y aislamiento por esquemas](07-modelo-de-datos-y-aislamiento-por-esquemas.md) | Modelo lógico, integridad, permisos técnicos y 22 escenarios; ocho decisiones DMP aprobadas en principios, detalles físicos pendientes |
| 8 | [Arquitectura y decisiones técnicas](08-arquitectura-y-decisiones-tecnicas.md) | Stack, monolito modular, sesión/API, conexiones, migraciones, pruebas y secuencia backend acordada en APR-08 |
| 9 | [Diseño de pantallas](09-diseno-de-pantallas.md) | Flujos, perfiles, estados y boceto navegable; distingue propuestas visuales de requisitos aprobados |

## Estado del proyecto

Documentación de especificación y preparación de la implementación. Este repositorio contiene documentos y un boceto HTML; el código de aplicación corresponde a los repositorios separados de backend y frontend. Los documentos distinguen decisiones aprobadas, detalles pendientes y verificaciones aún no ejecutadas.

El usuario aprobó alcance y roles de la primera versión en APR-01 del documento 1: una membresía por usuario/empresa con varios roles posibles, permisos combinados solo en la empresa activa y restricciones personales que prevalecen. Auditor solo es de lectura por sí mismo; combinado con gestor recibe sus permisos de edición. No se permite autoaprobar tareas, y AP no obtiene acceso operativo automático. Esta aprobación no cierra decisiones metodológicas DEC ni precisiones EST pendientes.

APR-02 aprueba criticidad por máximo C/I/D, notas 0/20/40/60/80/100, promedio global por control evaluado con avance separado, cinco categorías de riesgo y residual conjunto justificado sin descuentos automáticos. Aprueba mínimos de evidencia/cierre y recorridos de estado destacados. Referencias documentales verificables se admiten si el criterio lo permite. PHVA ponderado se difiere; rúbricas detalladas, madurez global, vigencias y políticas operativas siguen pendientes.

APR-03 aprueba DMP-01/02/03/05/06: PostgreSQL, distribución común/empresarial, pertenencia, identidad técnica por empresa, claves/integridad e historial. APR-04 aprueba DMP-04/07/08: referencias/conexiones acotadas, altas y migraciones recuperables y archivos/recuperación inicial. Las ocho DMP están aprobadas en sus principios; configuración, canales, límites, cuotas, retención y metas operativas siguen pendientes. No se ha creado la base de datos.

Los tres Excel revisados son fuentes funcionales del proyecto; actualmente están en la carpeta superior y no se incorporan automáticamente a este repositorio. Sus nombres y usos se describen en el documento de requerimientos.

## Trazabilidad

- RF: requerimiento funcional; RN: regla de negocio; RNF: requerimiento no funcional; SEG: regla de aislamiento.
- PER: permiso de una acción; PA: escenario de verificación de permisos; CU: caso de uso.
- CAL: regla de cálculo; HAL: hallazgo de fuente; VAL: caso de referencia; DEC: decisión metodológica con estado de aprobación individual.
- DD: entidad del diccionario; DVAL: validación transversal; DAT: escenario de aceptación de datos.
- TR: transición u operación de ciclo de vida; EST: precisión propuesta de estados; ET: escenario de aceptación de transiciones.
- APR: decisión aprobada por el usuario y registrada con alcance explícito.
- MD-C/MD-E: tabla lógica común/empresarial; MOD: regla de integridad; AIS: escenario de aislamiento; DMP: decisión de modelo/arquitectura con estado individual.
- Mantener los identificadores al revisar documentos y actualizar sus enlaces y versiones cuando cambie el alcance.
- Los escenarios documentados son criterios para pruebas futuras, no resultados de pruebas ejecutadas.

APR-05 a APR-07, registrados en el documento 08, confirman el stack y aprueban los principios de modularidad/persistencia, autenticación por sesión, API, conexiones, migraciones, frontend y pruebas/CI. APR-08 ratifica los seis grupos de trabajo backend y acuerda el orden: diagnóstico → estructura modular → migraciones y aislamiento con dos empresas → sesión y permisos → primera funcionalidad completa. Pruebas y CI acompañan las entregas desde el inicio.

La siguiente actividad del backend es el diagnóstico del proyecto real y delimitación de su primera entrega; los entregables y condiciones de avance están en la sección 19 del documento 08. No se requiere volver a aprobar principios ya aceptados. Versiones/configuración, políticas operativas y pendientes metodológicos conservan su estado individual. El modelo y los escenarios documentados no acreditan tablas implementadas ni pruebas superadas.
