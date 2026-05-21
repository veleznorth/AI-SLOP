# Anti-patterns y required updates diferidos

## Anti-patterns del smoke test

- Wrapper duplicando `final_prompt.md`.
- `reading_leaf` prohibiendo al reader producir su propio `evidence_packet`.
- Artifacts planos sin indice o autoridad clara por fase.
- Evidence packets largos que entierran claims utiles.
- Reader ejecutando multiples hojas en una sola corrida Standard/Strict.
- Evidence oversized como autoridad limpia.
- Pi declarado probado sin prueba.
- Guia placeholder tratada como autoridad fuerte.
- Diff para untracked medido sin `git add -N`.
- Respuestas humanas largas cuando bastaba status, bloqueo y siguiente accion.

## Reglas de contencion

- Un wrapper no debe reinterpretar `final_prompt.md`.
- `reading_leaf` debe prohibir implementacion y final prompt, no el output
  principal del Evidence Reader.
- Artifacts planos pueden seguir existiendo, pero necesitan indice y autoridad
  visible por fase.
- Evidence packets largos deben reducirse a summaries y claims trazables antes
  de llegar al prompt final.
- Evidence oversized solo sirve para replan, lectura adicional o stop; no es
  autoridad limpia.
- Pi, skills y enforcement se mantienen `NOT_PROVEN` hasta validacion real.

## Required updates deferred

Estos cambios quedan diferidos. No se aplican desde esta guia:

- JSON schemas / Structured Outputs.
- Parser de stop codes.
- Ledger de calibracion de diff forecast vs diff real.
- Pi validation.
- Skills.
- Plantilla oficial endurecida de `final_prompt.md`.
- Politica operativa completa para modos Lite / Standard / Strict.
- Ejemplos positivos/negativos de claims en templates.

## Nota de fase

Este modulo documenta deuda y anti-patterns. No autoriza tocar prompts,
templates, `AGENTS.patch.md`, `ai-rules-starter-es/` ni reglas normativas del
starter kit.
