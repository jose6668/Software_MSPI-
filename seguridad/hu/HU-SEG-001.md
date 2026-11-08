# HU-SEG-001 — Diagnóstico y matriz de seguridad

Estado: lista para revisión; rama publicada y PR abierto hacia `Develop`.
Rama: `docs/HU-SEG-001-diagnostico-seguridad`.
Base documental: `Develop`, creada desde `main` en el commit `964249cbd00d13c3fde06e4451f7948d583531b8` por instrucción del usuario.
Backend revisado: `Develop`, commit `814753bbf7786859c1385a9ad3585d0f66648d39`.
Frontend revisado: commit `86a30f10f671d0c04b982d8c7d1958e19892dfc6`.
PR hacia Develop: [#1](https://github.com/jose6668/Software_MSPI-/pull/1).

Como equipo del proyecto académico, queremos identificar defensas, brechas y pruebas de seguridad antes de implementar el frontend, para conservar aislamiento, integridad y disponibilidad ante un evaluador que conoce todos los repositorios y puede acceder desde el equipo, LAN o Internet.

## Alcance

Incluye modelo de amenazas inicial, revisión estática de controles, escaneo de secretos actual/histórico, consulta de dependencias, matriz priorizada, inventario ASVS L1/L2 y plan de pruebas con resultados reales separados de los pendientes.

Incluye la publicación de la rama documental y un PR hacia `Develop` por instrucción del usuario. No incluye correcciones de aplicación, despliegue, nuevas funciones frontend ni ataques/carga contra una aplicación en ejecución. No se promete cumplimiento completo de ASVS ni ausencia de vulnerabilidades.

Trazabilidad: SEG-01 a SEG-10, RNF-01/04/06/07/09/10, DMP-03/04/08, ARQ-09/10/12/14 y nueva prioridad de seguridad del usuario. ASVS 5.0.0 es la referencia técnica de esta entrega; su aplicabilidad completa sigue en evaluación.

## Criterios y evidencia

| Criterio | Evidencia y resultado |
| --- | --- |
| CA-01. Registrar escenario, activos y límites, incluidos los tres accesos y conocimiento del código | Diagnóstico §4; límites de privilegio local/admin explícitos |
| CA-02. Revisar secretos actuales e históricos sin divulgar valores | Gitleaks 8.30.1 verificado, seis ejecuciones salida 0/cero hallazgos; revisión complementaria y límites registrados |
| CA-03. Revisar dependencias sin declarar limpios lotes fallidos | Árbol Maven y OSV: 193 pares únicos, ocho lotes completos, cero coincidencias; plataforma/plugins excluidos explícitamente |
| CA-04. Distinguir controles existentes, brechas y pruebas no ejecutadas | Matriz de 30 requisitos propios; diagnóstico con H-01 a H-11 y fuentes S01–S11 |
| CA-05. Dejar aceptación y secuencia para frontend/seguridad | Plan P01–P30 y backlog; criterios de avance y exposición distintos |
| CA-06. Conservar el estado de implementación y el alcance documental | Solo archivos documentales/evidencias; sin cambio backend/frontend, secretos o apertura de servicios |

## Rúbrica adaptada

| Dimensión | Resultado |
| --- | --- |
| R1 Alcance/trazabilidad | 2: criterios, revisiones y referencias registradas |
| R2 Arquitectura | 2: revisión respeta contratos, repositorios y ámbito; no modifica implementación |
| R3 Seguridad | 2 para esta entrega: escáneres redactados y sin secretos publicados; no representa seguridad completa del producto |
| R4 Datos/evidencia | 2: resultados estructurados, versiones y hashes; sin datos reales de empresas |
| R5 Contrato | N/A: no cambia API; condiciones de integración sí documentadas |
| R6 Verificación | 2 para diagnóstico: comprobaciones ejecutadas y límites registrados; pruebas dinámicas posteriores visibles |
| R7 Construcción | N/A para documentación; árbol de dependencias resuelto, sin declarar compilación/test nuevos |
| R8 Revisión/entrega | 2: diff, enlaces, JSON y huellas del contenido versionado revisados; rama publicada y PR #1 abierto hacia Develop |

La entrega no se declara integrada. Documentación, backend y frontend usan ramas de trabajo y PR hacia `Develop`; publicar el PR no implica fusionarlo.

## Siguiente entrega recomendada

HU-SEG-002 para prevención de secretos en los repositorios, seguida de las correcciones de credenciales/sesión y límites identificadas. Puede definirse la estructura frontend incorporando la matriz, manteniendo bloqueada la exposición hasta cerrar los P1 aplicables y ejecutar las verificaciones.
