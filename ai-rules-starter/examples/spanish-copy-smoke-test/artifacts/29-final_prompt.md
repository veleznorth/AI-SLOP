# Final Prompt: Spanish Reading Copy Regeneration

Actua como instancia implementadora Codex simulada para AI Rules B8g.

## Autoridad

El prompt actual es la autoridad operativa. El repo actual es la fuente de verdad. AI Rules v0.4 puede usarse solo como referencia conceptual.

Usa como evidencia limpia: artifacts 03, 04, 06, 09 y 20-27. Usa `ai-rules-starter/docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` como guia local de redaccion. No uses artifacts 08 ni 10 como autoridad limpia; solo fueron senal de replan/oversize.

Pi Agent no esta configurado para este smoke test. Usa Codex como rol simulado. No declares que Pi enforcement fue probado.

## Objetivo

Crear `ai-rules-starter-es/` desde cero como copia espanola de lectura humana derivada de `ai-rules-starter/`, sin modificar `ai-rules-starter/` ni crear tooling.

## Alcance Permitido

Lectura permitida:

- `ai-rules-starter/`
- los artifacts limpios indicados arriba

Escritura permitida:

- solo `ai-rules-starter-es/`

No uses internet. No anadas dependencias. No crees scripts, generadores, tests nuevos, artifacts nuevos, `reading_leaf.yaml`, `evidence_packet.yaml`, `final_prompt.md` ni `audit_report.md`.

## Preflight Obligatorio

1. Ejecuta `git status --short` y conserva trabajo ajeno.
2. Ejecuta `test -e ai-rules-starter-es; printf 'exists=%s\n' "$?"`.
3. Si `ai-rules-starter-es/` ya existe, detente sin sobrescribir y reporta `STOP-TARGET-EXISTS`.
4. Confirma que `ai-rules-starter/` existe.
5. Revisa los archivos fuente autorizados antes de traducirlos. Si falta un archivo requerido, detente y reporta `STOP-SOURCE-MISSING`.
6. Si estimas que el diff final superara 600 lineas, detente y reporta `STOP-DIFF-ESTIMATE-UNCERTAIN`.

## Notice Obligatorio

Decision humana `HUMAN_DECISION_SPANISH_NOTICE_001`: coloca exactamente esta linea al inicio de cada archivo espanol:

Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

El notice debe ser la primera linea de cada archivo creado bajo `ai-rules-starter-es/`.

## Archivos A Crear

Crea la estructura destino equivalente para estos documentos humanos verificados o candidatos limpios:

- `ai-rules-starter-es/README.md` desde `ai-rules-starter/README.md`
- `ai-rules-starter-es/QUICKSTART.md` desde `ai-rules-starter/QUICKSTART.md`
- `ai-rules-starter-es/GUIDE_BASIC.md` desde `ai-rules-starter/GUIDE_BASIC.md`
- `ai-rules-starter-es/FUNDAMENTALS.md` desde `ai-rules-starter/FUNDAMENTALS.md`
- `ai-rules-starter-es/docs/advanced-reference.md` desde `ai-rules-starter/docs/advanced-reference.md`
- `ai-rules-starter-es/docs/codex-setup.md` desde `ai-rules-starter/docs/codex-setup.md`
- `ai-rules-starter-es/docs/convex-integration.md` desde `ai-rules-starter/docs/convex-integration.md`
- `ai-rules-starter-es/docs/mcp-safety.md` desde `ai-rules-starter/docs/mcp-safety.md`
- `ai-rules-starter-es/docs/operator-context-policy.md` desde `ai-rules-starter/docs/operator-context-policy.md`
- `ai-rules-starter-es/docs/pi-agent-setup.md` desde `ai-rules-starter/docs/pi-agent-setup.md`
- `ai-rules-starter-es/docs/skills-policy.md` desde `ai-rules-starter/docs/skills-policy.md`
- `ai-rules-starter-es/docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` desde `ai-rules-starter/docs/model-guides/gpt-5.5-prompting-for-ai-rules.md`
- `ai-rules-starter-es/starter-overlay/AGENTS.patch.es.md` desde `ai-rules-starter/starter-overlay/AGENTS.patch.md`

Antes de traducir `docs/convex-integration.md` y `docs/skills-policy.md`, confirma con lectura directa que son documentos Markdown humanos. Si alguno no lo es, no lo traduzcas y reporta el riesgo. Trata `docs/advanced-reference.md` como placeholder de lectura si su contenido actual sigue siendo placeholder.

No copies ni traduzcas `ai-rules-starter/examples/`, `ai-rules-starter/starter-overlay/.ai-rules/`, `.gitkeep` ni artifacts del smoke test, porque no hay evidencia limpia que los autorice para esta implementacion.

## Reglas De Traduccion

Traduce la prosa humana al espanol manteniendo el significado operativo. No cambies la semantica de reglas para agentes.

Preserva byte-for-byte cuando aparezcan:

- comandos shell como `git diff --numstat` y `pi --tools read,grep,find,ls --no-session`
- rutas y prefijos como `ai-rules-starter/`, `ai-rules-starter-es/`, `starter-overlay/`, `.ai-rules/`, `docs/`
- nombres de archivo como `AGENTS.md`, `AGENTS.patch.md`, `classifier.prompt.md`, `reading-planner.prompt.md`, `evidence-reader.prompt.md`, `final-prompt-builder.prompt.md`, `final_prompt.md`
- markers `<!-- AI_RULES_MANAGED_START -->` y `<!-- AI_RULES_MANAGED_END -->`
- stop/status labels `OUT_OF_SCOPE_FOR_THIS_PHASE`, `R1_EXCEEDED`, `R2_EXCEEDED`, `READ_LEAF_OVERSIZE`, `CONTEXT_LIMIT_EXCEEDED`, `HARNESS_SCOPE_EXCEEDED`, `HARNESS_INSUFFICIENT`, `STOP-SCOPE-ESCALATION-REQUIRED`, `RETURN_TO_READING_PLANNER`
- constantes `R1`, `R2`, `W`, `500`, `600`, `8%`, `10%`, `15%`

`AGENTS.patch.es.md` es lectura humana, no patch operativo. No crees `AGENTS.md` en la copia espanola y no indiques que la copia espanola se deba aplicar al repo destino.

## Verificacion

Ejecuta y reporta:

- `find ai-rules-starter-es -type f | sort`
- `git diff --check`
- `git diff --numstat -- ai-rules-starter-es`
- `git diff --numstat -- ai-rules-starter`
- `grep -R "Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino." ai-rules-starter-es`

Confirma que `ai-rules-starter/` no tiene cambios.

## Reporte Final

Reporta:

- status: `PASS`, `PASS_WITH_WARNINGS` o `FAIL`
- archivos creados
- diff numstat y total aproximado de lineas
- checks ejecutados y resultado
- confirmacion de que no se modifico `ai-rules-starter/`
- confirmacion de que no se usaron internet, dependencias ni tooling nuevo
- confirmacion de que no se declaro Pi enforcement probado
- riesgos, incluyendo placeholders, archivos inferidos por ruta y cualquier skip
