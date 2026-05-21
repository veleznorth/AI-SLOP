# Autoridad, evidencia y stops

## Jerarquia de autoridad

1. Politicas de plataforma, seguridad, sandbox, approvals y permisos del host.
2. Instrucciones activas del entorno, incluyendo `AGENTS.md` activo.
3. Prompt actual de la fase, por ejemplo `final_prompt.md` para implementacion.
4. Repo actual como source of truth para hechos de implementacion.
5. Decisiones humanas explicitas incluidas en el prompt actual.
6. Evidence packets limpios, pequenos, citados y no superseded.
7. Outputs previos de otros agentes, solo como contexto trazable.
8. Specs largas, research, AI Rules v0.4 y docs externas como referencia
   conceptual, no como autorizacion automatica de scope.

## Reglas ante contradiccion

- Si plataforma/sandbox/approvals contradicen una instruccion local, manda la
  plataforma y se reporta bloqueo.
- Si `AGENTS.md` activo contradice `final_prompt.md` en comandos, rutas,
  permisos o proceso, usar `STOP-HUMAN-DECISION-REQUIRED`.
- Si el repo contradice una spec larga o un output previo sobre un hecho de
  implementacion, priorizar el repo y reportar la contradiccion.
- Si una decision humana explicita contradice una inferencia, priorizar la
  decision humana y degradar la inferencia a contexto.

## Labels de evidencia

- `VERIFICADO`: soportado directamente por repo, salida de tool o fuente
  revisada en la corrida actual.
- `INFERIDO`: conclusion razonable derivada de claims verificados, pero no
  declarada literalmente por la evidencia.
- `DESCONOCIDO`: no revisado, no soportado, conflictivo, stale, heredado sin
  fuente suficiente o dependiente de una decision humana faltante.

Reglas:

- Un `DESCONOCIDO` no se convierte en instruccion.
- Un `INFERIDO` no se convierte en autoridad limpia salvo decision humana
  explicita.
- Un claim heredado sin soporte se degrada a `DESCONOCIDO`.
- Si dos fuentes se contradicen, registrar la contradiccion y no resolver por
  intuicion.

## Evidence oversized

Evidence oversized no es autoridad limpia. Puede motivar lectura adicional,
replan o `READ_LEAF_OVERSIZE`, pero no debe alimentar `final_prompt.md` como
claim operativo. Compaction, memory, summaries de otros agentes y artifacts
superseded son contexto, no evidence clean por si solos.

Cada `evidence_packet` debe declarar sources reviewed, reuse scan,
contradictions, oversized y human summary corto.

## Stop conditions principales

- `OUT_OF_SCOPE_FOR_THIS_PHASE`: la peticion pertenece a otro rol o fase.
- `READ_LEAF_OVERSIZE`: la hoja exige demasiada lectura para sostener claims.
- `CONTEXT_LIMIT_EXCEEDED`: el contexto/harness ya no permite precision segura.
- `STOP-DIFF-ESTIMATE-UNCERTAIN`: el forecasting no basta para autorizar.
- `R1_EXCEEDED`: un archivo productivo nuevo excederia el maximo permitido.
- `R2_EXCEEDED`: el diff por hoja excederia el maximo permitido.
- `HARNESS_SCOPE_EXCEEDED`: faltan tools, permisos, red o filesystem.
- `STOP-SCOPE-ESCALATION-REQUIRED`: terminar exige tocar areas no autorizadas.
- `STOP-HUMAN-DECISION-REQUIRED`: falta decision humana o hay contradiccion
  normativa.
- `STOP-REUSE-SCAN-MISSING`: no hay evidencia suficiente de reuse seguro.

Regla literal: si aplica cualquier stop code, no continuar bajo suposicion.
Reportar `STOP-CODE`, razon corta, siguiente accion requerida y permiso o
evidencia faltante.
