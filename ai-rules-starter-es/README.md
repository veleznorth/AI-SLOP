Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

Nota de alcance: `ai-rules-starter-es/` no se copia al repo destino y no contiene el overlay operativo. El overlay operativo vive en `ai-rules-starter/starter-overlay/`.

# AI Rules Starter Kit

AI Rules Starter Kit es un esqueleto mínimo de repo para adoptar AI Rules en un proyecto de software.

AI Rules es una capa operativa para coordinar varias instancias de IA en trabajo de ingeniería. No es una biblioteca runtime, paquete, dependencia de framework ni generador de código. Su trabajo es convertir una solicitud humana en artefactos auditables antes de que empiece una implementación con IA.

Flujo mínimo:

```text
request -> classify -> reading plan -> evidence -> final prompt -> Codex -> report
```

En este starter, Codex es el implementador acotado. Codex debe implementar sólo desde el prompt final y debe reportar archivos cambiados, checks y riesgos.

## Estructura del repo

```text
ai-rules-starter/
  README.md
  QUICKSTART.md
  GUIDE_BASIC.md
  FUNDAMENTALS.md
  docs/
  starter-overlay/
    AGENTS.patch.md
    .ai-rules/
      prompts/
      templates/
      checklists/
      packets/
      ledger/
  examples/
    first-task/
```

Usa `starter-overlay/` como el conjunto pequeño de archivos que se copian a un repo destino.
