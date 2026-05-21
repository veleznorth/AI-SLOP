# Final prompt y Codex

## Que debe incluir `final_prompt.md`

- Autoridad operativa: repo fuente de verdad, `AGENTS.md` vigente y specs
  largas solo como referencia conceptual.
- Objetivo observable de la hoja.
- `done_when` concreto.
- Alcance permitido: rutas, archivos nuevos autorizados y tipo de cambio.
- Alcance prohibido: no refactors, no archivos fuera de lista, no tooling no
  autorizado, no dependencias nuevas, no invenciones.
- Evidence digest con claims `VERIFICADO` resumidos y trazables.
- Decisiones humanas explicitas.
- Reuse requirements y que hacer si no hay reuse scan suficiente.
- Checks obligatorios y como reportar bloqueos.
- Stop conditions literales.
- Formato de reporte final.

## Que no debe incluir

- Evidence packets completos si son largos.
- Copia de `AGENTS.md`.
- Background conceptual que no cambie la ejecucion.
- Varias soluciones alternativas si la hoja ya tiene mision concreta.
- Claims `INFERIDO` o `DESCONOCIDO` como instrucciones.
- Reescrituras redundantes que dupliquen o cambien autoridad, evidencia, scope o
  checks.

## Handoff al implementer

El implementer recibe final_prompt.md directamente. Si un wrapper externo cambia
scope, prioridad, evidencia o checks, debe detenerse con
`STOP-HUMAN-DECISION-REQUIRED`.

## Codex como implementer

- Codex lee antes de editar: `final_prompt.md`, `AGENTS.md` activo y rutas
  autorizadas.
- Codex no amplia scope, no hace cleanup vecino, no agrega tooling ni
  dependencias sin autorizacion.
- Codex usa patrones existentes del repo y respeta instrucciones de plataforma,
  sandbox y permisos.
- Codex reporta diff/checks: archivos creados, archivos modificados, checks
  ejecutados, resultado, diff medido, riesgos y stop code si aplica.
- Codex no declara Pi Agent, skills, runtime o enforcement como probados sin
  evidencia real.

## AGENTS.md durable

`AGENTS.md` es instruccion durable del repo. No debe copiarse dentro de cada
prompt ni tratarse como scope efimero de una hoja. Si cambia o contradice el
prompt de fase, la fase debe registrar la contradiccion y pedir decision humana.
