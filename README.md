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

## Estado del proyecto

Etapa de especificación. No hay implementación de la aplicación en este repositorio. Los documentos distinguen decisiones confirmadas, propuestas y pendientes; las metodologías de cálculo y las tecnologías todavía requieren definición.

El usuario aprobó alcance y roles de la primera versión en APR-01 del documento 1: una membresía por usuario/empresa con varios roles posibles, permisos combinados solo en la empresa activa y restricciones personales que prevalecen. Auditor solo es de lectura por sí mismo; combinado con gestor recibe sus permisos de edición. No se permite autoaprobar tareas, y AP no obtiene acceso operativo automático. Esta aprobación no cierra decisiones metodológicas DEC ni precisiones EST pendientes.

APR-02 aprueba criticidad por máximo C/I/D, notas 0/20/40/60/80/100, promedio global por control evaluado con avance separado, cinco categorías de riesgo y residual conjunto justificado sin descuentos automáticos. Aprueba mínimos de evidencia/cierre y recorridos de estado destacados. Referencias documentales verificables se admiten si el criterio lo permite. PHVA ponderado se difiere; rúbricas detalladas, madurez global, vigencias y políticas operativas siguen pendientes.

Los tres Excel revisados son fuentes funcionales del proyecto; actualmente están en la carpeta superior y no se incorporan automáticamente a este repositorio. Sus nombres y usos se describen en el documento de requerimientos.

## Trazabilidad

- RF: requerimiento funcional; RN: regla de negocio; RNF: requerimiento no funcional; SEG: regla de aislamiento.
- PER: permiso de una acción; PA: escenario de verificación de permisos; CU: caso de uso.
- CAL: regla de cálculo; HAL: hallazgo de fuente; VAL: caso de referencia; DEC: decisión metodológica con estado de aprobación individual.
- DD: entidad del diccionario; DVAL: validación transversal; DAT: escenario de aceptación de datos.
- TR: transición u operación de ciclo de vida; EST: precisión propuesta de estados; ET: escenario de aceptación de transiciones.
- APR: decisión aprobada por el usuario y registrada con alcance explícito.
- Mantener los identificadores al revisar documentos y actualizar sus enlaces y versiones cuando cambie el alcance.
- Los escenarios documentados son criterios para pruebas futuras, no resultados de pruebas ejecutadas.

La siguiente etapa propuesta es revisar los pendientes metodológicos y las precisiones funcionales y diseñar el modelo de datos y los esquemas, incorporando versiones, historial y restricciones de transición. Las entidades del documento 5 son conceptuales, no tablas implementadas. Las fórmulas observadas y las propuestas del software se distinguen en el documento 4; las precisiones de estados se identifican en el documento 6. No equivalen a políticas ya aprobadas.
