# Plan de verificación de seguridad

Fecha: 8 de noviembre de 2026. Referencia: [matriz](MATRIZ-SEGURIDAD.md).

## 1. Estado y entorno

**Ejecutados aquí:** P01, P02 como diagnóstico de exclusiones (resultado no satisfactorio para prevención) y P25. **Los demás son casos de prueba preparados, sin ejecución nueva ni resultado aprobado.** La revisión estática de código y la existencia de pruebas históricas se registran en el diagnóstico.

Para pruebas dinámicas preparar JDK 17, Docker Linux para la regresión, base dedicada, almacenamiento privado, dos empresas ficticias A/B, cuentas AP sin membresía, AE, GS, AU, RA y una cuenta con roles distintos en A/B. Preparar SMTP de prueba y copias antes de los ensayos. Registrar commit, versión construida, configuración sin secretos, cliente, fecha, resultados y métricas.

Usar únicamente el equipo, aplicación y cuentas del ejercicio. En LAN/Internet fijar el punto de entrada propio y excluir servicios ajenos. Esto delimita el objetivo de la prueba, sin cambiar el requisito de que el profesor pueda conocer los repositorios.

Los límites de carga se calibran con el hardware real. No se ejecutaron ataques de saturación en esta entrega. Antes de ejecutarlos acordar volumen/duración, criterio de interrupción y conservación de evidencia; no inferir una capacidad numérica desde los valores de fixtures.

## 2. Casos

| Caso | Preparación y acción | Resultado exigido | Evidencia / estado actual |
| --- | --- | --- | --- |
| P01 | Gitleaks 8.30.1, reglas estándar, redacción 100 %, git --all/full-history y dir en los tres repositorios | Sin hallazgos no revisados; errores de herramienta no cuentan como éxito | Ejecutado: seis salidas 0 y cero hallazgos; evidencias JSON |
| P02 | Comprobar exclusión de .env, .env.local, mspi/.env y credentials.pem; después probar detector en carpeta temporal aislada | Reglas impiden incorporación accidental y detector demuestra funcionamiento; ejemplos seguros quedan versionables | Diagnóstico ejecutado: ninguna ruta probada queda excluida. Falta corregir y probar el detector con fixture aislado |
| P03 | Cuenta de evaluación separada intenta leer configuración privada; rotar credenciales técnicas por el procedimiento elegido | Lectura denegada y credencial anterior revocada; sin secretos en repositorio/bundle/log | Pendiente |
| P04 | Login/logout con CSRF vigente, ausente y anterior a rotación; inspeccionar cookie en navegador HTTPS | Solo flujos válidos mutan; cookie protegida y nueva sesión tras autenticar | Pendiente; existen pruebas backend históricas |
| P05 | Conservar sesión, salir/vencer/restablecer credencial/deshabilitar cuenta y consultar sesión y rutas protegidas | Sesión anterior deja de ser válida en todos los caminos correspondientes | Pendiente; H-08 identifica diferencia estática al deshabilitar |
| P06 | Alta invitada, recuperación y alta técnica con credenciales fuera de política o comprometidas | Política uniforme, sin truncar ni transmitir contraseña a un tercero | Pendiente |
| P07 | Login y recuperación con la política de factor adicional elegida, incluidos pérdida y reuso | No se omite el factor; alternativa a V6.3.3 justificada si se adopta excepción | Pendiente de definición e implementación |
| P08 | Ensayar cuota de cuenta/IP/global con abuso controlado y usuarios legítimos simultáneos | Abuso acotado; sin bloqueo general evitable y con métricas; no eliminar límites globales | Pendiente |
| P09 | Invitación/recuperación usada, vencida, destinatario distinto y dos consumos simultáneos | Máximo un consumo, sin cambio de roles ajenos ni token en URL/log | Pendiente; existen escenarios backend históricos |
| P10 | Solicitar objeto de B desde A cambiando UUID/ruta/cuerpo; probar rol AU, RA y AP sin membresía | Sin contenido, modificaciones o confirmación sensible de objeto ajeno | Pendiente |
| P11 | Con sesión abierta, retirar rol, revocar membresía o suspender empresa | Siguiente solicitud pierde el acceso; otra membresía autorizada conserva su alcance | Pendiente |
| P12 | Conexión técnica A consulta/escribe B; alternar empresas con errores y conexiones reutilizadas | PostgreSQL rechaza acceso cruzado y el contexto siguiente queda limpio | Pendiente; ejecutar suite de aislamiento existente |
| P13 | Probar entradas de inyección, tipos incorrectos, claves duplicadas y campos no permitidos en cada operación existente | Rechazo/valor literal según contrato, sin efectos adicionales ni errores internos expuestos | Pendiente |
| P14 | Dos mutaciones con la misma revisión, cierre directo y edición de evaluación cerrada | Una ganadora cuando corresponda, conflicto explícito y original inmutable | Pendiente |
| P15 | Guardar texto de prueba de XSS en campos renderizados y URLs con protocolos no permitidos | Texto sin ejecución y enlaces rechazados; comprobar lista, ficha, error y reporte futuro | Pendiente de frontend |
| P16 | Inspeccionar cabeceras de HTML/API/archivos y probar incrustación en otro origen | CSP efectiva y compatible, framing bloqueado, nosniff y referrer adecuados | Pendiente de frontend/entorno |
| P17 | Enviar consultas y mutaciones desde un origen de laboratorio no permitido, con y sin cookies | Sin lectura sensible o mutación; CORS/CSRF/permisos actúan según cada solicitud | Pendiente |
| P18 | Retrasar respuesta de A, cambiar a B, revocar permiso y cerrar sesión mientras hay consultas/formularios | Sin datos/borradores de contexto anterior ni caché privada tras salida; no enviar escritura al destino equivocado | Pendiente de frontend |
| P19 | Subir archivo falso, truncado, sobredimensionado o nombre con separadores; agotar cuota y descargar evidencia ajena | Sin publicación/carga parcial ni rutas privadas; rechazo autorizado y cuota consistente | Pendiente |
| P20 | Probar marcador antivirus de laboratorio con política explícita y fallo del servicio de inspección | Sin distribución de archivo no aprobado; resultado no depende solo de parsear PDF/imagen | Pendiente de antimalware; no introducir malware real |
| P21 | Carga progresiva: sesiones CSRF, autenticación, lectura, archivos y consultas costosas; mantener tráfico legítimo | Presupuesto CPU/RAM/disco/tiempos respetado, rechazo controlado, recuperación y datos coherentes | Pendiente; definir límites medibles antes de ejecutar |
| P22 | Probar límites HTTP y mensajes ambiguos en la cadena proxy/API de laboratorio | Rechazo coherente sin desincronización o procesamiento inesperado | Pendiente de proxy |
| P23 | Desde localhost, otro equipo LAN y cliente externo, comprobar HTTPS y superficie accesible | Solo exposición prevista; base/Docker/administración no accesibles por LAN/Internet; versión construida | Pendiente; registrar los tres resultados por separado |
| P24 | Enviar cabeceras de IP/esquema falsificadas directamente y a través del proxy autorizado | Solo intermediario confiable fija esos datos; el límite de seguridad no se elude | Pendiente |
| P25 | Resolver árbol Maven y consultar OSV por todos los pares únicos paquete-versión, con manejo de errores/paginación | Resultado por todos los paquetes, avisos investigados; ningún lote fallido presentado como limpio | Ejecutado: 193 pares, ocho lotes completos, cero coincidencias |
| P26 | Preparar controles CI y verificar con cambios inocuos y fixtures temporales | Fallos reales detectados y reportes redactados; no introducir un secreto de prueba en el historial compartido | Pendiente |
| P27 | Generar acceso fallido, denegación, CSRF inválido y exceso de límite; revisar logs y rotación | Evento trazable sin contraseña/token/cookie, acceso restringido y disco acotado | Pendiente |
| P28 | Simular caída de base, SMTP, almacenamiento o antimalware según operación | Sin éxito falso, permiso abierto o archivo parcialmente publicado; error genérico y recuperación | Pendiente |
| P29 | Restaurar base, catálogos, empresas y archivos en entorno separado | Relaciones y huellas consistentes, aislamiento conservado y tiempos registrados | Pendiente; no prometer recuperación de 8 h sin medir |
| P30 | Revisar aplicabilidad y evidencia de cada ID L1/L2 del inventario | Ningún requisito aplicable sin evaluar; exclusiones fundamentadas, desviaciones visibles | Inventario preparado; evaluación detallada pendiente |

## 3. Registro de resultados

Por cada ejecución registrar caso y perfil, commit, requisitos de matriz, condiciones, herramienta/versión, resultado esperado/real, métricas relevantes, referencia al reporte redactado, hallazgo y revisión posterior. Los valores de credenciales y comprobantes nunca se incluyen en capturas o informes públicos.

Una prueba impedida por falta de entorno se marca pendiente/bloqueada, no superada. Las capturas de pantalla no sustituyen respuestas HTTP o comprobaciones de persistencia en autorización y concurrencia. Un escaneo sin autenticación no cubre el aislamiento entre empresas.

## 4. Salida antes de evaluación

Cerrar los hallazgos P1 aplicables, volver a ejecutar regresión con JDK 17/PostgreSQL, comprobar la versión construida desde los tres perfiles, guardar un respaldo restaurable y entregar resultados con límites explícitos. Los P2 se corrigen o se registran con decisión de riesgo y efecto; esa decisión no permite afirmar cumplimiento de requisitos incumplidos.
