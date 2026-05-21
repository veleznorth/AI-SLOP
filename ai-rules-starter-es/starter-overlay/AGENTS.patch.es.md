Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Lectura De AGENTS.md Patch

Este archivo es una copia de lectura humana del bloque gestionado de `AGENTS.patch.md`; no es un patch operativo para aplicar al repo destino.

<!-- AI_RULES_MANAGED_START -->
## AI Rules

- Usa `.ai-rules/` para artifacts de AI Rules: packets, prompts, checklists y entradas del ledger.
- Codex actúa como implementador acotado.
- Implementa solo desde el final prompt producido por el flujo de AI Rules.
- AI Rules v0.4 o cualquier documento de especificación completa es solo material de referencia.
- El prompt de fase actual es la autoridad operativa.
- Si los docs de referencia sugieren trabajo extra fuera de los archivos/acciones permitidos, reporta `OUT_OF_SCOPE_FOR_THIS_PHASE`.
- R1: Los archivos productivos nuevos deben ser <= 500 líneas.
- R1: Los archivos productivos existentes ya > 500 líneas no deben crecer sin autorización explícita.
- R2: El diff total de la executable leaf debe ser <= 600 líneas, calculado como añadidas + eliminadas desde `git diff --numstat`.
- Si R1 o R2 se viola, reporta `R1_EXCEEDED` o `R2_EXCEEDED`.
- El operador humano controla el monitoreo de contexto a nivel de sesión.
- los agentes no deben inventar porcentajes de W/contexto.
- Las reading leaves sobre 15% W deben enrutarse de vuelta al Reading Planner para división.
- No inventes arquitectura, tooling, dependencias ni abstracciones fuera del prompt.
- Detente si falta evidencia requerida o si las restricciones entran en conflicto.
- El final prompt builder debe consultar `docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` antes de producir `final_prompt.md`.
- Antes del reporte final, mide el diff con `git diff --numstat`.
- Reporta archivos cambiados, checks ejecutados, riesgos y cualquier validación omitida.
<!-- AI_RULES_MANAGED_END -->
