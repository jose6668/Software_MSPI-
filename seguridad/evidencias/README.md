# Evidencias de HU-SEG-001

Fecha: 8 de noviembre de 2026. Estas evidencias son del diagnóstico inicial, con las revisiones de repositorio fijadas en `repository-hygiene.json`.

| Archivo | Contenido |
| --- | --- |
| resumen.json | Alcance, versiones, conteos, resultados y verificaciones no ejecutadas |
| tool-provenance.json | Publicación oficial de Gitleaks y SHA-256 del archivo Windows x64 verificado |
| scan-summary.json | Resultados resumidos de los seis escaneos iniciales |
| gitleaks-*.json / .log | Reportes y logs redactados del estado inicial y el historial disponible |
| repository-hygiene.json | Revisiones, commits alcanzables, exclusiones probadas y ubicaciones de candidatos literales; sin valores |
| dependency-tree.json / packages.json | Árbol Maven resuelto y pares paquete-versión únicos |
| osv-attempt.json / osv-matches.json | Ocho consultas completas por lotes a OSV y lista de coincidencias vacía |
| inventario-asvs-l1-l2.json | 253 IDs de ASVS 5.0.0: 70 L1 y 183 L2; verificación individual pendiente y 55 exclusiones candidatas sin aprobación implícita |
| SHA256SUMS.json | Huellas del contenido versionado en Git, con saltos de línea LF, sin incluir el propio manifiesto |

La revisión complementaria de literales identificó ubicaciones de una expresión de validación y dos datos de pruebas; no se confundieron con credenciales operativas. Los seis reportes de Gitleaks están vacíos porque no hubo hallazgos, no porque se eliminaran resultados.

El inventario ASVS conserva IDs/niveles; el texto normativo se consulta en la fuente oficial fijada por versión y huella, indicada en ese archivo. No se adjunta una copia modificada del estándar ni se declara evaluado un requisito por generarlo en JSON.

## Reproducción básica

Utilizar Gitleaks 8.30.1 obtenido de su publicación oficial y verificar su archivo contra la suma publicada. Ejecutar desde cada carpeta de repositorio, manteniendo los reportes fuera de la carpeta que se está examinando:

```powershell
gitleaks git . --log-opts='--all --full-history' --redact=100 --ignore-gitleaks-allow --report-format=json --report-path='<salida-externa>/historial.json'
gitleaks dir . --redact=100 --ignore-gitleaks-allow --report-format=json --report-path='<salida-externa>/archivos.json'
```

Revisar códigos de salida: 0 indica ausencia de hallazgos detectados; 1 normalmente corresponde a hallazgos y otros errores deben investigarse. No usar excepciones, reportes base o variables de configuración que omitan resultados sin documentarlas. El diagnóstico comprobó que no estaban definidas las variables GITLEAKS_CONFIG ni GITLEAKS_CONFIG_TOML.

Desde `mspi/`, con el wrapper:

```powershell
.\mvnw.cmd --batch-mode --no-transfer-progress dependency:tree '-DoutputType=json' '-DoutputFile=<ruta-absoluta-externa>/dependency-tree.json'
```

Construir los pares únicos grupo:artefacto/versión, incluidos ámbitos de prueba, y consultar `https://api.osv.dev/v1/querybatch` en lotes. Cada elemento usa `package.ecosystem=Maven`, `package.name=grupo:artefacto` y `version`. Conservar respuestas, resolver paginación si aparece y descargar/investigar avisos encontrados. No transmitir código, contraseñas o archivos de configuración privados.

El primer intento OSV único tuvo un reinicio de conexión. La consulta posterior por ocho lotes sí terminó y se verificó para los 193 pares, sin tokens de paginación pendientes. Un fallo de conexión nunca se contó como cero vulnerabilidades.

Los reportes son una fotografía temporal. Volver a consultar después de cambiar dependencias o antes de la evaluación. Un resultado vacío no cubre vulnerabilidades desconocidas, componentes fuera del inventario ni debilidades de diseño.

Para verificar las huellas en Windows, usar el contenido de los archivos versionados en Git o una copia con saltos de línea LF. Una copia de trabajo convertida automáticamente a CRLF puede tener otra huella sin que cambie el contenido lógico del reporte.
