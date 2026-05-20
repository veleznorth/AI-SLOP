Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Setup de Codex

Usa Codex como el implementador acotado.

Expectativa mínima:

- Recibir un prompt final, no una solicitud cruda.
- Preservar cambios no relacionados en el worktree.
- Evitar invención de arquitectura.
- Ejecutar los checks pedidos cuando estén disponibles.
- Reportar archivos cambiados, checks, riesgos y tamaño del diff.
