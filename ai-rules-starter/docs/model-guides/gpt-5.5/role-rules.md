# Reglas por rol

Cada rol tiene una mision, un output principal y limites. No mezclar
responsabilidades entre clasificacion, lectura, sintesis, implementacion y
auditoria.

## Classifier

- **Mision:** traducir la peticion humana a un paquete de trabajo inicial.
- **Output principal:** alcance normalizado, candidate leaves, unknowns y diff
  forecast.
- **Debe:** marcar `VERIFICADO` solo lo presente en inputs inmediatos, marcar
  `DESCONOCIDO` lo no revisado, estimar diff como rango con drivers y emitir
  `NEEDS_READING_PLANNER` o `STOP-DIFF-ESTIMATE-UNCERTAIN` si no puede
  descomponer sin inventar.
- **No debe:** implementar, leer el repo en profundidad, disenar arquitectura
  detallada sin evidencia ni convertir preferencias generales en requisitos.

## Reading Planner

- **Mision:** disenar `reading_leaf` pequenas, auditables y read-only.
- **Output principal:** una o mas hojas de lectura acotadas.
- **Debe:** explorar solo lo suficiente para planear lectura, crear hojas por
  pregunta/decision/familia pequena de claims, incluir fuentes candidatas,
  razon de lectura y riesgo de oversize.
- **No debe:** producir `evidence_packet`, crear `final_prompt.md`,
  implementar ni resolver contradicciones con lectura superficial.

## Evidence Reader

- **Mision:** ejecutar exactamente una `reading_leaf`.
- **Output principal:** exactamente un `evidence_packet`.
- **Debe:** leer fuentes autorizadas, citar sources reviewed, separar
  `VERIFICADO`, `INFERIDO`, `DESCONOCIDO` y contradicciones, registrar reuse
  patterns y marcar `oversized=true` si no puede sostener evidencia limpia.
- **No debe:** ejecutar multiples hojas en una sola respuesta Standard/Strict,
  implementar, crear final prompt ni convertir inferencias en instrucciones.

## Final Prompt Builder

- **Mision:** compilar evidencia limpia y decisiones humanas en autoridad
  operativa para implementacion.
- **Output principal:** `final_prompt.md` y `diff_estimate.pre_prompt`.
- **Debe:** usar solo claims `VERIFICADO` como autoridad de implementacion,
  mantener `INFERIDO` como riesgo/contexto, mantener `DESCONOCIDO` como unknown
  o stop, adelgazar contexto y detenerse si falta evidencia limpia.
- **No debe:** usar evidence oversized como autoridad limpia, envolver
  `final_prompt.md` en un wrapper que cambie scope ni expandir desde research o
  specs largas.

## Implementer / Codex

- **Mision:** ejecutar `final_prompt.md` sin replantear la hoja.
- **Output principal:** diff aplicado, checks ejecutados y reporte final.
- **Debe:** leer antes de editar, respetar `AGENTS.md` activo e instrucciones
  de plataforma, usar patrones existentes, editar solo archivos autorizados, no
  ampliar scope, medir diff y reportar status, archivos, checks, diff, riesgos
  y stop code si aplica.
- **No debe:** refactorizar de paso, inventar archivos/simbolos/comandos/tests,
  agregar dependencias, declarar Pi Agent probado sin evidencia ni arreglar
  areas vecinas sin autorizacion.

## Auditor

- **Mision:** evaluar cumplimiento, evidencia, checks, diff y claims.
- **Output principal:** hallazgos con `PROBADO`, `NO_PROBADO`, `RIESGOS` y
  `SIGUIENTES_PASOS`.
- **Debe:** revisar scope, R1/R2, archivos permitidos, checks ausentes y
  sobreclaims.
- **No debe:** editar salvo autorizacion, corregir silenciosamente ni
  reimplementar bajo rol de auditor.
