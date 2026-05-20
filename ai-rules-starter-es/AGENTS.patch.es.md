Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

Nota operativa: este archivo es traducción de lectura. El patch operativo real es `ai-rules-starter/starter-overlay/AGENTS.patch.md`.

# Parche de AGENTS.md

Inserta este bloque administrado en el `AGENTS.md` del repo destino.

<!-- AI_RULES_MANAGED_START -->
## AI Rules

- Usa `.ai-rules/` para artefactos de AI Rules: packets, prompts, checklists y entradas de ledger.
- Codex actúa como un implementador acotado.
- Implementa sólo desde el prompt final producido por el flujo de AI Rules.
- AI Rules v0.4 o cualquier documento de especificación completa es sólo material de referencia.
- El prompt de fase actual es la autoridad operativa.
- Si los docs de referencia sugieren trabajo extra fuera de los archivos/acciones permitidos, reporta `OUT_OF_SCOPE_FOR_THIS_PHASE`.
- R1: Los archivos productivos nuevos deben ser <= 500 líneas.
- R1: Los archivos productivos existentes ya > 500 líneas no deben crecer sin autorización explícita.
- R2: El diff total de hojas ejecutables debe ser <= 600 líneas, calculado como agregadas + eliminadas desde `git diff --numstat`.
- Si R1 o R2 se viola, reporta `R1_EXCEEDED` o `R2_EXCEEDED`.
- No inventes arquitectura, tooling, dependencias ni abstracciones fuera del prompt.
- Detente si falta evidencia requerida o las restricciones entran en conflicto.
- Antes del reporte final, mide el diff con `git diff --numstat`.
- Reporta archivos cambiados, checks ejecutados, riesgos y cualquier validación omitida.
<!-- AI_RULES_MANAGED_END -->
