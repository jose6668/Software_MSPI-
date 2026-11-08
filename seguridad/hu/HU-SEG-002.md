# HU-SEG-002 — Prevención de secretos implementada

Estado: implementación local en los tres repositorios; verificación/publicación en curso.
Rama por repositorio: `feat/HU-SEG-002-prevencion-secretos`.
Base documental Develop: `4cf3e75e656c2ea1c29539617982cdd73659116b`.
PR documental y PR de código: pendientes de publicación.

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

El 8 de noviembre de 2026 pasaron los verificadores y las pruebas negativas en los tres repositorios con Python 3.13 y Gitleaks 8.30.1. Los seis escaneos actuales/históricos terminaron con salida 0 y cero hallazgos. Se comprobó que un archivo privado forzado al índice se rechaza y que un comprobante sintético se detecta aunque se retire del último commit, sin aparecer en el reporte. Los diffs preparados no contienen errores de formato. [Resultados estructurados](../evidencias/HU-SEG-002/resultados-locales.json). Los resultados CI se registran tras publicar los PR.

## Respuesta a una exposición

Revocar/rotar la credencial en el servicio, actualizar su configuración privada y retirar el valor del código/logs/artefactos sin copiarlo al reporte. Evaluar historial, copias y accesos; coordinar una eventual limpieza de ramas con colaboradores. Borrar la última versión del archivo o añadir .gitignore no revoca un secreto ni elimina los commits previos.
