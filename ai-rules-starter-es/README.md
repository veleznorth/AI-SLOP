Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# AI Rules Starter Kit

AI Rules Starter Kit es un esqueleto mínimo de repo para adoptar AI Rules en un proyecto de software.

AI Rules es una capa operativa para coordinar varias instancias de IA en trabajo de ingeniería. No es una biblioteca runtime, paquete, dependencia de framework ni generador de código. Su trabajo es convertir una solicitud humana en artifacts auditables antes de que empiece una implementación de IA.

Flujo mínimo:

```text
request -> classify -> reading plan -> evidence -> final prompt -> Codex -> report
```

En este starter, Codex es el implementador acotado. Codex debe implementar solo desde el final prompt y debe reportar archivos cambiados, checks y riesgos.

## Estructura Del Repo

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

Usa `starter-overlay/` como el conjunto pequeño de archivos copiados a un repo destino.
