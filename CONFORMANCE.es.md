> Traducción comunitaria (borrador) — Política P2-002 de NTARI, Difusión Multilingüe Global. Fuente: CONFORMANCE.md (original en inglés, instantánea del 2026-10-05). Borrador comunitario asistido por máquina, pendiente de revisión por mantenedores regionales conforme a P2-002 §3.1. Las especificaciones técnicas centrales permanecen en inglés conforme a §2.2.
>
> ¿Encontraste un error en esta traducción? Tu corrección es una contribución
> bienvenida y valorada: haz un fork del repositorio y abre un pull request en
> https://github.com/NTARI-RAND/shelter.

> **El original en inglés es normativo.** Esta traducción se ofrece para lectura, no para interpretación: donde difiera de `CONFORMANCE.md`, rige el inglés. Las palabras clave normativas (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY), los nombres de campos, las etiquetas y los identificadores se mantienen en inglés porque constituyen el contrato de conformidad.

# shelter — Autodescripción de conformidad

*Autodeclarada hasta que un conjunto de pruebas cite estos ID (registro: `JFA/jfa-conformance-suite.py`). En etapa de propuesta; "cumplido por diseño" significa que la especificación establece la regla y exige la prueba.*

**Forma:** la economía de apoyo a la reintegración de la Parte VI de la arquitectura — evaluación bidireccional, prioridad sin conexión (offline-first) en dispositivos de gama básica, la dignidad y la salida por delante de la supervisión; el caso señalado como el más agudo del problema abierto 8.

| ID | Invariante | Estado |
|---|---|---|
| COV-1 | Sin promedios; distribución completa | **Cumplido por diseño** — INV-4, con una guarda de grep en CI exigida |
| COV-2 | La calificación más baja es el incumplimiento | **Cumplido por diseño** — −1 marcado como daño, comentario obligatorio, se notifica al gestor de casos |
| COV-3 | Simétrico; toda afirmación admite respuesta | **Cumplido por diseño** — calificaciones bidireccionales; la vía de disputa anota, nunca borra |
| COV-4 | Decide si, nunca cuánto | **Cumplido por diseño** — ninguna puntuación condiciona el valor de nadie; el recuento es una señal positiva, no un rango |
| REC-1 | Solo adición | **Cumplido por diseño** — eventos de calificación inmutables; la línea entre lo borrable y lo duradero sigue abierta (OQ-1) |
| REC-2 | Perdonar, nunca ocultar | **Cumplido por diseño** — las marcas/disputas contextualizan |
| REC-3 | Sin PII (datos de identificación personal) en nada compartido | **Cumplido por diseño** — INV-5/6/8/10: seudónimos, sin PII en los registros (logs), verificar sin retener, sin categorías de estatus |
| REC-9 | Tipos de relación | **Parcial** — las calificaciones tipadas por dirección (ciudadano↔beneficiario, inspección) se corresponden de forma natural; el tipado formal se define en la construcción |
| GOV-9 | Documento vivo de preguntas abiertas | **Cumplido** — `OPEN-QUESTIONS.md` se distribuye con esta semilla |
| GOV-10 | Bien común copyleft | **Abierto** — LBTAS: reutilizar frente a reimplementar (OQ-3) |
| Problema 8 | Piso de costo de los testigos | **Nombrado** — registros autodeclarados hasta que atestiguar sea asequible aquí (OQ-2) |

**Línea de honestidad:** no existe ningún registro atestiguado; toda lectura de reputación está atestada por el operador. La defensa "no recopilamos eso" de INV-10 es arquitectónica y debe sobrevivir a todo cambio futuro del esquema.
