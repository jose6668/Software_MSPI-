# Software MSPI — Documentación

Repositorio de documentación para una aplicación web de gestión de seguridad de la información, con varias empresas y un esquema de base de datos por empresa.

## Documentos

| Orden | Documento | Contenido |
| --- | --- | --- |
| 1 | [Parámetros y requerimientos](01-parametros-y-requerimientos.md) | Objetivo, alcance, funciones, aislamiento y criterios de aceptación |
| 2 | [Matriz de permisos](02-matriz-de-permisos.md) | Cinco roles, 38 acciones autorizables y escenarios de verificación |
| 3 | [Casos de uso](03-casos-de-uso.md) | 28 flujos con condiciones, alternativas, resultados y trazabilidad a los 22 requerimientos funcionales |

## Estado del proyecto

Etapa de especificación. No hay implementación de la aplicación en este repositorio. Los documentos distinguen decisiones confirmadas, propuestas y pendientes; las metodologías de cálculo y las tecnologías todavía requieren definición.

Los tres Excel revisados son fuentes funcionales del proyecto; actualmente están en la carpeta superior y no se incorporan automáticamente a este repositorio. Sus nombres y usos se describen en el documento de requerimientos.

## Trazabilidad

- RF: requerimiento funcional; RN: regla de negocio; RNF: requerimiento no funcional; SEG: regla de aislamiento.
- PER: permiso de una acción; PA: escenario de verificación de permisos; CU: caso de uso.
- Mantener los identificadores al revisar documentos y actualizar sus enlaces y versiones cuando cambie el alcance.
- Los escenarios documentados son criterios para pruebas futuras, no resultados de pruebas ejecutadas.

La siguiente etapa propuesta es el modelo de datos y el diseño del esquema común y los esquemas empresariales, conservando los pendientes de metodología y políticas que afecten su estructura.
