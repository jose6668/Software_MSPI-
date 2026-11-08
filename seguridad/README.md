# Seguridad de la aplicación MSPI

La seguridad es el criterio principal de la implementación y de la evaluación académica. Se asume que el evaluador conoce el código y todos los repositorios y puede conectarse desde el mismo equipo, la red local o Internet.

## Entrega inicial HU-SEG-001

Implementación posterior: [HU-SEG-002, controles de repositorio y CI contra secretos](hu/HU-SEG-002.md).

- [Diagnóstico y hallazgos priorizados](DIAGNOSTICO-SEGURIDAD.md).
- [Matriz de requisitos y evidencia](MATRIZ-SEGURIDAD.md).
- [Escenarios y plan de pruebas](PLAN-PRUEBAS-SEGURIDAD.md).
- [Ficha de la historia](hu/HU-SEG-001.md).
- [Resumen de verificaciones ejecutadas](evidencias/resumen.json).
- [Inventario de requisitos ASVS L1/L2](evidencias/inventario-asvs-l1-l2.json).

Esta entrega documenta el estado observado el 8 de noviembre de 2026. Los escaneos de secretos y dependencias sí se ejecutaron; las pruebas ofensivas, de navegador, carga y recuperación se especifican para ejecución posterior. No hay declaración de invulnerabilidad ni de cumplimiento integral de ASVS.

El objetivo de referencia es OWASP ASVS 5.0.0 L2, con evaluación de aplicabilidad y justificación de exclusiones. El inventario completo L1/L2 evita confundir los requisitos priorizados de la matriz con una evaluación completa del estándar.

La especificación funcional de los documentos 01–09 conserva sus decisiones. Este directorio añade requisitos de seguridad de la propia aplicación, distintos de los controles empresariales que MSPI permite evaluar.
