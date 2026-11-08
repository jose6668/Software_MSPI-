# HU-SEG-001: diagnóstico de seguridad

Fecha: 8 de noviembre de 2026, America/Bogota.
Estado: análisis inicial terminado; controles pendientes de implementación/verificación identificados.

## 1. Decisión y resultado

La aplicación debe conservar su protección aunque el evaluador conozca el código, los endpoints, los algoritmos y los nombres de las variables de configuración. Contraseñas, comprobantes y claves privadas reales se mantienen fuera de los repositorios y del frontend construido. Ninguna defensa depende de ocultar cómo funciona MSPI.

El backend tiene una base útil: sesión con CSRF, autorización empresarial, privilegios PostgreSQL limitados, versiones y archivos privados. Sin embargo, **todavía no hay evidencia suficiente para presentar el sistema como preparado para una evaluación hostil por red o Internet**. Faltan controles y comprobaciones de disponibilidad, entorno, autenticación reforzada, archivos y observabilidad, además del frontend seguro aún inexistente.

No se confirmó una filtración de secretos ni una vulnerabilidad conocida en las dependencias consultadas. Esto no demuestra ausencia de fallos: los resultados quedan limitados al alcance, herramientas, versiones y fecha registrados abajo.

## 2. Alcance y versiones

| Repositorio | Revisión analizada | Estado inicial |
| --- | --- | --- |
| Software_MSPI-back | `814753bbf7786859c1385a9ad3585d0f66648d39`, Develop | 243 archivos versionados; 61 commits alcanzables por las referencias locales disponibles |
| Software_MSPI- | `964249cbd00d13c3fde06e4451f7948d583531b8`, main | 11 archivos versionados; 15 commits alcanzables |
| Software_MSPI-front | `86a30f10f671d0c04b982d8c7d1958e19892dfc6`, main; Develop remoto igual | Un README, un commit; sin aplicación ni manifiesto de dependencias |

Se actualizaron las referencias remotas antes de revisar. La revisión documental partió de main. Por instrucción posterior del usuario, se crea `Develop` desde el mismo commit `964249cbd00d13c3fde06e4451f7948d583531b8` y se entrega `docs/HU-SEG-001-diagnostico-seguridad` mediante PR hacia `Develop`. Backend y frontend conservan sus ramas y archivos. Las nuevas entregas de los tres repositorios siguen el flujo de ramas y PR hacia `Develop`.

El diagnóstico no modificó la aplicación, la configuración de ejecución, el firewall, las credenciales o los servicios. La publicación posterior crea las ramas documentales autorizadas y un PR hacia `Develop`. No se realizaron ataques contra servicios externos.

## 3. Verificaciones realmente ejecutadas

### 3.1. Secretos

Se descargó Gitleaks **8.30.1** de su publicación oficial y se verificó SHA-256 del archivo descargado contra el archivo de sumas de esa publicación. No se instaló globalmente. Se usó redacción al 100 %, sin excepciones de comentarios `gitleaks:allow`.

Se ejecutaron dos recorridos por repositorio: `gitleaks git --log-opts="--all --full-history"` y `gitleaks dir`. Los seis terminaron con salida 0 y cero hallazgos. El historial backend contiene 61 commits alcanzables; Gitleaks informó 42 commits con diffs analizados, excluyendo las fusiones sin diff relevante. No se equiparan ambas cantidades ni se afirma haber inspeccionado commits remotos inaccesibles o eliminados.

También se revisaron nombres de archivos sensibles en el historial y asignaciones literales sospechosas en los archivos versionados. No aparecieron archivos .env, almacenes de claves o claves privadas con los patrones revisados. Las tres coincidencias literales actuales correspondieron a una expresión de validación de token, una contraseña ficticia de prueba y un valor de prueba unido a un UUID aleatorio. No se publican sus valores en este informe.

**Resultado:** no se detectaron secretos con estos procedimientos. No equivale a una garantía: reglas por patrones pueden omitir contraseñas comunes, secretos ofuscados o valores que parezcan datos ordinarios. No se inspeccionaron ni publicaron los secretos del entorno del equipo, gestores de credenciales, otros proyectos, mensajes privados o datos de GitHub Actions.

### 3.2. Dependencias

Maven Wrapper resolvió el árbol mediante `dependency:tree`, con plugin 3.10.0 y salida JSON. El objetivo terminó con BUILD SUCCESS; hubo avisos 401 de repositorios secundarios de metadatos, sin impedir obtener el árbol. Este objetivo no compila ni ejecuta la aplicación y no demuestra compatibilidad de runtime.

Se consultaron **193 pares únicos paquete-versión Maven**, incluidos ámbitos de ejecución y prueba, contra la API OSV en ocho lotes. Todos los lotes respondieron y ninguno indicó paginación adicional. **Cero coincidencias** en esa consulta. Solo se transmitieron coordenadas públicas de dependencias; no se envió código ni secretos.

El alcance no incluye vulnerabilidades del sistema operativo, JDK, Docker, imágenes, plugins de construcción, proxy futuro, servicios externos o paquetes que se incorporen después. No hay dependencias frontend que consultar todavía. Una base de avisos puede tener retrasos o cobertura incompleta.

### 3.3. Entorno y pruebas previas

El Java predeterminado es 21.0.5. El proyecto exige JDK 17 en su construcción. El motor Docker Linux no respondió. Por ello no se ejecutó nuevamente `clean verify`, no se arrancó una aplicación de evaluación y no se hicieron pruebas de penetración, carga o restauración nuevas.

Las fichas existentes registran 110 pruebas satisfactorias para HU-018. Esa evidencia histórica apoya la revisión, pero no acredita pruebas nuevas ni la configuración del equipo para la demostración.

## 4. Activos, actores y límites de confianza

Activos principales: contraseñas y comprobantes, sesión, membresías y roles, datos de cada empresa, evidencias, historial, configuración de base y SMTP, copias y disponibilidad del equipo.

Actores de prueba:

- Persona sin sesión con acceso HTTP.
- Usuario legítimo con rol limitado: AU o RA.
- Miembro de A que intenta acceder o modificar B, y persona con roles diferentes en ambas.
- AE/GS que intenta saltar estados, sobrescribir versiones o aprobar una entrega propia cuando exista ese flujo.
- Lector de todos los repositorios e historial.
- Usuario en el equipo que utiliza navegador o terminal sin privilegios de servicio.
- Administrador del sistema operativo, como límite de confianza distinto.

Los controles de aplicación no impiden que un administrador del sistema detenga procesos o lea archivos y memoria a los que tiene acceso. Si el evaluador usa la misma cuenta de Windows que ejecuta la aplicación, puede tener acceso a los secretos de esa cuenta. Preparar una cuenta de evaluación separada, permisos de archivos y, cuando corresponda, aislamiento de entorno; no prometer que .gitignore resuelve ese escenario.

| Escenario | Límite que se debe comprobar |
| --- | --- |
| Mismo equipo | Probar la API directamente además de la pantalla. El firewall de red no sustituye autorización ni protege secretos de la misma cuenta del sistema |
| Red local | Acceso al punto de entrada HTTPS desde otro equipo; la base, Docker y administración no quedan expuestos a la LAN |
| Internet | Acceso por un único punto de entrada HTTPS/túnel configurado; validar origen, proxy y límites. Un túnel no elimina ataques de aplicación ni saturación del enlace |

No se habilitan permanentemente LAN e Internet por preparar ambos perfiles. La misma versión construida y las mismas reglas de autorización se verifican en los tres escenarios.

## 5. Controles existentes que deben conservarse

| Control observado | Evidencia principal | Qué falta demostrar en esta entrega |
| --- | --- | --- |
| PBKDF2-HMAC-SHA256, sal aleatoria y 600 000 iteraciones | S01 | Política completa de credenciales y coste aceptable bajo carga |
| Sesión de servidor, rotación, CSRF, expiración, cookie HttpOnly/Secure/Lax | S02/S03 | Navegador y HTTPS reales en cada perfil; deshabilitación de cuenta según H-08 |
| Límites de autenticación y verificaciones costosas simultáneas | S04 | Resistencia al bloqueo inducido y cobertura fuera del login |
| Membresías vigentes, combinación de roles local y permiso por operación | S05 | Regresión ejecutada de nuevo con dos empresas y navegador |
| Conexiones y privilegios separados por empresa; contexto fijo y limpieza | S06 | Configuración de la instancia de demostración y privilegios efectivos |
| Consultas parametrizadas y JSON estricto | S07 | Pruebas de abuso por endpoint; los controles existentes no prueban toda entrada futura |
| Archivos privados, tamaño, formato, cuota, huella y descarga autorizada | S08 | Archivos maliciosos y coste de procesamiento concurrente |
| Control de revisión, transiciones e historial | S09 | Flujos futuros de acciones/riesgos y pruebas integradas |
| CI con permisos de lectura y acciones fijadas por SHA | S10 | Puertas continuas de secretos, dependencias y otras verificaciones de seguridad |

## 6. Hallazgos priorizados

Prioridades: **P1** antes de exponer a evaluación hostil o de afirmar cumplimiento del objetivo; **P2** dentro del endurecimiento previo a la demostración. Son prioridades de trabajo, no puntuaciones CVSS. «Confirmado estático» describe código/configuración observado; no implica explotación dinámica demostrada.

### H-01 — Falta prevención de incorporación accidental de secretos — P1

Confirmado: `git check-ignore --no-index` no excluye `.env`, `.env.local`, `mspi/.env` ni `credentials.pem` en los tres repositorios. El gitignore backend está en mspi y contiene reglas de IDE/build; frontend y documentación carecen de reglas equivalentes. CI no incorpora un detector de secretos.

Riesgo: una futura configuración puede terminar en Git aunque hoy el escaneo no detecte secretos. Ignorar archivos no protege valores ya versionados ni oculta secretos al propietario de la cuenta del equipo.

Corrección propuesta: reglas en la raíz de cada repo, plantilla sin valores reales, verificación continua/precommit y procedimiento de revocación/rotación. Cierre: comprobar archivos de ejemplo detectables en una carpeta temporal y pruebas de exclusión, sin introducir secretos reales o de prueba en el historial de trabajo.

### H-02 — Protección contra agotamiento de recursos incompleta — P1

Confirmado estático: los semáforos y límites existentes se concentran en identidad; no se encontró un límite general de solicitudes o creación de sesiones ni una configuración de evaluación con límites de proceso, almacenamiento temporal y tiempo SQL. El GET CSRF público permite iniciar sesiones anónimas. El control de archivos limita bytes/píxeles, pero los lectores de PDF/imagen y descargas consumen memoria y CPU por operación y no tienen un presupuesto global documentado. Existen valores predeterminados de contenedor: su ausencia en YAML no significa que no haya ningún límite; falta adecuarlos y probarlos.

Corrección propuesta: límites por recurso/actor y capacidad total, creación de sesiones, concurrencia de archivos, tamaños y tiempos HTTP/SQL, disco temporal y recursos del proceso. Medir con carga progresiva y comprobar recuperación y servicio legítimo. No se ha demostrado una caída ni fijado capacidad numérica sin medir el equipo.

### H-03 — El limitador permite agotar cuotas de acceso legítimo — P1

Confirmado estático en S04: cada intento admitido consume cuota aunque sea correcto; el contador por cuenta y la cota global comparten una ventana. Intentos dirigidos a una cuenta pueden bloquear temporalmente su acceso legítimo; varias fuentes/cuentas pueden agotar la cuota de instancia. El tope por IP limita el abuso desde una sola dirección pero no elimina estos casos; usuarios detrás de un mismo proxy también pueden compartir cuota.

Corrección propuesta: política contra abuso y bloqueo inducido, identidad de cliente confiable detrás de proxy, métricas y respuesta controlada. Conservar cota global de seguridad, sin simplemente retirarla. Cierre: prueba donde el abuso de otra cuenta/fuente no impida indiscriminadamente accesos legítimos dentro del presupuesto acordado. La disponibilidad ilimitada no es un criterio posible.

### H-04 — Política de contraseñas y autenticación reforzada incompleta — P1 para objetivo L2

Confirmado: alta y recuperación validan longitud configurable, pero aceptan una configuración mínima tan baja como 1; el generador técnico permite una contraseña no vacía. No se encontró comprobación de contraseñas comprometidas ni un segundo factor. El correo de invitación/recuperación no constituye un segundo factor durante cada login.

Corrección propuesta: mínimo coherente en todos los caminos, comprobación de contraseñas comprometidas sin transmitirlas en claro y política de segundo factor, especialmente AP/AE, con recuperación segura. ASVS 5.0.0 V6.3.3 requiere autenticación reforzada o justificación completa con controles mitigantes para una relajación; no marcarlo N/A por no haberlo implementado. La decisión y su verificación forman parte de la siguiente HU, sin cambiar aquí el mecanismo de sesión aprobado.

### H-05 — Un archivo parseable no equivale a archivo libre de contenido malicioso — P1 antes de ofrecer archivos no confiables al evaluador

Confirmado estático S08: se comprueban formato y estructura de PDF/imagen, pero no se encontró antivirus/cuarentena ni eliminación de contenido activo PDF. Un PDF válido puede contener acciones o adjuntos; comprobar cabecera, parser y huella no acredita inocuidad. Descargar como attachment y CSP sandbox limita el contexto del navegador, pero no protege por sí solo el visor externo del destinatario.

Corrección propuesta: política de inspección/antimalware, estado no descargable hasta el veredicto, comportamiento ante fallo del motor y tratamiento de PDF activo. Verificar límites de CPU/memoria. No declarar que el parser es vulnerable; es una brecha de política/control de archivos no confiables, relacionada con ASVS V5.4.3.

### H-06 — No hay perfil reproducible de exposición para la evaluación — P1 antes de abrir acceso

Confirmado: no hay configuración versionada de los tres perfiles con punto HTTPS, proxy, puertos internos, confianza en cabeceras, ACL de secretos/archivos y límites del proceso. Esto no demuestra que el equipo esté actualmente expuesto; no se hizo un escaneo remoto del host.

Corrección propuesta: artefacto frontend construido y API bajo mismo origen, proxy limitado, credencial de ejecución no administrativa, base/administración sin exposición LAN/Internet, certificados y secretos externos, perfiles cerrados por defecto y pruebas desde cada ubicación. No confiar en X-Forwarded-* recibido libremente ni en un nombre de esquema secreto.

### H-07 — Falta base de seguridad del navegador para el frontend — P1 antes de integrar pantallas con datos privados

Confirmado: no hay frontend implementado ni política CSP global de producto. La CSP del archivo de evidencia solo aplica a esa respuesta. El boceto documental usa capacidades de demostración y estilos/scripts inline; su CSP no se copia a la aplicación.

Definir CSP compatible con el build de React/Material UI, `frame-ancestors`, `base-uri`, `object-src`, política de referencias, renderizado sin HTML arbitrario, URLs permitidas y caché separada por usuario/empresa. Verificar las cabeceras efectivas; no afirmar que faltan todas las cabeceras, porque Spring Security conserva valores predeterminados. Prohibir secretos VITE_* y otros secretos dentro del bundle.

### H-08 — La consulta de sesión no invalida una cuenta deshabilitada — P2

Confirmado estático: `CurrentActiveSession.isCurrent` comprueba expiración y versión de credencial, pero no `estado_cuenta`; el filtro usa ese método y `GET identidad/sesion` devuelve el principal. `requireActiveUser` sí exige cuenta HABILITADA para operaciones empresariales. Por tanto, la inconsistencia no demuestra acceso empresarial después de deshabilitar, pero permite seguir informando una sesión como vigente y no satisface una terminación uniforme de sesiones al deshabilitar cuenta.

Corrección propuesta: una comprobación coherente en el filtro y pruebas de deshabilitación/eliminación con la sesión original, incluidas consulta de sesión y rutas de negocio. Relación ASVS V7.4.2.

### H-09 — Eventos de seguridad y recuperación operativa incompletos — P2

Confirmado en rutas revisadas: hay correlación y eventos de negocio; `ApiErrors` registra explícitamente fallos 5xx. No se encontró un registro central completo de accesos exitosos/fallidos, CSRF inválido, denegaciones y exceso de límites, con rotación, cotas y protección. El historial de negocio no reemplaza esos eventos. La restauración integral HU-029 sigue pendiente.

Corrección propuesta: eventos mínimos sin contraseña/token/cookie, volumen acotado, permisos de lectura, alertas para el ejercicio y copia/restauración comprobada de base y archivos. Verificar que el registro bajo ataque no llene el disco ni revele datos de otra empresa.

### H-10 — Seguridad de dependencias y construcción sin seguimiento continuo — P2

Confirmado S10: CI construye y prueba, pero no contiene escaneo automático de secretos/dependencias ni análisis estático de seguridad. El resultado OSV puntual es satisfactorio dentro de su alcance y no reemplaza seguimiento. Faltan SBOM/registro de componentes operativos del entorno y una política de actualización.

Corrección propuesta: verificaciones repetibles en PR, alertas revisadas y evidencia de la versión final, incluyendo runtime/proxy/imágenes y frontend cuando exista. No actualizar versiones indiscriminadamente sin pruebas de compatibilidad.

### H-11 — Operación de secretos pendiente de demostrar — P1 antes de exposición

Confirmado: los adaptadores aceptan secretos externos y no se detectaron secretos versionados. No se ha definido/demostrado en el equipo un almacenamiento protegido, permisos por identidad, rotación, copias y revocación. Un .env fuera de Git es un mecanismo de entrega de configuración, no prueba por sí solo el ciclo de gestión requerido para L2.

Corrección propuesta: definir solución local protegida y accesible solo por la identidad del servicio, ubicar credenciales fuera de las carpetas compartidas de repositorio, documentar rotación y recuperación y comprobar los permisos desde una cuenta de evaluación separada. No ocultar la existencia de variables o algoritmos al profesor.

## 7. Plan de trabajo posterior

| Entrega propuesta | Alcance | Repositorio y base |
| --- | --- | --- |
| HU-SEG-002 | Prevención de secretos: exclusiones, plantillas seguras y controles automáticos | PR por repositorio afectado hacia Develop |
| HU-SEG-003 | Sesión y credenciales: H-04/H-08, política y pruebas; separar segundo factor si requiere otra HU | Backend Develop; frontend se enlaza al contrato resultante |
| HU-SEG-004 | Defensa de disponibilidad: H-02/H-03 y mediciones | Backend/entorno; separar cambios independientes en HU acotadas |
| HU-SEG-005 | Archivos no confiables: H-05 | Backend Develop; actualizar contrato y estados si corresponde |
| HU-SEG-006 | Perfiles local/LAN/Internet y secretos operativos: H-06/H-11 | Documentación/configuración de despliegue sin valores reales |
| HU-SEG-007 | Eventos y restauración: H-09 | Backend/operación; coordinar con HU-029 |
| HU-FE-001 | Estructura/frontend con H-07 como criterio desde la base | Frontend Develop |

Cada entrega debe delimitarse antes de iniciar para mantener una HU y un PR revisables. Esta lista es un backlog propuesto; no declara implementadas las correcciones.

**Se puede definir la estructura frontend después de esta matriz**, incorporando desde el diseño cliente HTTP/CSRF, roles, aislamiento de caché y renderizado seguro. No se debe abrir una demostración al evaluador hasta resolver los P1 aplicables y ejecutar el plan en los tres perfiles. El trabajo visual no debe bloquear el cierre de las defensas backend/entorno ni sustituirlo.

## 8. Fuentes de evidencia del código

Los enlaces fijan el commit backend analizado, para conservar reproducibilidad.

| ID | Fuente |
| --- | --- |
| S01 | [Hash de contraseñas](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/identidad/internal/LocalPasswordHash.java) |
| S02 | [Controlador de sesión](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/gestion/internal/web/ApiSessionController.java) y [configuración](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/resources/application.yaml) |
| S03 | [Seguridad HTTP](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/gestion/internal/web/ApiSecurityConfiguration.java) |
| S04 | [Limitador](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/identidad/internal/AttemptLimiter.java) y [proveedor de autenticación](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/identidad/internal/LocalAccountProvider.java) |
| S05 | [Autorización empresarial](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/empresas/internal/access/CurrentCompanyAccess.java) |
| S06 | [Unidad empresarial](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/empresas/internal/tenancy/ScopedTenantUnitOfWork.java), pools y migraciones SQL bajo el mismo commit |
| S07 | [Errores](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/gestion/internal/web/ApiErrors.java), application.yaml y consultas JdbcClient con parámetros |
| S08 | [Contenido de archivos](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/evidencias/internal/persistence/tenant/EvidenceContent.java), PrivateEvidenceFiles y ApiEvidenceController |
| S09 | [Cierre y revisión de evaluación](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/evaluaciones/internal/persistence/tenant/JpaEvaluationDrafts.java) |
| S10 | [Workflow CI](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/.github/workflows/backend-ci.yml) |
| S11 | [Estado de sesión](https://github.com/jose6668/Software_MSPI-back/blob/814753bbf7786859c1385a9ad3585d0f66648d39/mspi/src/main/java/software/mspi/identidad/internal/CurrentActiveSession.java) y SessionExpiryFilter |

## 9. Referencias externas

- [OWASP ASVS 5.0.0](https://asvs.owasp.org/): referencia de requisitos; se fijan identificadores con versión en la matriz.
- [OWASP WSTG](https://wstg.owasp.org/): organización de pruebas; las pruebas definitivas deben fijar sus enlaces de versión.
- [OWASP, denegación de servicio](https://cheatsheetseries.owasp.org/cheatsheets/Denial_of_Service_Cheat_Sheet.html): fundamento de controles de disponibilidad.
- [Gitleaks](https://github.com/gitleaks/gitleaks) y [publicación utilizada](https://github.com/gitleaks/gitleaks/releases/tag/v8.30.1).
- [API OSV](https://google.github.io/osv.dev/api/): consulta por paquete y versión.

Las referencias sirven para definir controles y pruebas. La ausencia de hallazgos en escáneres no es una certificación ni reemplaza la evaluación manual.
