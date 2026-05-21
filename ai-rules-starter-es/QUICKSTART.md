Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Inicio Rápido

Esta guía instala el starter overlay en un repo existente.

## 1. Copia El Overlay

Copia el overlay al repo destino desde el punto de partida que estés usando:

```text
# From inside ai-rules-starter/
starter-overlay/.ai-rules -> .ai-rules

# From the repo root containing ai-rules-starter/
ai-rules-starter/starter-overlay/.ai-rules -> .ai-rules
```

Mantén `.ai-rules/` committeado para que packets, prompts, checklists y entradas del ledger sean visibles para revisores.

Copia también `docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` al repo
destino en la misma ruta. El final prompt builder debe leerlo antes de
crear `final_prompt.md`.

Lee `docs/operator-context-policy.md` antes de tareas largas. Cuando el operador tenga
mediciones visibles de contexto, regístralas con
`.ai-rules/templates/operator_context_log.yaml`.

## 2. Parchea AGENTS.md

Abre `starter-overlay/AGENTS.patch.md` desde dentro de `ai-rules-starter/`, o `ai-rules-starter/starter-overlay/AGENTS.patch.md` desde la raíz del repo, e inserta su bloque gestionado en el `AGENTS.md` del repo destino.

Si el repo destino tiene reglas de agente existentes, añade el bloque de AI Rules sin borrar las reglas locales.

## 3. Ejecuta Una Primera Tarea Manual

Usa esta secuencia manual:

1. Escribe la solicitud humana en `.ai-rules/packets/`.
2. Usa `classifier.prompt.md` para clasificar el alcance.
3. Usa `reading-planner.prompt.md` para elegir archivos a inspeccionar.
4. Usa `evidence-reader.prompt.md` para recopilar evidencia.
5. Opcional: usa Pi solo lectura con `pi --tools read,grep,find,ls --no-session`
   solo para evidencia del planner/reader.
6. Usa `final-prompt-builder.prompt.md` para producir el prompt del implementador.
7. Usa una instancia fresca antes de una fase nueva si la sesión está alta.
8. Entrega solo ese final prompt a Codex.
9. Registra el resultado y los checks en `.ai-rules/ledger/`.

No añadas automatización todavía.
