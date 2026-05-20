Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Inicio rápido

Esta guía instala el starter overlay en un repo existente.

Aunque estás leyendo la versión en español, ejecuta la instalación desde `ai-rules-starter/`. No copies archivos desde ai-rules-starter-es/. Usa `ai-rules-starter/starter-overlay/.ai-rules` como fuente real del overlay.

## 1. Copia el overlay

Copia el overlay al repo destino desde el punto de partida que estés usando:

```text
# From inside ai-rules-starter/
starter-overlay/.ai-rules -> .ai-rules

# From the repo root containing ai-rules-starter/
ai-rules-starter/starter-overlay/.ai-rules -> .ai-rules
```

Mantén `.ai-rules/` commiteado para que los paquetes, prompts, checklists y entradas del ledger sean visibles para revisores.

Lee `docs/operator-context-policy.md` antes de tareas largas. Cuando el
operador tenga mediciones visibles de contexto, registralas con
`.ai-rules/templates/operator_context_log.yaml`.

## 2. Parchea AGENTS.md

Abre `starter-overlay/AGENTS.patch.md` desde dentro de `ai-rules-starter/`, o `ai-rules-starter/starter-overlay/AGENTS.patch.md` desde la raíz del repo, e inserta su bloque administrado en el `AGENTS.md` del repo destino.

Si el repo destino ya tiene reglas de agentes, agrega el bloque de AI Rules sin borrar las reglas locales.

## 3. Ejecuta una primera tarea manual

Usa esta secuencia manual:

1. Escribe la solicitud humana en `.ai-rules/packets/`.
2. Usa `classifier.prompt.md` para clasificar el alcance.
3. Usa `reading-planner.prompt.md` para elegir los archivos que se deben inspeccionar.
4. Usa `evidence-reader.prompt.md` para recopilar evidencia.
5. Opcional: usa Pi de sólo lectura con `pi --tools read,grep,find,ls --no-session`
   sólo para evidencia del planner/reader.
6. Usa `final-prompt-builder.prompt.md` para producir el prompt del implementador.
7. Usa una instancia fresca antes de una fase nueva si la sesion actual esta alta.
8. Entrega sólo ese prompt final a Codex.
9. Registra el resultado y los checks en `.ai-rules/ledger/`.

No agregues automatización todavía.
