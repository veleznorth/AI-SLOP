# GPT-5.5 para AI Rules

## Proposito

Este modulo resume como usar GPT-5.5/Codex dentro de AI Rules sin ampliar el
scope del starter kit. La guia esta pensada para reducir friccion de lectura:
cada rol debe abrir solo la pieza que necesita para ejecutar su fase.

## Evidence level

- **Alto:** capacidades y guias publicas citadas de GPT-5.5, Codex, Structured
  Outputs, token counting, compaction, AGENTS.md, sandboxing y workflows.
- **Medio:** traslado de esas guias al diseno operativo de AI Rules.
- **Bajo:** inferencias sobre comportamiento interno no publicado, W efectivo
  exacto, paridad entre superficies y Pi Agent real.

`NOT_PROVEN` se mantiene para W efectivo exacto, Pi Agent, skills reales,
paridad CLI/web/app/IDE y adopcion completa en un repo productivo.

## Como usar los modulos

- Si hay duda de autoridad o evidencia, leer
  [authority-evidence-stop.md](authority-evidence-stop.md).
- Si hay duda de rol, leer [role-rules.md](role-rules.md).
- Si se construye o ejecuta `final_prompt.md`, leer
  [final-prompt-and-codex.md](final-prompt-and-codex.md).
- Si la duda es contexto, formato, diff o R1/R2, leer
  [context-output-diff.md](context-output-diff.md).
- Si la fase toca deuda conocida, leer
  [anti-patterns-and-deferred-updates.md](anti-patterns-and-deferred-updates.md).

Regla operativa: leer solo el modulo necesario segun rol/fase. No convertir esta
guia en background obligatorio completo si el prompt actual ya delimita la fase.

## Referencias clave preservadas

- [OpenAI GPT-5.5 model page](https://platform.openai.com/docs/models/gpt-5.5)
- [Using GPT-5.5](https://developers.openai.com/api/docs/guides/latest-model)
- [GPT-5.5 System Card](https://deploymentsafety.openai.com/gpt-5-5)
- [Codex Prompting](https://developers.openai.com/codex/prompting)
- [Codex Best Practices](https://developers.openai.com/codex/learn/best-practices)
- [AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md)
- [Codex Workflows](https://developers.openai.com/codex/workflows)
- [Codex Sandboxing](https://developers.openai.com/codex/concepts/sandboxing)
- [Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
- [Prompt Guidance](https://developers.openai.com/api/docs/guides/prompt-guidance?model=gpt-5.5)
- [Compaction](https://developers.openai.com/api/docs/guides/compaction)
- [Token Counting](https://developers.openai.com/api/docs/guides/token-counting)
- [Optimizing LLM Accuracy](https://developers.openai.com/api/docs/guides/optimizing-llm-accuracy)
- [Agent Evals](https://developers.openai.com/api/docs/guides/agent-evals)
- [Lost in the Middle](https://arxiv.org/abs/2307.03172)
