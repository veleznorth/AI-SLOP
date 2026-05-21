Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Configuración De Codex

Usa Codex como el implementador acotado.

Expectativa mínima:

- Recibir un final prompt, no una solicitud cruda.
- Preservar cambios no relacionados en el worktree.
- Evitar inventar arquitectura.
- Ejecutar los checks solicitados cuando estén disponibles.
- Reportar archivos cambiados, checks, riesgos y tamaño del diff.
