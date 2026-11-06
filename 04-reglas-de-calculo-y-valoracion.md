# Reglas de cálculo y valoración

Versión: 0.2 — Decisiones APR-02 incorporadas; observaciones de Excel conservadas
Fecha: 6 de noviembre de 2026
Relacionados: [Requerimientos](01-parametros-y-requerimientos.md), [Permisos](02-matriz-de-permisos.md), [Casos de uso](03-casos-de-uso.md).

## 1. Propósito, alcance y estados

Especificar las entradas, escalas, procedimientos, límites y resultados de la valoración de activos, evaluaciones y riesgos. Las fórmulas de los archivos son fuentes del proyecto; no se consideran automáticamente una metodología aprobada ni una exigencia de una norma.

Este documento distingue:

- **OBS:** comportamiento observado en las fórmulas o tablas de los Excel.
- **APR:** comportamiento aprobado para la primera versión según APR-02 del documento 1; puede diferir del Excel.
- **PROP:** regla propuesta para el software, pendiente de aprobación funcional.
- **PEND:** decisión no resuelta; no habilita un cálculo automático en producción.

Las reglas ya establecidas en los requerimientos —separación empresarial, conservación histórica y diferencia entre faltantes y cero— siguen vigentes. Autorizar la elaboración de este documento no aprueba los pesos o cambios metodológicos propuestos.

APR-02 registra una aprobación posterior y explícita: criticidad por máximo; escala discreta y promedio por control evaluado; cinco categorías de riesgo; residual conjunto documentado sin descuentos automáticos; mínimos de evidencia/cierre y conservación histórica. Las observaciones originales se mantienen como referencia, no como fórmulas productivas. Los pesos PHVA, niveles agregados de madurez, rúbricas detalladas y políticas operativas no se consideran aprobados por asociación.

Se inspeccionaron fórmulas, valores almacenados y tablas de referencia mediante lectura de los archivos. No se guardaron cambios en los Excel ni se ejecutó un motor de recálculo de Excel. Las comprobaciones numéricas indicadas son cálculos independientes sobre las entradas leídas, no una certificación del funcionamiento del libro en Excel. Las advertencias del lector sobre imágenes o extensiones de validación no se usan para concluir que dichas funciones estén ausentes del archivo original.

## 2. Fuentes y localización reproducible

Los archivos están en la carpeta superior al repositorio; los enlaces siguientes dependen de esa ubicación local. No se incorporan copias al repositorio en esta tarea.

| Fuente | Archivo | Referencias principales |
| --- | --- | --- |
| F1 | [Diagnóstico](../articles-401779_recurso_1.xlsx) | `ESCALA DE EVALUACIÓN!C16:E22`, dominios, `CLÁUSULAS`, `PORTADA`, `NIST` |
| F2 | [Inventario](../articles-401780_recurso_1.xlsx) | `'Inventario Activos'!S14:Y15`, `BD43:BD49` |
| F3 | [Riesgos](../articles-401781_recurso_1.xlsx) | `'Matriz de Riesgo'!K13:AK14`, `Listas!A3:G7`, `Listas!L3:P7`, `Listas!C11:D15` |

La hoja `TECNOLÓGICOS ` de F1 tiene un espacio final en su nombre. Se conserva al localizar sus celdas; el software usará identificadores propios, no nombres de pestaña como claves.

Huellas SHA-256 de las versiones inspeccionadas:

```text
F1 a015a14b885012d709ea18f3486bd6b6b9902f351c0a2ae7c041f19470cd6e20
F2 26f2a1bb047011ea178290afd2c11bc49f367dc58a50e6e60c065df01d92fe85
F3 e202d947420644d87dba6f5ee8dbccf47de5081b1911b641ccebffefff9f2234
```

## 3. Reglas transversales

### CAL-GEN-01. Datos completos, inválidos y no aplicables

- **Estado:** APR; desarrolla RN-05 y los criterios de RF-08/RF-13, conforme APR-02.
- **Entradas:** valor, aplicabilidad, justificación y metodología de cada registro.
- **Regla:** un faltante es ausencia de dato; cero es un valor válido solo en las escalas que lo admiten; «no aplica» es una decisión explícita con justificación, no un texto que se convierte a cero.
- **Salida:** resultado válido, pendiente por datos, no aplicable justificado o error de validación. Son estados distintos.
- **Validación:** entradas fuera del catálogo/rango se rechazan; no se recortan al mínimo o máximo para ocultar el error. La ausencia de todos los datos no produce un promedio de cero.
- **Ejemplo:** confidencialidad vacía implica criticidad pendiente; calificación de control 0 significa inexistente, no pendiente.
- **Origen:** F1 escala; F2 fórmulas T14:Y14; F3 clasificaciones L13/N13. La propuesta evita que coerciones de Excel definan el comportamiento web.

### CAL-GEN-02. Unidades, precisión y redondeo

- **Estado:** APR para conservar decimales y mostrar dos; el modo decimal de redondeo de mitades hacia arriba sigue como precisión técnica propuesta. Diferencias observadas señaladas en cada regla.
- **Regla:** distinguir puntos de cumplimiento de 0 a 100, índices normalizados de riesgo de 0 a 1 y conteos enteros. La interfaz puede mostrar un índice como porcentaje, pero el contrato de datos debe identificar la unidad; `0,65` normalizado representa `65 %`, no `0,65 %`.
- **Precisión:** conservar resultados decimales sin redondeos intermedios; redondear solo para presentación a dos decimales mediante redondeo decimal de mitades hacia arriba. Clasificar sobre el valor sin redondear. La precisión de almacenamiento se definirá en el diccionario de datos.
- **Límite:** un índice no acredita una probabilidad estadística medida. Su interpretación y evidencia deben constar en la metodología.
- **Ejemplo:** 60,004 puntos se muestra 60,00, pero pertenece al intervalo superior a 60 si esa regla es aprobada; la interfaz debe permitir ver precisión adicional junto al límite.
- **Origen:** F1 redondea promedios de dominios y cláusulas a enteros antes de usarlos; F3 usa fracciones normalizadas.

### CAL-GEN-03. Versión, vigencia y contexto empresarial

- **Estado:** APR para conservación de versiones e historial; desarrolla RN-07 y SEG-01 a SEG-07.
- **Regla:** cada valoración conserva empresa, objeto, entradas, autor, fecha, resultado y versión de metodología. Las referencias empresariales se validan antes del cálculo y de guardar el resultado.
- **Historial:** cambios en escalas, controles o evidencia no reescriben valoraciones cerradas. Marcan necesidad de revisión y generan una valoración nueva cuando se reevalúa.
- **Salida:** resultado reproducible y estado de revisión separado de su valor histórico. Comparaciones entre versiones distintas muestran esa diferencia.
- **Ejemplo:** un control compartido por dos riesgos de A puede señalar ambos para revisión; no afecta registros de B.

## 4. Activos

### CAL-ACT-01. Escalas de confidencialidad, integridad y disponibilidad

- **Estado:** OBS para equivalencias de F2; APR para escala bajo=1, medio=3, alto=5 y faltantes pendientes.
- **Entradas:** tres categorías obligatorias, una por dimensión.

| Dimensión | Categoría de origen | Valor |
| --- | --- | ---: |
| Confidencialidad | Información pública / pública = bajo | 1 |
| Confidencialidad | Clasificada / uso interno = medio | 3 |
| Confidencialidad | Pública reservada / confidencial = alta | 5 |
| Integridad o disponibilidad | Bajo | 1 |
| Integridad o disponibilidad | Medio | 3 |
| Integridad o disponibilidad | Alto | 5 |

- **Origen:** F2 `S14:T14`, `U14:V14`, `W14:X14`; listas `BD43:BD45` y `BD47:BD49`.
- **Diferencia resuelta para v1:** las fórmulas también asignan 5 a «No Clasificada», opción ausente de esas listas. APR-02 dispone representarla como pendiente, sin puntaje automático. No se incluye «no aplica» en las tres dimensiones de la versión inicial.
- **Validación:** usar códigos de catálogo; no comparar libremente textos con tildes o espacios. La clasificación de acceso no sustituye la verificación jurídica de categorías de información.
- **Ejemplo:** uso interno, integridad alta y disponibilidad alta producen `(3,5,5)`.

### CAL-ACT-02. Determinar criticidad

- **Estado:** APR; sustituye la combinación de F2 por máximo de las dimensiones (APR-02).
- **Entradas:** C, I y D en `{1,3,5}`.
- **Procedimiento aprobado:** validar las tres dimensiones; si falta alguna, pendiente. Si están completas, calcular `max(C,I,D)`: 1 → baja, 3 → media, 5 → alta. No calcular el máximo solo de las dimensiones presentes.
- **Salida:** baja, media, alta o pendiente de clasificación.
- **Origen:** F2 `Y14:Y15`; las fórmulas originales detectan 0 como «FALTAN DATOS» y contienen una comprobación redundante de tres valores 5.
- **Ejemplos aprobados:** `(1,1,1) → baja`; `(3,1,1) → media`; `(5,1,1) → alta`; `(3,3,3) → media`; `(5,5,1) → alta`; `(3,5,5) → alta`; `(5,vacío,5) → pendiente`.
- **Diferencia histórica:** F2 exige dos dimensiones altas para alta y clasifica `(5,1,1)` como media. La versión web por máximo es una metodología propia, no una reproducción de esa fórmula. Los históricos no se reetiquetan silenciosamente.

## 5. Evaluación de controles y cláusulas

### CAL-EVA-01. Calificación individual

- **Estado:** OBS en F1 y APR para valores discretos y aplicabilidad separada.
- **Entrada:** elemento del catálogo, calificación y fundamento/evidencia según requisitos de cierre.

| Calificación en puntos | Nivel individual |
| ---: | --- |
| 0 | Inexistente |
| 20 | Inicial |
| 40 | Repetible |
| 60 | Efectivo |
| 80 | Gestionado |
| 100 | Optimizado |

- **Origen:** F1 `'ESCALA DE EVALUACIÓN'!C16:E22`. Los criterios completos permanecen en la fuente y deben trasladarse al catálogo antes de implementar; esta tabla no los reemplaza.
- **Validación aprobada:** no admitir 50 u otro intermedio como calificación individual; un agregado sí puede producirlo. «No aplica» para controles exige justificación, no tiene puntaje y se excluye del promedio. Sin evaluar queda pendiente y cero significa inexistente.
- **Cláusulas:** usar la misma escala individual en la versión inicial, con criterios específicos por requisito; no habilitar libremente «no aplica» hasta definir política de aplicabilidad. Mostrar resultados por cláusula separados de controles.
- **Evidencia:** cada respuesta necesita fundamento; el criterio determina si corresponde adjunto o referencia documental verificable. Rúbricas por elemento y suficiencia de cada evidencia siguen por definir. La nota 80 de un control no significa mitigación del riesgo de 80 %.

### CAL-EVA-02. Promedio y avance por dominio o grupo de cláusulas

- **Estado:** OBS para rangos actuales; APR para promedio de aplicables evaluados, avance separado, incompletitud y conservación de decimales.

| Grupo de F1 | Entradas observadas | Resultado observado |
| --- | --- | --- |
| Organizacionales | `ORGANIZACIONALES!I16:I52` (37 posiciones) | `I53 = ROUND(AVERAGE(I16:I52),0)` |
| Personas | `PERSONAS!I16:I23` (8 posiciones) | `I24`, mismo procedimiento |
| Físicos | `FÍSICOS!I16:I29` (14 posiciones) | `I30`, mismo procedimiento |
| Tecnológicos | `'TECNOLÓGICOS '!I16:I49` (34 posiciones) | `I50`, mismo procedimiento |
| Cláusula 4 | `CLÁUSULAS!H16:H19` | `D14`, mismo procedimiento |
| Cláusula 5 | `CLÁUSULAS!H22:H24` | `D20`, mismo procedimiento |
| Cláusula 6 | `CLÁUSULAS!H27:H29` | `D25`, mismo procedimiento |
| Cláusula 7 | `CLÁUSULAS!H32:H36` | `D30`, mismo procedimiento |
| Cláusula 8 | `CLÁUSULAS!H39:H41` | `D37`, mismo procedimiento |
| Cláusula 9 | `CLÁUSULAS!H44:H46` | `D42`, mismo procedimiento |
| Cláusula 10 | `CLÁUSULAS!H49:H50` | `D47`, mismo procedimiento |

- **Definiciones:** T = elementos del alcance; N = no aplicables justificados permitidos por política; A = T − N; E = aplicables con calificación válida; U = aplicabilidad aún sin decidir; B = aplicables identificados aún sin calificación. Cada elemento pertenece a una sola categoría: `T = N + E + B + U` y `A = E + B + U` como denominador conservador hasta resolver U. En cláusulas no se admite N libremente.
- **Puntaje provisional:** `suma de calificaciones válidas / E` si E > 0. No incluir N, B ni U como ceros. Es provisional cuando B > 0 o U > 0.
- **Avance de evaluación:** `100 × E / A` si A > 0. Exponer además E, B, U y N para que el porcentaje no oculte decisiones pendientes. Aquí U cuenta como pendiente hasta decidir su aplicabilidad.
- **Casos vacíos:** si E = 0 y A > 0, puntaje pendiente y avance 0 %; si T > 0 y todos son N, grupo no aplicable y avance no aplicable, nunca 100 por división artificial; si T = 0, sin elementos en alcance, sin puntaje.
- **Ejemplo:** notas 100 y 60, un aplicable pendiente y un N justificado → puntaje provisional 80 puntos, E=2, A=3, avance 66,67 %, N=1. No se declara evaluación completa.
- **Cierre aprobado como mínimo:** B=0, U=0, fundamento por respuesta y evidencia según criterio, admitiendo adjunto o referencia verificable cuando corresponda. Los requisitos detallados por elemento siguen pendientes antes de habilitar cierre definitivo.
- **Origen y hallazgo:** Excel AVERAGE omite vacíos y textos; si no encuentra números puede fallar. En el rango organizacional leído hay 27 números y 10 vacíos: `2560/27 = 94,8148…`, que el archivo redondea a 95. Los vacíos por sí solos no permiten saber si son no aplicables o sin evaluar.

### CAL-EVA-03. Resultado global de controles y nivel agregado

- **Estado:** APR para promedio por control, decimales y presentación provisional; OBS/PEND para etiquetas de madurez agregada.
- **Origen:** F1 `PORTADA!G19:G23` promedia los cuatro resultados de dominio; `I19:I23` asigna nivel.
- **Regla aprobada:** reunir los controles aplicables evaluados de todos los dominios y calcular `suma de sus calificaciones / cantidad de controles evaluados`. Cada control tiene igual peso; no promediar las cuatro medias de dominio con pesos iguales. Mostrar además resultados y avance por dominio. Si hay pendientes o aplicabilidad sin resolver, el global disponible se marca provisional, incluso si un dominio entero carece de notas. Si no hay controles evaluados y existen pendientes, global pendiente; todos no aplicables justificados → no aplicable; sin elementos en alcance → sin datos. La cobertura global usa elementos, no media de porcentajes.
- **Umbrales observados en la fuente, todavía pendientes para etiquetas agregadas del software:**

| Intervalo válido de puntos | Nivel |
| --- | --- |
| 0 ≤ x ≤ 1 | Inexistente |
| 1 < x ≤ 20 | Inicial |
| 20 < x ≤ 40 | Repetible |
| 40 < x ≤ 60 | Efectivo |
| 60 < x ≤ 80 | Gestionado |
| 80 < x ≤ 100 | Optimizado |

- **Decisión pendiente acotada:** confirmar umbrales de etiquetas agregadas, incluido 1 para inexistente. El peso por control ya está aprobado; hasta definir umbrales se muestra puntaje sin atribuir nivel global definitivo.
- **Comparación independiente de fuentes:** F1 promedia medias redondeadas 95,40,93,0 y obtiene 57. Promediar medias sin redondearlas daría 56,9179…; esa alternativa no fue elegida. La regla aprobada reúne 83 valores numéricos con suma 4180 y obtiene `4180/83 = 50,3614…`, mostrado 50,36 provisional, con 83/93 = 89,25 % de avance si los diez vacíos se consideran pendientes, no N justificados. Son ejemplos de los datos de la plantilla, no diagnóstico confirmado de una empresa real.

### CAL-EVA-04. Avance ponderado de cláusulas y PHVA

- **Estado:** OBS; ponderación diferida expresamente por APR-02. En v1 se muestran resultados por cláusula sin global PHVA ponderado.
- **Origen:** F1 `PORTADA!F30:F37`: divide las medias redondeadas de las cláusulas 4,5,6,7,9 y 10 por 714, y la cláusula 8 por 625. Los objetivos G30:G36 son 0,14 salvo operación, 0,16.
- **Hallazgo:** a puntuación 100 en las siete cláusulas, las fórmulas suman `6 × 100/714 + 100/625 = 1,000336…`, equivalente a 100,0336… %. La diferencia proviene de aproximar pesos mediante divisores.
- **Alternativa documentada, no elegida para v1:** pesos 0,14 para cláusulas 4,5,6,7,9,10 y 0,16 para la 8; `puntaje ponderado = suma(peso × media de cláusula)` en puntos. Con todas en 100, daría 100 exacto. No implementar esta fórmula en el panel inicial.
- **Ejemplo:** las medias redondeadas almacenadas 50,67,67,60,67,67,50 dan 61,26 puntos con pesos explícitos; las fórmulas actuales dan aproximadamente 61,280224 %. La diferencia debe registrarse al cambiar de metodología.
- **Pendiente:** justificar pesos y decidir aplicabilidad de cláusulas. No excluir requisitos obligatorios por analogía con controles. Mientras se decide, un grupo sin información bloquea el global definitivo; no redistribuir pesos automáticamente. Los componentes PHVA se agrupan de acuerdo con el mapeo aprobado y no se mezclan con el global de controles.

### CAL-EVA-05. Cobertura de procesos del alcance

- **Estado:** OBS y PROP para validación y nombre.
- **Origen:** F1 `'LEVANTAMIENTO DE INFO.'!I72:N73`; `N73 = K73/I73`, con 5 procesos en alcance y 20 totales → 25 %.
- **Regla propuesta:** entradas enteras, total > 0 y `0 ≤ procesos en alcance ≤ total`; `cobertura = 100 × procesos en alcance / total`.
- **Salida:** cobertura del alcance, no cumplimiento o efectividad del MSPI. Cero en alcance con total positivo produce 0 %; total cero deja indicador no disponible y exige revisar configuración.
- **Pendiente:** confirmar que se desea mostrar este indicador en el panel inicial; no contar procesos automáticamente hasta definir la marca «incluido en alcance» en el diccionario de datos.

## 6. Riesgos

### CAL-RIE-01. Clasificar probabilidad e impacto normalizados

- **Estado:** APR para captura categórica; OBS para intervalos numéricos del Excel.
- **Entrada aprobada de v1:** elegir una de cinco categorías de probabilidad y una de cinco de impacto, con fundamento y periodo de referencia. No se aceptan porcentajes libres como entrada ordinaria. Si se requieren equivalencias internas, usar valores de referencia 0,20/0,40/0,60/0,80/1,00 para representar categorías, sin afirmar probabilidad estadística medida. La categoría es la entrada principal y el índice equivalente es derivado.
- **Referencia histórica:** los intervalos siguientes describen fórmulas de F3 y no una autorización para introducir cualquier decimal en v1.

| Intervalo normalizado | Probabilidad | Impacto |
| --- | --- | --- |
| 0 ≤ x ≤ 0,20 | Muy baja | Leve |
| 0,20 < x ≤ 0,40 | Baja | Menor |
| 0,40 < x ≤ 0,60 | Media | Moderado |
| 0,60 < x ≤ 0,80 | Alta | Mayor |
| 0,80 < x ≤ 1 | Muy alta | Catastrófico |

- **Origen:** F3 `'Matriz de Riesgo'!K13:N14`, `AG13:AJ14`. No multiplicar P por I para obtener el nivel: este archivo usa una tabla de cruce.
- **Datos faltantes:** si falta un índice, valoración pendiente y sin nivel; un campo vacío no se convierte a 0 ni «no aplica» automáticamente.
- **Ejemplos:** 0,20 → muy baja/leve; 0,2001 → baja/menor; 0,80 → alta/mayor; 0,8001 → muy alta/catastrófico. Un cero explícito cae en el primer intervalo; no significa ausencia de riesgo con esta matriz.
- **Decisión de captura resuelta:** v1 utiliza categorías tanto en inherente como residual; los decimales continuos quedan como referencia del Excel. Queda pendiente detallar rúbricas justificables por categoría y su periodo de observación.

### CAL-RIE-02. Asignar índices a partir de frecuencia y consecuencias

- **Estado:** OBS para tablas; derivación automática diferida por APR-02. Las categorías se seleccionan con criterios y justificación.
- **Origen:** F3 `Listas!A3:C7` asocia bandas de frecuencia anual a índices 0,2 a 1. Las fórmulas de K13/K14 no derivan esos valores de J13: son entradas manuales.
- **Frecuencia observada:** hasta 2 → 0,2; 3 a 24 → 0,4; 24 a 500 → 0,6; 500 a 5000 → 0,8; más de 5000 → 1. Se superponen 24 y 500. J13 expresa horas/año, mientras la tabla habla de ejecuciones/año.
- **Propuesta para discutir:** si se adopta frecuencia entera en ejecuciones/año, usar 0–2, 3–24, 25–500, 501–5000 y >5000. Cero actividades se distingue de dato faltante y requiere contexto del periodo. No convertir horas a ejecuciones sin información adicional ni afirmar que frecuencia de actividad equivale a frecuencia de incidentes.
- **Impacto observado:** `Listas!E3:G7` describe consecuencias económicas en SMLMV y reputacionales. Rangos económicos: <10; entre 10 y 50; entre 50 y 100; entre 100 y 500; >500. Los textos no fijan inequívocamente la inclusión de 50 y 100, ni cómo combinar criterios económicos y reputacionales.
- **Pendiente:** aprobar límites, criterio de combinación, evidencia y si se usa impacto financiero. No incluir conversión a pesos ni valor salarial anual hasta requerirlo y verificar su fuente. El software no debe asignar índices desde estas tablas automáticamente mientras subsistan las ambigüedades.

### CAL-RIE-03. Nivel inherente y residual mediante matriz

- **Estado:** OBS y APR para usar estas 25 combinaciones como matriz inicial del proyecto, con códigos normalizados.
- **Entradas:** categorías válidas de CAL-RIE-01.
- **Origen:** F3 `Listas!L3:P7`; fórmulas `'Matriz de Riesgo'!O13:O14` y `AK13:AK14`.

| Probabilidad \ Impacto | Leve | Menor | Moderado | Mayor | Catastrófico |
| --- | --- | --- | --- | --- | --- |
| Muy baja | Bajo | Bajo | Moderado | Alto | Extremo |
| Baja | Bajo | Moderado | Moderado | Alto | Extremo |
| Media | Moderado | Moderado | Moderado | Alto | Extremo |
| Alta | Moderado | Moderado | Alto | Alto | Extremo |
| Muy alta | Alto | Alto | Alto | Alto | Extremo |

- **Validación:** toda combinación válida tiene exactamente un resultado. Una categoría inválida genera error y faltantes generan pendiente.
- **Hallazgo:** las fórmulas devuelven `" Moderado"` con espacio inicial para media/moderado; usar un código único y etiqueta limpia. Excel compara los textos de categorías sin distinguir mayúsculas, pero no se debe depender de ese comportamiento en la web.
- **Ejemplos:** muy alta/catastrófico → extremo; muy alta/mayor → alto. Toda la columna catastrófico produce extremo, incluso con probabilidad muy baja: comportamiento conservado en la matriz inicial aprobada del proyecto, no una exigencia normativa.

### CAL-RIE-04. Calificación nominal de un control

- **Estado:** OBS únicamente; pesos no adoptados para mitigación automática en v1 según APR-02.
- **Origen:** F3 `Listas!C11:D15` y `'Matriz de Riesgo'!Y13:AC14`.

| Atributo | Valor | Peso observado |
| --- | --- | --- |
| Tipo | Preventivo | 0,25 |
| Tipo | Detectivo | 0,15 |
| Tipo | Correctivo | 0,10 |
| Implementación | Automático | 0,25 |
| Implementación | Manual | 0,10 en Listas D15; **0,15 en AB13/AB14** |

- **Fórmula observada:** `q = peso del tipo + peso de implementación`. La calificación depende de dos atributos; AD, AE y AF (documentación, frecuencia y evidencia) no intervienen en AC13/AC14.
- **Ejemplos:** correctivo automático → q=0,35; preventivo automático → 0,50; preventivo manual → 0,35 con tabla, 0,40 con fórmula. No se elige una versión por intuición.
- **Límite:** q es una puntuación nominal del archivo, no evidencia de efectividad real. La fórmula tampoco comprueba estado de implementación antes de aplicar reducción.
- **Propuesta:** no permitir una reducción automática solo por seleccionar tipo y modo. Exigir implementación verificada y evidencia vigente, además de aprobar si estos pesos son adecuados. El tipo debe corresponder al efecto real del control sobre el riesgo; automatización no demuestra por sí sola mayor efectividad.

### CAL-RIE-05. Residual con un control según la fórmula original

- **Estado:** OBS únicamente; fórmula residual automática no adoptada para v1, también cuando existe un solo control.
- **Entradas:** P e I inherentes normalizados, tipo y q nominal. F3 AG/AI usan K/M de la misma fila.

| Tipo observado | P residual | I residual |
| --- | --- | --- |
| Preventivo o detectivo | `P × (1 − q)` | `I` |
| Correctivo | `P` | `I × (1 − q)` |

- **Salida:** volver a clasificar índices con CAL-RIE-01 y cruzar CAL-RIE-03. No restar porcentajes a la etiqueta del nivel.
- **Ejemplo reproducido de F3 fila 13:** P=1, I=1, correctivo automático, q=0,35 → Pr=1, Ir=0,65 → muy alta/mayor → alto, frente a extremo inherente. Coincide con los valores almacenados; no valida la pertinencia del control.
- **Propuesta de validación:** los índices y q deben estar entre 0 y 1; un control propuesto, en ejecución o sin verificación no habilita descuento por sí solo. Sin control evaluado, la valoración residual queda pendiente de revisión; si el evaluador confirma ausencia de mitigación, puede registrar Pr=P e Ir=I justificadamente.
- **Hallazgo:** fila 14 está «En ejecución», pero calcula el mismo descuento 0,35 y residual alto. El archivo no condiciona la reducción a ese estado.
- **Pendiente:** definir qué tipos afectan cada dimensión, qué evidencia habilita reducción, vigencia de efectividad y si se conserva el modelo nominal. No confundir el puntaje de madurez 0–100 de F1 con q.

### CAL-RIE-06. Varios controles sobre un mismo riesgo

- **Estado:** OBS para cálculo por fila independiente; APR para valoración residual conjunta documentada sin descuentos automáticos, con uno o varios controles.
- **Origen:** F3 filas 13 y 14 comparten referencia de riesgo B=1 y tienen controles P=1 y P=2. AG14 usa K14 y AC14; AI14 usa M14 y AC14, sin encadenar la mitigación de la fila anterior.
- **Regla aprobada:** representar un riesgo una sola vez y asociar sus controles. AE/GS selecciona probabilidad e impacto residuales, justifica la valoración conjunta y relaciona implementaciones verificadas consideradas. El sistema deriva índices de referencia y calcula nivel por la matriz; no calcula probabilidades ni descuentos a partir de notas, tipo o modo de control. Sin mitigación puede justificar categorías residuales iguales a las inherentes.
- **Restricciones:** ni sumar q ni aplicar productos de reducciones sucesivas sin metodología aprobada. Controles solapados, dependientes o duplicados no se consideran beneficios independientes automáticamente. Debe identificarse la dimensión afectada y conservar evidencia.
- **Ejemplo:** dos controles con q=0,35 no autorizan descontar 70 % ni afirmar que el residual es 42,25 % del inherente. Esos resultados requieren supuestos que las filas no establecen.
- **Decisión resuelta para v1:** valoración conjunta documentada; cualquier fórmula de combinación se difiere. Las rúbricas de evaluación y evidencia mínima deben completarse antes de habilitar confirmaciones definitivas.

### CAL-RIE-07. Conteo y seguimiento del riesgo

- **Estado:** PROP; desarrolla RF-18 y evita duplicación por vínculos de control.
- **Regla:** contar riesgos distintos por identificador empresarial, no filas de control. Clasificar panel y matriz por valoración vigente seleccionada y exponer por separado riesgos pendientes o con revisión requerida.
- **Tratamiento:** reducir, compartir, aceptar o evitar son decisiones; no modifican índices por selección. Aceptación exige PER-27 y no equivale a cierre. Completar una tarea no prueba efectividad ni reduce el residual automáticamente.
- **Ejemplo:** referencia 1 con dos controles cuenta como un riesgo y dos asociaciones de control. No sumar nivel inherente y residual como dos riesgos distintos.
- **Origen:** F3 B13:B14 y P13:P14; tratamientos en `Listas!B17:B20`; RN-06 y RN-09.

## 7. NIST: análisis de referencia para una fase posterior

### CAL-REF-01. Mapeo y agregación NIST

- **Estado:** OBS/PEND; fuera del módulo independiente de la primera versión, según alcance.
- **Origen:** F1 `NIST!E16:E38` y `PORTADA!C53:C58`.
- **Observado:** mezcla referencias individuales, sumas divididas por cantidades fijas y promedios por función. No hay justificación del mapeo en las fórmulas ni debe asumirse equivalencia normativa.
- **Hallazgo A:** NIST E25 suma cuatro celdas tecnológicas I17:I20 y tres organizacionales I30:I32: siete entradas, pero divide por ocho. Su descripción enumera ocho controles; falta resolver referencia o divisor. Con siete valores 100 la fórmula devuelve 87,5, no 100.
- **Hallazgo B:** PORTADA C58 promedia NIST E36:E39; E36:E37 son recuperar, E38 contiene el promedio general E16:E37 y E39 está vacía. El indicador de recuperar incorpora el promedio global. No es una referencia circular, pero mezcla categorías distintas.
- **Otros límites:** varias categorías reutilizan controles; textos «N/A» o faltantes pueden comportarse distinto en sumas aritméticas y AVERAGE. La automatización requerirá catálogo de mapeo versionado, validación de denominadores y cobertura por categoría.
- **Propuesta futura:** corregir el mapeo a partir de una decisión documentada antes de implementar; no duplicar puntuaciones o reconstruir la metodología solo por similitud de nombres.

## 8. Registro de hallazgos

| ID | Fuente exacta | Hallazgo | Tratamiento propuesto |
| --- | --- | --- | --- |
| HAL-01 | F2 T14/V14/X14 y BD43:BD49 | «No Clasificada» se convierte a 5, aunque no aparece en las listas indicadas | APR-02: pendiente sin puntaje |
| HAL-02 | F2 Y14:Y15 | Un único 5 con otros valores bajos produce media | APR-02: web usa máximo y produce alta; fuente conservada |
| HAL-03 | F1 ORGANIZACIONALES I16:I53 | 10 vacíos se omiten y el promedio de 27 números aparece como 95 | Separar nota provisional y avance |
| HAL-04 | F1 PORTADA G19:G23 y dominios | Media de medias redondeadas; dominios con cantidades diferentes tienen igual peso | APR-02: igual peso por control, sin redondeo intermedio |
| HAL-05 | F1 PORTADA I19:I23 | Umbral de inexistente llega a 1 punto | Confirmar o versionar un cambio |
| HAL-06 | F1 PORTADA F30:F37 | Divisores aproximan pesos y permiten total 100,0336… % | APR-02: diferir ponderación; mostrar cada cláusula |
| HAL-07 | F1 LEVANTAMIENTO DE INFO. I72:N73 | Fracción de procesos del alcance rotulada en contexto de cumplimiento | Nombrar cobertura de alcance |
| HAL-08 | F3 Listas D15 y Matriz de Riesgo AB13:AB14 | Manual tiene peso 0,10 en tabla y 0,15 en fórmula | APR-02: no usar esos pesos para mitigar automáticamente |
| HAL-09 | F3 AC13:AC14 y AD13:AF14 | Documentación, frecuencia y evidencia no entran en la calificación nominal | Definir requisitos de efectividad verificable |
| HAL-10 | F3 V14 y AG14/AI14 | Control en ejecución recibe reducción sin verificación de estado | No reducir por propuesta o ejecución sin evaluar |
| HAL-11 | F3 B13:B14 y AG13:AI14 | Dos controles del mismo riesgo producen residuales independientes | Riesgo único y regla explícita de valoración conjunta |
| HAL-12 | F3 Listas B3:B7 y J13 | Frecuencias se superponen en 24/500 y unidades difieren | Aprobar bandas y unidades |
| HAL-13 | F3 Listas F3:G7 | Límites económicos y combinación reputacional/económica no explicitados | No derivar impacto automáticamente |
| HAL-14 | F3 O13:O14 y AK13:AK14 | Etiqueta « Moderado» contiene espacio inicial | Código de categoría normalizado |
| HAL-15 | F1 NIST E25 | Siete entradas divididas por ocho, descripción menciona ocho | Resolver mapeo antes de habilitar módulo |
| HAL-16 | F1 PORTADA C58 y NIST E36:E39 | Recuperar incluye promedio global | Revisar rango, conservar referencia histórica |
| HAL-17 | F3 Q13:Q14 frente a F1 dominios | Identificadores A.9.x en matriz y A.5–A.8 en diagnóstico | Identificar versión y mapeo; no renombrar automáticamente |

Los hallazgos describen fuentes locales, no incumplimientos normativos. No se modificaron las fórmulas originales para resolverlos.

## 9. Casos de referencia para verificación futura

«OBS» reproduce origen; «APR» es un criterio aprobado para web; «PROP» sigue por revisar. Un ejemplo OBS puede diferir expresamente de la web. Son criterios de pruebas futuras, no pruebas de una aplicación existente.

| ID | Regla | Entrada o condición | Resultado esperado | Tipo |
| --- | --- | --- | --- | --- |
| VAL-01 | CAL-ACT-02 | (1,1,1) | Baja | APR |
| VAL-02 | CAL-ACT-02 | (5,1,1) | Alta en web; media solo en fuente original | APR/OBS |
| VAL-03 | CAL-ACT-02 | (3,5,5) | Alta | APR |
| VAL-04 | CAL-ACT-01/02 | Una dimensión vacía o no clasificada | Pendiente, sin criticidad calculada | APR |
| VAL-05 | CAL-ACT-01 | Valor 4 fuera del catálogo | Error de validación | APR |
| VAL-06 | CAL-EVA-01 | Calificación individual 50 | Error; agregado 50 sí es válido | APR |
| VAL-07 | CAL-EVA-02 | 100,60,un pendiente,un N justificado | 80 provisional; 2/3, avance 66,67 % | APR |
| VAL-08 | CAL-EVA-02 | Tres aplicables sin calificación | Nota pendiente; avance 0 % | APR |
| VAL-09 | CAL-EVA-02 | Todos los controles N justificados | Grupo no aplicable; no habilita N libre en cláusulas | APR |
| VAL-10 | CAL-EVA-02 | Un 0 y un 100, ambos evaluados | 50 puntos; avance 100 % | APR |
| VAL-11 | CAL-EVA-03 | x=1, x=1,01, x=20, x=20,01 | Inexistente, inicial, inicial, repetible | OBS/PROP |
| VAL-12 | CAL-EVA-03 | x=40, 40,01, 60, 60,01, 80, 80,01, 100 | Repetible, efectivo, efectivo, gestionado, gestionado, optimizado, optimizado | OBS/PROP |
| VAL-13 | CAL-EVA-04 | Siete cláusulas con media 100, alternativa futura 14/16 | Aritmética daría 100; fórmula no habilitada en v1 | PROP diferida |
| VAL-14 | CAL-EVA-05 | 5 procesos en alcance / 20 totales | 25 % de cobertura | OBS/PROP |
| VAL-15 | CAL-EVA-05 | Total cero o alcance mayor al total | No dividir por cero; error/configuración pendiente | PROP |
| VAL-16 | CAL-RIE-01 | P=0; P=0,20; P=0,2001 en fórmulas fuente | Muy baja; muy baja; baja; no son entradas libres de la web | OBS |
| VAL-17 | CAL-RIE-01 | I=0,40;0,4001;0,60;0,6001;0,80;0,8001;1 en fuente | Menor; moderado; moderado; mayor; mayor; catastrófico; catastrófico | OBS |
| VAL-18 | CAL-RIE-01 | P=-0,01, I=1,01 o un faltante | Fuera de rango: error; faltante: pendiente | PROP |
| VAL-19 | CAL-RIE-03 | Las 25 combinaciones de categorías | Coincidir con cada celda de la matriz de sección 6 | APR |
| VAL-20 | CAL-RIE-04 | Correctivo automático | q=0,35 nominal | OBS |
| VAL-21 | CAL-RIE-04 | Preventivo manual | Mostrar discrepancia 0,35 vs 0,40; sin valor aprobado | PEND |
| VAL-22 | CAL-RIE-05 | P=I=1; correctivo automático | Pr=1; Ir=0,65; alto según fórmula original | OBS |
| VAL-23 | CAL-RIE-05 | P=I=1; preventivo automático | Pr=0,50; Ir=1; extremo según fórmula original | OBS |
| VAL-24 | CAL-RIE-05/06 | Control propuesto o sin evidencia requerida | No conceder descuento automático; residual exige justificación y datos válidos | APR |
| VAL-25 | CAL-RIE-06/07 | Un riesgo con dos controles q=0,35 en fuente | Un riesgo; residual conjunto justificado, q no entra en cálculo web | APR |
| VAL-26 | CAL-GEN-03 | Nueva metodología sobre evaluación cerrada | Conservar resultado anterior; crear nueva valoración | APR |
| VAL-27 | CAL-REF-01 | Siete entradas de E25 valen 100 | Fuente devuelve 87,5; incidencia de mapeo abierta | OBS |
| VAL-28 | CAL-REF-01 | Recuperar 20 y 40; promedio global 80 | C58 observado da 46,666…; solo recuperar daría 30 | OBS/PEND |
| VAL-29 | CAL-GEN-03 | Datos o implementación pertenecen a otra empresa | Rechazo antes de calcular o guardar | Requisito vigente |
| VAL-30 | CAL-EVA-02 | E=2, B=0, U=1, N=1; notas 100 y 60 | 80 provisional, avance 66,67 %, aplicabilidad pendiente visible | APR |
| VAL-31 | CAL-EVA-03 | Un dominio pendiente y otros con controles calificados | Global sobre evaluados, provisional; dominio pendiente visible | APR |
| VAL-32 | CAL-EVA-03 | Un dominio con un control 100 y otro con tres controles 0 | Global 25 puntos, no media de dominios 50; avance 100 % | APR |
| VAL-33 | CAL-RIE-01/06 | Seleccionar probabilidad alta e impacto mayor residuales con fundamento | Equivalencias derivadas 0,80/0,80 y nivel alto, sin descuento automático | APR |
| VAL-34 | CAL-RIE-06 | Un control calificado en 80 puntos | No deriva una reducción de 80 % | APR |
| VAL-35 | CAL-ACT-02 | (3,1,1) y (5,vacío,1) | Media y pendiente, respectivamente | APR |
| VAL-36 | CAL-EVA-04 | Solicitar global PHVA con pesos 14/16 en v1 | No disponible; presentar resultados separados por cláusula | APR |

## 10. Trazabilidad funcional

| Reglas | Requerimientos y casos relacionados |
| --- | --- |
| CAL-GEN-01 a CAL-GEN-03 | RN-05, RN-07, RN-10; SEG-01 a SEG-07; transversal a CU-11 y CU-14 a CU-27 |
| CAL-ACT-01/02 | RF-08; CU-11; PER-16 |
| CAL-EVA-01/02/03 | RF-09 a RF-11, RF-18; CU-13 a CU-16, CU-26 |
| CAL-EVA-04 | RF-11, RF-18; CU-15, CU-16, CU-26; ponderación diferida, resultados por cláusula en v1 |
| CAL-EVA-05 | RF-06, RF-18; CU-09, CU-26; indicador propuesto |
| CAL-RIE-01/02/03 | RF-12, RF-13; CU-17, CU-18 |
| CAL-RIE-04/05/06 | RF-13, RF-14, RF-17; CU-18, CU-19, CU-25 |
| CAL-RIE-07 | RF-18, RF-20; RN-06, RN-09; CU-20, CU-21, CU-26, CU-27 |
| CAL-REF-01 | Referencia de fuente; módulo NIST independiente diferido |

## 11. Registro de decisiones metodológicas tras APR-02

| ID | Decisión | Estado actual y comportamiento | Pendiente o efecto |
| --- | --- | --- | --- |
| DEC-01 | Escalas y criticidad de activos | Aprobado: 1/3/5 y máximo de las tres dimensiones completas | No clasificado queda pendiente; fuente original conservada |
| DEC-02 | Notas y aplicabilidad | Aprobado: 0/20/40/60/80/100, N separado; en cláusulas N restringido | Detallar rúbricas y política específica de cláusulas |
| DEC-03 | Agregación y cobertura | Aprobado: media de evaluados, cobertura separada y provisionalidad | No transformar pendientes en cero |
| DEC-04 | Pesos y niveles agregados | Aprobado: igual peso por control y sin redondeo intermedio, dos decimales visibles | Umbrales de madurez global y precisión física aún pendientes |
| DEC-05 | Ponderación PHVA | Diferida: resultados por cláusula sin global PHVA ponderado en v1 | No implementar pesos 14/16 |
| DEC-06 | Evidencia y vigencia | Aprobados mínimos de fundamento, evidencia pertinente, autor/fecha y cierres | Precisar adjunto vs referencia por criterio y periodicidad de revisión |
| DEC-07 | Asignación de probabilidad e impacto | Aprobada captura en cinco categorías justificadas | Detallar rúbricas; derivación por frecuencia/dinero diferida |
| DEC-08 | Matriz de niveles | Aprobadas las 25 combinaciones como matriz inicial del proyecto | No equivale a criterio normativo de aceptación |
| DEC-09 | Pesos nominales y efecto del control | No adoptados para mitigación automática en v1 | La discrepancia manual 10/15 queda como hallazgo de fuente, sin bloquear valoración manual |
| DEC-10 | Uno o varios controles | Aprobada valoración residual conjunta documentada | Fórmula automática de descuentos/combinación diferida |
| DEC-11 | Catálogos y referencia NIST | Identificar versiones A.9.x/A.5–A.8 y corregir mapeos antes de fase NIST | Mantener origen sin equivalencias inventadas |
| DEC-12 | Cobertura de procesos | Confirmar indicador y pertenencia de procesos al alcance | No mostrar cobertura como cumplimiento |

APR-02 identifica fecha, autor de aprobación (usuario del proyecto) y alcance. Los elementos aún PROP/PEND no cambian por asociación. El procedimiento futuro de publicación/aprobación de metodología dentro del sistema sigue por definir; la aprobación de requisitos del proyecto no implementa ese procedimiento.
