# GPT-5.5 Prompting for AI Rules

Indice operativo de la guia GPT-5.5 para AI Rules.

## Status

- **Status:** guia dividida en modulos de lectura corta.
- **Evidence level:** alto para capacidades y guias publicas citadas de
  GPT-5.5/Codex; medio para su traslado a AI Rules; bajo para inferencias sobre
  comportamiento interno no publicado.
- **Last researched:** 2026-05-21, segun el deep research local usado en B11a.
- **Scope:** documentacion operativa en espanol para humanos y agentes que usan
  AI Rules.
- **Sin cambio normativo intencional:** este split no cambia prompts,
  templates, `AGENTS.patch.md`, reglas del starter kit ni archivos de
  `ai-rules-starter-es/`.
- **Nota de fase:** esta guia no aplica cambios por si sola.

## Que es esta guia

Esta guia destila el research GPT-5.5 en reglas de lectura y handoff para AI
Rules. No copia el research completo y no reemplaza el prompt de fase, el repo
actual, las instrucciones activas del entorno ni `AGENTS.md`.

AI Rules v0.4 puede usarse como referencia conceptual. El prompt actual sigue
siendo la autoridad operativa y el repo actual sigue siendo la fuente de verdad.

## Orden de lectura recomendado

1. Leer [gpt-5.5/README.md](gpt-5.5/README.md) para orientacion general.
2. Leer [authority-evidence-stop.md](gpt-5.5/authority-evidence-stop.md) si hay
   dudas de autoridad, evidencia o stop codes.
3. Leer [role-rules.md](gpt-5.5/role-rules.md) si la fase depende de un rol.
4. Leer [final-prompt-and-codex.md](gpt-5.5/final-prompt-and-codex.md) antes de
   construir o ejecutar `final_prompt.md`.
5. Leer [context-output-diff.md](gpt-5.5/context-output-diff.md) para contexto,
   formato de salida, W efectivo, R1/R2 y medicion de diff.
6. Leer
   [anti-patterns-and-deferred-updates.md](gpt-5.5/anti-patterns-and-deferred-updates.md)
   para evitar errores del smoke test y recordar updates diferidos.

Regla de uso: leer solo el modulo necesario segun rol/fase, salvo que el prompt
actual pida una revision completa.

## Modulos

- [gpt-5.5/README.md](gpt-5.5/README.md): resumen ejecutivo, evidence level y
  guia de uso.
- [gpt-5.5/authority-evidence-stop.md](gpt-5.5/authority-evidence-stop.md):
  jerarquia de autoridad, evidence clean, labels y stop conditions.
- [gpt-5.5/role-rules.md](gpt-5.5/role-rules.md): reglas compactas por rol.
- [gpt-5.5/final-prompt-and-codex.md](gpt-5.5/final-prompt-and-codex.md):
  contrato de `final_prompt.md` e implementacion con Codex.
- [gpt-5.5/context-output-diff.md](gpt-5.5/context-output-diff.md): manejo de
  contexto, outputs, Structured Outputs, YAML v0.1, diff y R1/R2.
- [gpt-5.5/anti-patterns-and-deferred-updates.md](gpt-5.5/anti-patterns-and-deferred-updates.md):
  anti-patterns del smoke test y required updates diferidos.

## Not proven / NOT_PROVEN

- W efectivo exacto por superficie de Codex.
- Paridad interna entre CLI, web, app e IDE.
- Pi Agent como lector real.
- Skills reales.
- Metrica publica oficial de alucinacion en tareas de software engineering para
  GPT-5.5/Codex.
- Adopcion completa de AI Rules en un repo de codigo productivo.

## Required updates deferred

No aplicar desde esta guia. Siguen diferidos para fases autorizadas:

- JSON schemas / Structured Outputs.
- Parser de stop codes.
- Ledger de calibracion de diff.
- Pi validation.
- Skills.
- Plantillas oficiales endurecidas.
- Cambios de prompts, templates o `AGENTS.patch.md`.
