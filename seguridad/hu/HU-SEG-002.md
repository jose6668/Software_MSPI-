# HU-SEG-002 — Prevención de secretos implementada

Estado: implementación verificada y publicada en los tres repositorios; pendiente de revisión e integración.
Rama por repositorio: `feat/HU-SEG-002-prevencion-secretos`.
Base documental Develop: `4cf3e75e656c2ea1c29539617982cdd73659116b`.
PR documental hacia Develop: [#2](https://github.com/jose6668/Software_MSPI-/pull/2). PR relacionados: [backend #20](https://github.com/jose6668/Software_MSPI-back/pull/20) y [frontend #1](https://github.com/jose6668/Software_MSPI-front/pull/1).

Como equipo, queremos prevenir publicación accidental de credenciales y comprobar esa defensa automáticamente mientras continuamos las historias funcionales.

## Cambios reales

- Los tres repositorios incorporan .gitignore raíz, script de control de archivos/plantillas y workflow Secret hygiene en PR hacia Develop y pushes a esa rama.
- Backend y frontend incorporan plantillas .env.example sin secretos. No se crea una plantilla artificial de ejecución para el repositorio documental.
- El detector Gitleaks 8.30.1 se descarga desde la publicación oficial y se verifica por SHA-256, sin usar credenciales de ejecución ni secretos de GitHub.
- Las pruebas negativas crean un repositorio temporal, fuerzan un .env al índice e introducen/retiran un comprobante sintético para comprobar archivos, historial y redacción. No se incorpora el comprobante al historial de los proyectos.

No se cambian API, datos, sesiones, migraciones, permisos de otros colaboradores o protecciones administrativas de rama. Es implementación de controles de repositorio/CI; las correcciones runtime del backend siguen en las siguientes HU.

## Uso por colaboradores

Trabajar normalmente en una rama desde Develop. Mantener contraseñas/configuración privada fuera de Git; una plantilla pública mantiene vacíos los valores secretos. El backend no carga .env automáticamente y toda variable VITE_* es pública. Si se introduce una variable pública nueva, ampliar explícitamente la política; no desactivar el check.

Ejecutar desde cada raíz, con Python 3 y Gitleaks verificado:

```powershell
python .github/scripts/check_secret_hygiene.py --self-test --gitleaks 'C:\ruta\gitleaks.exe'
```

La comprobación falla ante archivos privados versionados o hallazgos del detector, pero GitHub solo impide fusionar por ese motivo si se configura el check como obligatorio. No se ha cambiado esa protección en esta entrega. Un detector por patrones no encuentra necesariamente todos los secretos.

## Aceptación

| Criterio | Comprobación |
| --- | --- |
| CA-01 | Exclusiones raíz/anidadas efectivas y plantillas públicas versionables |
| CA-02 | .env forzado al índice rechazado; plantillas con valores privados/URL con credencial rechazadas sin imprimir valores |
| CA-03 | Comprobante sintético detectado en archivos y después de borrarlo del último commit; reportes redactados |
| CA-04 | Escaneo actual e histórico de las tres ramas sin hallazgos no revisados |
| CA-05 | CI pasa en cada PR hacia Develop y resultados/enlaces quedan registrados |

Trazabilidad: H-01, SEC-001/002 y P01/P02; ASVS 5.0.0 V13.3.1. La gestión de secretos operativos, ACL, rotación efectiva y almacenamiento protegido H-11 no se da por completa.

## Verificación local

El 8 de noviembre de 2026 pasaron los verificadores y las pruebas negativas en los tres repositorios con Python 3.13 y Gitleaks 8.30.1. Los seis escaneos actuales/históricos terminaron con salida 0 y cero hallazgos. Se comprobó que un archivo privado forzado al índice se rechaza y que un comprobante sintético se detecta aunque se retire del último commit, sin aparecer en el reporte. Los diffs preparados no contienen errores de formato. [Resultados estructurados](../evidencias/HU-SEG-002/resultados-locales.json).

## Evidencia CI de implementación

| Repositorio | Implementación | Verificación satisfactoria |
| --- | --- | --- |
| Backend | bbfbb998d851346b7cb2fa9c19858fa6c0ed70c4 | [Secret hygiene](https://github.com/jose6668/Software_MSPI-back/actions/runs/42505477555) y [Backend CI](https://github.com/jose6668/Software_MSPI-back/actions/runs/42505477443): 110 pruebas, sin fallos/errores/omisiones, Java 17/PostgreSQL |
| Documentación | 07b143167bdee577363abd1764b04c48420f4e89 | [Secret hygiene](https://github.com/jose6668/Software_MSPI-/actions/runs/42505480037) |
| Frontend | 5b57e51567231c14b9e0e8d2d6008b041cc62b12 | [Secret hygiene](https://github.com/jose6668/Software_MSPI-front/actions/runs/42505478556) |

Las ejecuciones registradas corresponden a los commits de implementación. Las actualizaciones de evidencia se vuelven a verificar mediante CI antes de dejar los PR listos para revisión. No se declara integración hasta su fusión autorizada.

## Respuesta a una exposición

Revocar/rotar la credencial en el servicio, actualizar su configuración privada y retirar el valor del código/logs/artefactos sin copiarlo al reporte. Evaluar historial, copias y accesos; coordinar una eventual limpieza de ramas con colaboradores. Borrar la última versión del archivo o añadir .gitignore no revoca un secreto ni elimina los commits previos.
