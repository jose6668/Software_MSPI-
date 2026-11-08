# Matriz inicial de seguridad MSPI

Fecha: 8 de noviembre de 2026. Base y alcance: [diagnóstico](DIAGNOSTICO-SEGURIDAD.md).

## Interpretación

La matriz es específica de la aplicación y prioriza el trabajo. Las referencias ASVS corresponden a **5.0.0**; una referencia de sección agrupa varios requisitos y no acredita cumplirlos todos. Se incluye por separado el [inventario completo L1/L2](evidencias/inventario-asvs-l1-l2.json), con aplicabilidad inicial y verificación pendiente. No se calcula un porcentaje de cumplimiento ASVS desde estas filas.

Estados:

- **Puntual:** comprobación ejecutada satisfactoriamente en esta revisión, limitada a su alcance.
- **Implementado / verificar:** hay código y evidencia histórica; falta repetir en el entorno de evaluación.
- **Parcial:** control incompleto o combinación de controles existentes y brechas.
- **Pendiente:** todavía no existe implementación o evidencia suficiente.
- **N/A candidato:** tecnología ausente en v1; exclusión sujeta a revisión, no cumplimiento automático.

S01–S11 se definen en el diagnóstico. Pxx identifica un caso del [plan de pruebas](PLAN-PRUEBAS-SEGURIDAD.md). Las prioridades P1/P2 se refieren al momento de cierre antes de exposición, no a CVSS.

## Requisitos y aceptación

| ID | Requisito para MSPI | ASVS 5.0.0 | Estado y evidencia actual | Brecha / prioridad | Condición de aceptación |
| --- | --- | --- | --- | --- | --- |
| SEC-001 | Revisar secretos en los tres repositorios y referencias disponibles | V13.3.1 | Puntual: seis escaneos Gitleaks sin hallazgos; evidencias JSON | No garantiza detectar todos los secretos | P01: escaneo reproducible y resultados sin valores sensibles |
| SEC-002 | Impedir incorporar secretos en futuras entregas | V13.3.1 / V15.2 | Pendiente: sin exclusión efectiva de archivos revisados y sin gate CI | H-01, P1 | P02: archivos locales fuera de Git, ejemplos sin secretos y detección automática probada |
| SEC-003 | Proteger secretos operativos frente a la cuenta de evaluación | V13.3.1 / V13.3.2 | Parcial: adaptadores externos; ACL/rotación no demostradas | H-11, P1 | P03: cuenta separada no lee secretos; rotación y revocación verificadas |
| SEC-004 | Mantener cookie y CSRF en todos los caminos de identidad y escritura | V3.3 / V3.5.1 | Implementado / verificar: S02/S03 y pruebas de sesión existentes | Comprobar navegador/proxy real, P1 | P04: cookie correcta, token anterior rechazado, petición sin CSRF sin efectos |
| SEC-005 | Invalidar sesiones por salida, vencimiento, recuperación y cuenta deshabilitada | V7.3 / V7.4.2 | Parcial: S02/S11; H-08 en deshabilitación | H-08, P2 | P05: sesión anterior rechazada en consulta de sesión y operaciones empresariales |
| SEC-006 | Aplicar política coherente de credenciales en todos los caminos | V6.2 / V6.2.12 | Parcial: S01, alta y recuperación con mínimo configurable | H-04, P1 | P06: mínimo seguro común y contraseñas comprometidas rechazadas sin divulgar la contraseña |
| SEC-007 | Resolver autenticación reforzada y su recuperación para objetivo L2 | V6.3.3 / V6.4.3 | Pendiente: login actual solo contraseña | H-04, P1 para L2 | P07: segundo factor o relajación justificada con mitigaciones y alcance explícitos; no marcar cumplido por defecto |
| SEC-008 | Evitar abuso de intentos sin bloqueo indiscriminado de usuarios legítimos | V6.1.1 / V2.4.1 | Parcial: S04, cuotas por cuenta/IP/instancia | H-03, P1 | P08: abuso acotado, cliente real de proxy verificado y acceso legítimo dentro del presupuesto |
| SEC-009 | Preservar comprobantes de invitación y recuperación de un solo uso | V6.4 / V11.5 / V14.2.1 | Implementado / verificar: huellas, aleatoriedad, caducidad y transacciones | Verificar con despliegue y frontend, P1 | P09: consumo concurrente único, destinatario correcto y token fuera de URL/log |
| SEC-010 | Autorizar cada empresa, objeto, acción y proyección de datos | V8.1 / V8.2 / V8.3 | Implementado / verificar: S05 y pruebas de autorización | Nueva regresión requerida, P1 | P10: AU/RA/AP y usuario de otra empresa no amplían acceso manipulando solicitudes |
| SEC-011 | Aplicar revocación y cambio de roles en nuevas solicitudes | V8.2 / V8.3 | Implementado / verificar: S05, roles no congelados en sesión | Verificar navegador/caché, P1 | P11: sesión abierta pierde el permiso retirado y conserva otros permisos válidos |
| SEC-012 | Separar conexiones y pertenencia de datos también en PostgreSQL | V8.3 / V15.2 | Implementado / verificar: S06 y scripts/grants | Instancia de evaluación no preparada, P1 | P12: conexión A no opera B, incluso con nombres calificados, y rollback no contamina contexto |
| SEC-013 | Rechazar entradas no contratadas y parametrizar consultas | V1.2.4 / V1.5 / V2.2 | Implementado / verificar: S07, Jackson y DTO | Revisar cada endpoint futuro, P1 | P13: inyección/campos adicionales/tipos inválidos sin efecto ni detalle interno |
| SEC-014 | Impedir saltos de estado, doble consumo y sobrescritura | V2.3 / V15.4 | Implementado / verificar: S09, revisión y bloqueo | Acciones/riesgos aún pendientes, P1 | P14: una mutación ganadora y rechazo de revisión obsoleta; cierres originales intactos |
| SEC-015 | Renderizar datos privados sin interpretación de código | V1.2.1 / V1.2.2 | Pendiente: frontend no implementado | H-07, P1 de frontend | P15: texto malicioso permanece texto en todas las vistas; URLs con protocolos permitidos |
| SEC-016 | Aplicar cabeceras y aislamiento de navegador de la versión construida | V3.4.3 / V3.4.4 / V3.4.5 / V3.4.6 | Parcial: Spring Security por defecto y CSP de descarga; sin frontend global | H-07, P1 | P16: CSP sin depender del prototipo, framing bloqueado y tipos/referrer efectivos comprobados |
| SEC-017 | Restringir solicitudes entre orígenes | V3.4.2 / V3.5.1 / V3.5.3 | Parcial: CSRF existente, despliegue mismo origen pendiente | H-06/H-07, P1 | P17: origen no autorizado no obtiene datos ni muta; CORS no sustituye permisos |
| SEC-018 | Segregar caché, formularios y respuestas tardías | V14.3.1 / V14.3.2 | Pendiente: frontend no implementado; backend usa no-store en vistas revisadas | H-07, P1 | P18: A nunca aparece en B; logout/expiración retiran datos del cliente |
| SEC-019 | Validar formato, tamaño, pertenencia y cuota de archivos | V5.1 / V5.2 / V5.3 / V5.4.2 | Implementado / verificar: S08 | H-02 para coste global; pruebas actuales no se repitieron | P19: extensión/contenido falso, exceso, ruta manipulada y descarga ajena rechazados |
| SEC-020 | Evitar distribuir contenido malicioso validado solo estructuralmente | V5.4.3 | Pendiente: sin antimalware/cuarentena observados | H-05, P1 | P20: archivo de prueba inocuo para antivirus no se publica; fallo de inspección no lo habilita |
| SEC-021 | Acotar trabajo intensivo y creación de sesiones | V2.4.1 / V15.1.3 / V15.2.2 | Parcial: semáforos de identidad, bytes/píxeles y pools limitados | H-02/H-03, P1 | P21: medir CPU/RAM/disco y tráfico legítimo bajo carga; rechazo controlado y recuperación |
| SEC-022 | Rechazar mensajes HTTP ambiguos y respetar límites de entrada | V4.1 / V4.2.1 | Parcial: parser/servidor sin perfil proxy verificado | H-06, P1 | P22: límites coherentes en proxy/API; peticiones ambiguas no alcanzan lógica de negocio |
| SEC-023 | Exponer únicamente el punto de entrada del ejercicio por HTTPS | V12.1 / V12.2 / V13.2 | Pendiente: perfiles no preparados | H-06, P1 | P23: perfiles local/LAN/Internet; base y administración inaccesibles desde LAN/Internet |
| SEC-024 | Confiar solo en cabeceras de intermediarios autorizados | V4.1.3 / V15.3.4 | Parcial: login toma dirección del contenedor, proxy aún indefinido | H-03/H-06, P1 | P24: cabecera de IP falsificada no elude controles; direcciones no colapsan involuntariamente |
| SEC-025 | Consultar avisos de dependencias de la revisión analizada | V15.2 | Puntual: árbol Maven y 193 consultas OSV, cero coincidencias | No cubre plataforma/plugins/futuras dependencias | P25: consultas completas, versiones y alcance conservados |
| SEC-026 | Repetir controles de seguridad y atender avisos en PR | V15.2 | Parcial: S10 solo construcción/pruebas; acciones por SHA | H-10, P2 | P26: detección de secretos/dependencias y análisis pertinentes en CI, con triage y evidencia |
| SEC-027 | Registrar eventos de seguridad sin secretos y sin agotar disco | V16.1 / V16.3 / V16.4 | Parcial: correlación, 5xx y eventos de negocio | H-09, P2 | P27: eventos útiles, redacción, permisos, volumen acotado y alerta verificable |
| SEC-028 | Conservar errores genéricos y rechazo seguro ante fallos | V16.5.1 / V16.5.2 | Implementado / verificar: S07 y compensaciones de evidencia | Volver a probar fallos de servicios, P2 | P28: sin SQL/rutas/tokens ni publicación parcial; estado e historial coherentes |
| SEC-029 | Restaurar datos y archivos coherentes en entorno separado | V14.2 / V16.5; DMP-08 del proyecto | Pendiente: HU-029 | H-09, P2 antes de demostración | P29: copia restaurada y comprobación de empresas, versiones y archivos; tiempos medidos |
| SEC-030 | Mantener inventario de alcance ASVS completo | ASVS 5.0.0 L1/L2 | Parcial: inventario generado desde publicación oficial | Revisar aplicabilidad uno por uno | P30: exclusiones justificadas y cada requisito aplicable con evidencia; no inventar porcentaje |

## Criterio para avanzar

La estructura del frontend puede definirse con SEC-004/010/015–018 como requisitos de diseño desde su primera HU. Antes de exponer el sistema deben cerrarse los P1 aplicables, ejecutar las pruebas de todos los escenarios y documentar cualquier excepción y su riesgo. Una excepción no se convierte en «cumplido» por estar documentada.

Ni tener la matriz ni superar P01/P25 equivale a cumplir ASVS L2. La aplicabilidad detallada, controles pendientes y verificaciones de runtime permanecen visibles en el inventario y el backlog.
